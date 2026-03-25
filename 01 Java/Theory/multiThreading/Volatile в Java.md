> `volatile` — це **ключове слово**, яке гарантує що змінна завжди читається з **основної пам'яті** (RAM), а не з локального кешу процесора.

---

> [!abstract] Core Framework 
> **`volatile` вирішує дві проблеми багатопотоковості:**
> 
> - **Visibility** — зміна змінної одним потоком одразу видима всім іншим
> - **Reordering** — забороняє процесору міняти порядок інструкцій навколо змінної

---

> [!info] Key Insights
> 
> - **Кеш процесора.** Кожен потік має свій локальний кеш. Без `volatile` потік може читати застаріле значення зі свого кешу, не бачачи змін іншого потоку.
> - **Memory barrier.** `volatile` встановлює бар'єр — всі записи до `volatile`-змінної одразу "прошиваються" в основну пам'ять, всі читання — напряму з неї.
> - **Не замінює `synchronized`.** `volatile` гарантує видимість, але **не атомарність**. `count++` з `volatile` все одно не є потокобезпечним — це три окремі операції (read → increment → write).
> - **Reordering.** JVM та процесор оптимізують код, міняючи порядок інструкцій. `volatile` забороняє це навколо себе.

---

> [!example] Практичне застосування
> 
> **Без volatile — потік може зависнути назавжди:**
> 
> java
> 
> ```java
> public class Task12 {
>     private static boolean isRunning = true; /* без volatile — читається з кешу */
> 
>     public static void main(String[] args) throws InterruptedException {
>         new Thread(() -> {
>             while (isRunning) {} /* крутиться у своєму кеші, не бачить зміни */
>             System.out.println("Завершився");
>         }).start();
> 
>         Thread.sleep(1000);
>         isRunning = false; /* записали в RAM, але потік читає з кешу */
>     }
> }
> ```
> 
> **З volatile — зміна одразу видима:**
> 
> java
> 
> ```java
> public class Task12 {
>     private volatile static boolean isRunning = true; /* завжди з RAM */
> 
>     public static void main(String[] args) throws InterruptedException {
>         new Thread(() -> {
>             while (isRunning) {} /* кожна ітерація читає з основної пам'яті */
>             System.out.println("Завершився"); /* побачить зміну і зупиниться */
>         }).start();
> 
>         Thread.sleep(1000);
>         isRunning = false; /* одразу видимо всім потокам */
>     }
> }
> ```
> 
> **Singleton з double-checked locking — навіщо volatile:**
> 
> java
> 
> ```java
> static class Singleton {
>     private static volatile Singleton instance; /* volatile — обов'язково */
> 
>     private Singleton() {}
> 
>     public static Singleton getInstance() {
>         if (instance == null) {               /* перша перевірка — без lock */
>             synchronized (Singleton.class) {
>                 if (instance == null) {        /* друга перевірка — з lock */
>                     instance = new Singleton();
>                     /*
>                      * Без volatile JVM може переставити кроки:
>                      * 1. виділити пам'ять
>                      * 2. записати адресу в instance  ← instance вже не null!
>                      * 3. ініціалізувати об'єкт       ← але об'єкт ще порожній
>                      *
>                      * Інший потік побачить instance != null
>                      * і отримає неініціалізований об'єкт 💥
>                      *
>                      * З volatile — порядок завжди 1 → 3 → 2
>                      */
>                 }
>             }
>         }
>         return instance;
>     }
> }
> ```

---

> [!warning] Обмеження та ризики
> 
> - **Не атомарність.** `volatile int count; count++` — не потокобезпечно. `count++` це три операції: read → increment → write. Два потоки можуть прочитати одне значення одночасно. Використовуй `AtomicInteger`.
> - **Reordering без volatile.** `instance = new Singleton()` складається з 3 кроків. Без `volatile` процесор може виконати їх не по порядку — інший потік отримає посилання на неініціалізований об'єкт.
> - **Залежність від JVM.** Зависання потоку без `volatile` проявляється на **server JVM** з агресивною оптимізацією. На client JVM або в debug-режимі баг може не виявитись роками, а потім впасти на продакшені.

---

> [!tip] Коли використовувати
> 
> **Так** ✅:
> 
> - Один потік пише, інші тільки читають (прапор `isRunning`)
> - Double-checked locking у Singleton
> - Статус/стан який змінюється рідко
> 
> **Ні** ❌:
> 
> - Потрібна атомарність (`count++`) → використовуй `AtomicInteger`
> - Складна логіка з кількома змінними → використовуй `synchronized`
> - Потрібна транзакційність → використовуй `Lock`

---
# Connections:
- [[Багатопотоковість, Процеси та Потоки в Java]]
- [[Проблеми багатопотоковості (Race Condition та Deadlock)]]