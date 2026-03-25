> `Condition` — це механізм **координації між потоками**, що дозволяє одному потоку **відпустити lock і заснути**, а іншому — **його розбудити** в потрібний момент.

---

> [!abstract] Core Framework 
> **Condition — точка очікування, прив'язана до конкретного `ReentrantLock`. Дозволяє потокам:**
> 
> - засинати і атомарно відпускати lock (`await()`)
> - прокидатися за сигналом іншого потоку (`signal()` / `signalAll()`)
> - очікувати з таймаутом (`await(time, unit)`)

---

> [!info] Key Insights
> 
> - **Атомарність `await()`**. Відпускання lock і перехід у сон відбуваються як одна операція — між ними неможливо вставити інший потік.
> - **Два Condition — точний контроль**. Один Condition для producer-потоків (`notFull`), інший для consumer-потоків (`notEmpty`). `signal()` будить **тільки потрібну групу**, а не випадковий потік.
> - **`while`, не `if`**. Після пробудження умова перевіряється знову, бо існують **spurious wakeups** — потік може прокинутись без виклику `signal()` (особливість JVM/OS).
> - **Завжди у `try/finally`**. Lock має бути відпущений навіть якщо `await()` кинув `InterruptedException`.

---

> [!example] Практичне застосування — BoundedBuffer
> 
> java
> 
> ```java
> static class BoundedBuffer<T> {
>     private final Lock lock = new ReentrantLock();
>     private final Condition notFull  = lock.newCondition(); /* producer-потоки чекають тут */
>     private final Condition notEmpty = lock.newCondition(); /* consumer-потоки чекають тут */
> 
>     private final int maxLength;
>     private final Queue<T> queue = new ArrayDeque<>();
> 
>     public BoundedBuffer(int maxLength) {
>         this.maxLength = maxLength;
>     }
> 
>     public void put(T t) throws InterruptedException {
>         lock.lock();
>         try {
>             while (queue.size() >= maxLength) {
>                 notFull.await(); /* 1. відпускає lock, 2. засинає, 3. прокидається → знову захоплює lock */
>             }
>             queue.add(t);
>             notEmpty.signal(); /* будить ТІЛЬКИ consumer-потоки */
>         } finally {
>             lock.unlock();
>         }
>     }
> 
>     public T take() throws InterruptedException {
>         lock.lock();
>         try {
>             while (queue.isEmpty()) {
>                 notEmpty.await(); /* чекає поки хтось покладе елемент */
>             }
>             T item = queue.poll();
>             notFull.signal(); /* будить ТІЛЬКИ producer-потоки */
>             return item;
>         } finally {
>             lock.unlock();
>         }
>     }
> }
> ```

---

> [!example] **Три головні методи Condition**
> 
> ### 1. `await()`
> 
> Потік **відпускає lock і засинає** до отримання сигналу.
> 
> java
> 
> ```java
> lock.lock();
> try {
>     while (/*умова не виконана*/ queue.isEmpty()) {
>         condition.await(); /* атомарно: відпустити lock + заснути */
>     }
>     /* продовжує роботу після пробудження */
> } finally {
>     lock.unlock();
> }
> ```
> 
> 
> ### 2. `signal()`
> 
> Будить **один** потік з черги очікування цього Condition.
> 
> java
> 
> ```java
> lock.lock();
> try {
>     queue.add(item);
>     notEmpty.signal(); /* будить одного сплячого consumer-а */
> } finally {
>     lock.unlock();
> }
> ```
> 
> 
> ### 3. `signalAll()`
> 
> Будить **всі** потоки що чекають на цьому Condition.
> 
> java
> 
> ```java
> lock.lock();
> try {
>     queue.clear();
>     notFull.signalAll(); /* будить ВСІХ producer-ів одразу */
> } finally {
>     lock.unlock();
> }
> ```

---

> [!example] **Порівняння: один Condition vs два**
> 
> java
> 
> ```java
> /* ❌ ОДИН Condition — небезпечно */
> Condition condition = lock.newCondition();
> 
> /* put() викликає signal() → може розбудити інший put()-потік */
> /* take() викликає signal() → може розбудити інший take()-потік */
> /* результат: потік прокидається, перевіряє умову — вона не виконана, засинає знову */
> 
> /* ✅ ДВА Condition — точний контроль */
> Condition notFull  = lock.newCondition(); /* тільки для put()-потоків  */
> Condition notEmpty = lock.newCondition(); /* тільки для take()-потоків */
> 
> /* put()  → сигналізує notEmpty → будить ТІЛЬКИ take() */
> /* take() → сигналізує notFull  → будить ТІЛЬКИ put()  */
> ```

---

> [!example] **Аналогія з `wait()` / `notify()`**
> 
> java
> 
> ```java
> /* wait()/notify() — вбудовано в кожен Object, працює з synchronized */
> synchronized (lock) {
>     while (queue.isEmpty()) {
>         lock.wait();    /* = condition.await() */
>     }
>     lock.notify();      /* = condition.signal() */
> }
> 
> /* Condition — те саме, але для ReentrantLock */
> /* Перевага Condition: можна мати КІЛЬКА точок очікування на один lock */
> /* У synchronized — лише одна чергу wait() на об'єкт */
> lock.lock();
> try {
>     while (queue.isEmpty()) {
>         notEmpty.await(); /* точна група очікування */
>     }
>     notFull.signal();
> } finally {
>     lock.unlock();
> }
> ```

---

> [!imporntant] **Головна ідея:**
> 
> **Два потоки** — Producer (зелений) і Consumer (червоний) — обидва намагаються захопити один Lock.
> 
> **Два Condition** — `notFull` і `notEmpty` — це дві окремі "зали очікування". Коли буфер повний → Producer іде в `notFull.await()` і **відпускає lock**. Коли буфер порожній → Consumer іде в `notEmpty.await()` і **відпускає lock**.
> 
> **Хрест-навхрест сигнали** — ключова деталь:
> 
> - Consumer після `take()` викликає `notFull.signal()` → будить Producer
> - Producer після `put()` викликає `notEmpty.signal()` → будить Consumer
> 
> Тобто кожен будить **саме того**, хто йому потрібен.

---

> [!warning] Обмеження та ризики
> 
> - **Spurious wakeup**. Потік може прокинутись **без `signal()`** — особливість JVM. Тому завжди `while`, ніколи `if` перед `await()`.
> - **`await()` тільки під локом**. Виклик `await()` без захопленого lock кидає `IllegalMonitorStateException`.
> - **`InterruptedException`**. `await()` може бути перерваний. Завжди обробляй або прокидай виключення, інакше потік мовчки продовжить роботу в неочікуваному стані.
> - **Забутий `signal()`**. Якщо `signal()` не викликати після зміни стану — потоки сплять вічно. Дедлок без жодного виключення.

---

> [!tip] Коли використовувати
> 
> **Так** ✅:
> 
> - Потрібні **кілька незалежних умов** очікування на один lock (producer/consumer)
> - Потрібен **точний контроль** — будити конкретну групу потоків
> - Використовуєш `ReentrantLock` замість `synchronized`
> 
> **Ні** ❌:
> 
> - Достатньо `synchronized` + `wait()/notify()` для простих сценаріїв
> - Одна умова очікування — `wait()/notifyAll()` простіше

---

# Connections:
- [[Монітор у Java (Synchronization Monitor)]]
- [[Проблеми багатопотоковості (Race Condition та Deadlock)]]
- [[Об'єкти блокування (Lock Objects)]]
- [[Багатопотоковість, Процеси та Потоки в Java]]