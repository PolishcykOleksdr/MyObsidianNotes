> **`Executors`** — це фабричний клас, що надає статичні методи для створення різних реалізацій `ExecutorService`. Кожен тип пулу оптимізований під конкретний сценарій навантаження та керує lifecycle потоків автоматично.

---

> [!abstract] Core Framework
> 
> **Чотири основні типи пулів потоків:**
> 
> - `newFixedThreadPool(n)` — фіксована кількість потоків, необмежена черга
> - `newCachedThreadPool()` — динамічна кількість потоків, кешування 60 сек
> - `newSingleThreadExecutor()` — рівно 1 потік з гарантією FIFO
> - `newScheduledThreadPool(n)` — фіксований пул з підтримкою планування
> 
> **`pool.submit(task)`** — кладе завдання в чергу, обгортає у `FutureTask` і повертає `Future<T>` для отримання результату або контролю виконання.

---

> [!info] Key Insights
> 
> - **FixedThreadPool vs Semaphore.** `FixedThreadPool` контролює **кількість потоків** (потоки належать пулу, завдання чекають потоку). `Semaphore` контролює **доступ до ресурсу** (потоки вже існують, Semaphore лише каже "стій/іди").
> - **submit() vs execute().** `execute()` — "fire and forget", без результату. `submit()` обгортає задачу у `FutureTask`, передає в `execute()` і повертає `Future`.
> - **Винятки у submit().** Виняток **не вилітає одразу** — він захований у `Future`. Вилетить лише при виклику `future.get()` як `ExecutionException`. У `execute()` — навпаки, виняток одразу йде в `UncaughtExceptionHandler`.
> - **newSingleThreadExecutor() ≠ newFixedThreadPool(1).** У `SingleThreadExecutor` потік **автоматично відновлюється** після помилки, у `FixedThreadPool(1)` — ні.
> - **Необмежена черга — прихована небезпека.** `Fixed` і `Single` використовують `LinkedBlockingQueue` без ліміту → при великому навантаженні можливе переповнення пам'яті.

---

> [!info] **Thread Pools**
> ## 1. `newFixedThreadPool(int nThreads)`
> 
> Фіксована кількість потоків. Якщо всі зайняті — завдання чекають у черзі (`LinkedBlockingQueue`, необмежена).
> 
> java
> 
> ```java
> ExecutorService pool = Executors.newFixedThreadPool(4);
> /* Потоки живуть весь час існування пулу.
>    Черга необмежена → може переповнитись пам'ять при великому навантаженні.
>    Підходить, коли знаєш скільки паралельних завдань допустимо. */
> ```
> 
> 
> ## 2. `newCachedThreadPool()`
> 
> Потоки створюються на вимогу, вільні потоки кешуються **60 секунд**, потім знищуються. Черга `SynchronousQueue` (без буфера).
> 
> java
> 
> ```java
> ExecutorService pool = Executors.newCachedThreadPool();
> /* Кількість потоків необмежена → ризик OutOfMemoryError при спалаху задач.
>    Ефективний при короткочасних задачах.
>    Автоматично масштабується. */
> ```
> 
> 
> ## 3. `newSingleThreadExecutor()`
> 
> Рівно **1 потік**. Якщо він впав — автоматично створюється новий. Черга необмежена, задачі виконуються послідовно.
> 
> java
> 
> ```java
> ExecutorService pool = Executors.newSingleThreadExecutor();
> /* Гарантує порядок виконання (FIFO).
>    Потокобезпечний доступ до ресурсів без синхронізації.
>    Відрізняється від newFixedThreadPool(1) — тут потік відновлюється після помилки. */
> ```
> 
> 
> ## 4. `newScheduledThreadPool(int corePoolSize)`
> 
> Фіксований пул потоків з підтримкою відкладеного та повторюваного виконання.
> 
> java
> 
> ```java
> ScheduledExecutorService pool = Executors.newScheduledThreadPool(2);
> 
> /* Виконати один раз через 5 секунд */
> pool.schedule(task, 5, TimeUnit.SECONDS);
> 
> /* Виконувати кожні 10 секунд */
> pool.scheduleAtFixedRate(task, 0, 10, TimeUnit.SECONDS);
> 
> /* Виконувати через 10 секунд після завершення попереднього */
> pool.scheduleWithFixedDelay(task, 0, 10, TimeUnit.SECONDS);
> ```

---

> [!important] Різниця між Semaphore та FixedThreadPool
> ## `Semaphore` vs `FixedThreadPool`
> 
> java
> 
> ```java
> Semaphore semaphore = new Semaphore(4);
> 
> /* Потік сам існує, просто чекає дозволу */
> executor.submit(() -> {
>     semaphore.acquire();        /* чекає "слот" */
>     try {
>         doWork();
>     } finally {
>         semaphore.release();    /* звільняє "слот" */
>     }
> });
> ```
> 
> java
> 
> ```java
> ExecutorService pool = Executors.newFixedThreadPool(4);
> 
> /* Потоки створює і керує ними сам пул */
> pool.submit(() -> doWork());
> /* Потоки не існують до submit (або створюються при старті).
>    Черга завдань вбудована, ти про неї не думаєш.
>    Керує lifecycle потоків (створення, reuse, знищення). */
> ```
> 

---

> [!important] Що відбувається у `pool.submit()`
> 
> java
> 
> ```java
> /* execute() — "fire and forget", немає результату */
> pool.execute(() -> doWork());
> 
> /* submit() — повертає Future, можна отримати результат або зловити виняток */
> Future<String> future = pool.submit(() -> "result");
> String result = future.get(); /* блокує поточний потік до завершення */
> ```
> 
> java
> 
> ```java
> /* Три перевантаження submit() */
> 
> /* 1. Callable<T> — повертає результат */
> Future<String> f1 = pool.submit(() -> "hello");
> 
> /* 2. Runnable + result — Runnable не має результату, але можна передати його явно */
> Future<String> f2 = pool.submit(() -> doWork(), "done");
> 
> /* 3. Runnable без результату — Future тільки для контролю завершення */
> Future<?> f3 = pool.submit(() -> doWork());
> /* f3.get() поверне null */
> ```
> 
> java
> 
> ```java
> /* Future — що можна робити */
> Future<Integer> future = pool.submit(() -> {
>     Thread.sleep(2000);
>     return 42;
> });
> 
> future.isDone();        /* false — ще виконується */
> future.cancel(true);    /* скасувати (якщо ще не почалось) */
> future.get();           /* чекати результат (блокує!) */
> future.get(1, TimeUnit.SECONDS); /* чекати максимум 1 сек → TimeoutException */
> ```
> 
> java
> 
> ```java
> /* Виняток НЕ вилітає одразу — він захований у Future! */
> Future<?> future = pool.submit(() -> {
>     throw new RuntimeException("щось пішло не так");
> });
> 
> future.get(); /* ось тут вилетить ExecutionException */
> ```
> 

---

> [!example] Практичне застосування
> 
> **Продакшн-конфігурація через `ThreadPoolExecutor` напряму:**
> 
> java
> 
> ```java
> new ThreadPoolExecutor(
>     4,                          /* corePoolSize */
>     8,                          /* maximumPoolSize */
>     60L, TimeUnit.SECONDS,      /* keepAliveTime */
>     new ArrayBlockingQueue<>(100), /* обмежена черга! */
>     new ThreadFactoryBuilder().setNameFormat("worker-%d").build(),
>     new CallerRunsPolicy()      /* політика при переповненні */
> );
> ```
> 
> **Комбінування `FixedThreadPool` + `Semaphore`:**
> 
> java
> 
> ```java
> ExecutorService pool = Executors.newFixedThreadPool(8);
> Semaphore dbSemaphore = new Semaphore(3); /* не більше 3 запитів до БД одночасно */
> 
> pool.submit(() -> {
>     dbSemaphore.acquire();
>     try {
>         queryDatabase();
>     } finally {
>         dbSemaphore.release();
>     }
> });
> /* FixedThreadPool обмежує кількість потоків,
>    Semaphore всередині обмежує доступ до БД */
> ```

---

> [!warning] Обмеження та ризики
> 
> - **Необмежена черга у Fixed/Single.** `LinkedBlockingQueue` без ліміту → `OutOfMemoryError` при накопиченні мільйонів задач.
> - **CachedThreadPool під навантаженням.** Необмежена кількість потоків → `OutOfMemoryError` при різкому спалаху задач.
> - **Приховані винятки у submit().** Якщо не викликати `future.get()` — виняток буде мовчки проковтнутий, задача просто "зникне".
> - **future.get() блокує.** Виклик без таймауту може заблокувати поточний потік назавжди при зависанні задачі.

---

> [!tip] Коли використовувати
> 
> **`newFixedThreadPool`** ✅:
> 
> - CPU-інтенсивні задачі
> - Веб-сервер з обмеженим паралелізмом
> - Обробка файлів батчами
> 
> **`newCachedThreadPool`** ✅:
> 
> - Багато короткочасних асинхронних задач
> - I/O-bound операції з непередбачуваним навантаженням
> 
> **`newSingleThreadExecutor`** ✅:
> 
> - Запис у файл/БД з одного потоку
> - Послідовна обробка подій, event loop
> 
> **`newScheduledThreadPool`** ✅:
> 
> - Cron-подібні задачі, health checks
> - Periodic cleanup, retry з затримкою
> 
> **`ThreadPoolExecutor` напряму** ✅:
> 
> - Продакшн-код де потрібен повний контроль над чергою, політикою відхилення та іменуванням потоків

---
# Connections:
- [[Синхронізація потоків у Java]]
- [[Синхронізатори Java. Частина 1]]
- [[Синхронізатори Java. Частина 2]]
- [[Багатопотоковість, Процеси та Потоки в Java]]
- [[Об'єкти блокування (Lock Objects)]]
- [[Callable та Future — асинхронний результат у Java]]