> **`Callable<V>`** — функціональний інтерфейс-задача, що **повертає результат** типу `V` і може кидати **checked exception**. **`Future<V>`** — квиток на результат, що дозволяє **отримати, скасувати або перевірити стан** асинхронно виконуваної задачі ззовні потоку.

---

> [!abstract] Core Framework
> 
> **Callable і Future вирішують головну проблему `Runnable` — відсутність результату:**
> 
> - `Runnable.run()` — `void`, не може кидати checked exception
> - `Callable.call()` — повертає `V`, може кидати `Exception`
> - `Future<V>` — міст між потоком-виконавцем і потоком-замовником
> 
> **Ролі:**
> 
> - `Callable` — **всередині** потоку, описує що зробити і що повернути
> - `Future` — **зовні** потоку, дає доступ до результату задачі

---

> [!info] Key Insights
> 
> - **Callable ≠ Future.** Вони не конкурують — працюють у парі. `Callable` описує задачу, `Future` — спосіб отримати її результат.
> - **Future повертається одразу.** `pool.submit(callable)` не чекає виконання — повертає `Future` миттєво. Реальний результат з'являється пізніше.
> - **Виняток захований у Future.** Якщо `Callable` кинув виняток — він не вилітає одразу. Вилетить як `ExecutionException` тільки при виклику `future.get()`.
> - **`get()` блокує поточний потік.** Виклик `future.get()` без таймауту може заблокувати `main`-потік назавжди якщо задача зависне.
> - **`cancel(true)` не гарантує зупинку.** Він надсилає `interrupt` потоку, але якщо задача не перевіряє `Thread.isInterrupted()` — вона продовжить виконання.
> - **`InterruptedException` треба обробляти правильно.** Завжди викликати `Thread.currentThread().interrupt()` після перехоплення, щоб відновити прапор переривання.

---

> [!example] Практичне застосування
> 
> **Callable — оголошення задачі:**
> 
> java
> 
> ```java
> /* Інтерфейс Callable має лише один метод */
> @FunctionalInterface
> public interface Callable<V> {
>     V call() throws Exception; /* повертає V, може кидати checked exception */
> }
> ```
> 
> **Порівняння Runnable vs Callable:**
> 
> java
> 
> ```java
> /* Runnable — void, без checked exceptions */
> Runnable runnable = () -> System.out.println("done");
> 
> /* Callable — повертає результат, може кидати Exception */
> Callable<Integer> callable = () -> {
>     Thread.sleep(1000); /* checked exception — ОК! */
>     return 42;          /* повертає результат */
> };
> ```
> 
> **Базовий сценарій submit + get:**
> 
> java
> 
> ```java
> ExecutorService pool = Executors.newFixedThreadPool(2);
> 
> /* submit() повертає Future ОДРАЗУ — задача ще виконується */
> Future<Integer> future = pool.submit(() -> {
>     Thread.sleep(2000);
>     return 42;
> });
> 
> /* Тут можна робити щось інше поки задача виконується */
> System.out.println("Задача ще виконується...");
> 
> /* get() блокує поточний потік поки не буде результату */
> Integer result = future.get(); /* чекаємо... отримуємо 42 */
> ```

---

> [!Info] **Методи Future**
> 
> ## `get()`
> 
> Чекає результат, **блокує** поточний потік до завершення задачі.
> 
> java
> 
> ```java
> Future<Integer> future = pool.submit(() -> {
>     Thread.sleep(3000);
>     return 42;
> });
> 
> Integer result = future.get(); /* блокує поточний потік */
> ```
> 
> 
> ## `get(timeout, unit)`
> 
> Чекає максимум N часу, кидає `TimeoutException` якщо не встигло.
> 
> java
> 
> ```java
> try {
>     Integer result = future.get(1, TimeUnit.SECONDS); /* чекає 1 сек */
> } catch (TimeoutException e) {
>     System.out.println("Не дочекались результату");
> }
> ```
> 
> 
> ## `isDone()`
> 
> Перевіряє чи завершилась задача. **Не блокує.**
> 
> java
> 
> ```java
> if (future.isDone()) {
>     System.out.println("Готово: " + future.get());
> } else {
>     System.out.println("Ще виконується...");
> }
> ```
> 
> 
> ## `cancel(mayInterruptIfRunning)`
> 
> Скасовує задачу.
> 
> java
> 
> ```java
> boolean cancelled = future.cancel(true);
> /* true  — перервати навіть якщо вже виконується (шле interrupt) */
> /* false — скасувати тільки якщо ще не почалась */
> ```
> 
> 
> ## `isCancelled()`
> 
> Перевіряє чи була задача скасована.
> 
> java
> 
> ```java
> if (future.isCancelled()) {
>     System.out.println("Задача скасована");
> }
> ```

---

> [!important] **Три перевантаження `submit()`**
> 
> java
> 
> ```java
> /* 1. Callable<T> — є результат */
> Future<String> f1 = pool.submit(() -> "hello");
> String s = f1.get(); /* "hello" */
> 
> /* 2. Runnable + явний результат */
> Future<String> f2 = pool.submit(() -> doWork(), "done");
> String s2 = f2.get(); /* "done" — результат який передали, не з Runnable */
> 
> /* 3. Runnable без результату — Future тільки для контролю завершення */
> Future<?> f3 = pool.submit(() -> doWork());
> Object result = f3.get(); /* null */
> ```

---

> [!info] **Обробка винятків**
> 
> java
> 
> ```java
> /* Callable кинув exception — він ховається всередині Future */
> Future<?> future = pool.submit(() -> {
>     throw new RuntimeException("щось пішло не так");
> });
> 
> /* Виняток НЕ вилітає одразу! Вилітає тільки при get() */
> try {
>     future.get();
> } catch (ExecutionException e) {
>     Throwable cause = e.getCause(); /* дістаємо оригінальний виняток */
>     System.out.println("Причина: " + cause.getMessage()); /* "щось пішло не так" */
> } catch (InterruptedException e) {
>     Thread.currentThread().interrupt(); /* відновлюємо прапор! */
> }
> ```

---

> [!info] **Паралельний збір результатів**
> 
> java
> 
> ```java
> ExecutorService pool = Executors.newFixedThreadPool(3);
> 
> /* Запускаємо 3 задачі паралельно */
> Future<Integer> f1 = pool.submit(() -> { Thread.sleep(1000); return 1; });
> Future<Integer> f2 = pool.submit(() -> { Thread.sleep(2000); return 2; });
> Future<Integer> f3 = pool.submit(() -> { Thread.sleep(500);  return 3; });
> 
> /* Всі три виконуються ПАРАЛЕЛЬНО поки ми тут */
> System.out.println("Задачі запущені, чекаємо...");
> 
> /* Збираємо результати */
> try {
>     System.out.println("f1 = " + f1.get()); /* чекає ~1 сек */
>     System.out.println("f2 = " + f2.get()); /* вже майже готово */
>     System.out.println("f3 = " + f3.get()); /* вже давно готово */
> } catch (ExecutionException | InterruptedException e) {
>     Thread.currentThread().interrupt();
> }
> 
> pool.shutdown();
> /* Загальний час: ~2 сек (паралельно), не 3.5 сек (послідовно) */
> ```

---

> [!warning] Обмеження та ризики
> 
> - **`get()` без таймауту — небезпечно.** Якщо задача зависне — `main`-потік заблокується назавжди. Завжди використовуй `get(timeout, unit)` у продакшн-коді.
> - **Виняток мовчки зникає.** Якщо викликати `submit()` і ніколи не викликати `future.get()` — виняток всередині задачі буде проковтнутий без жодного сигналу.
> - **`cancel(true)` не гарантує зупинку.** Надсилає `interrupt`, але задача може його ігнорувати якщо не перевіряє `Thread.isInterrupted()`.
> - **`InterruptedException` — не ігнорувати.** Завжди викликати `Thread.currentThread().interrupt()` після перехоплення.

---

> [!tip] Коли використовувати
> 
> **`Callable` + `Future`** ✅:
> 
> - Потрібен результат з асинхронної задачі
> - Потрібно зловити checked exception з потоку
> - Потрібен контроль над виконанням (скасування, таймаут)
> - Паралельний збір результатів з кількох задач
> 
> **`Runnable` + `execute()`** ✅:
> 
> - Результат не потрібен ("fire and forget")
> - Максимально проста задача без винятків

---
# Connections:
- [[Багатопотоковість, Процеси та Потоки в Java]]
- [[Об'єкти блокування (Lock Objects)]]