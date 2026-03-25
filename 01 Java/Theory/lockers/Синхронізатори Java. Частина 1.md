> У Java зв'язок між потоками досягається через спільний доступ до об'єктів та полів. Це створює ризики конфліктів потоків та порушення цілісності пам'яті. Синхронізатори з пакета `java.util.concurrent` надають високорівневі механізми для керування взаємодією потоків.

---

> [!abstract] Core Framework
> 
> **Синхронізатори (Synchronizers)** — це допоміжні утиліти спеціального призначення для синхронізації потоків:
> 
> - **Semaphore**: Обмежує доступ до ресурсу за допомогою лічильника дозволів.
>     
> - **CountDownLatch**: Змушує один або кілька потоків очікувати завершення певного набору операцій в інших потоках.
>     
> - **CyclicBarrier**: Дозволяє групі потоків чекати один одного в певній точці («бар'єрі»).
>     

---

> [!info] Key Insights
> 
> - **Semaphore (Семафор)**: Працює на основі "дозволів" (permits). Якщо лічильник $> 0$, потік бере дозвіл і зменшує лічильник. Якщо $0$ — потік блокується.
>     
> - **CountDownLatch (Клямка)**: Одноразовий механізм. Лічильник встановлюється в конструкторі та зменшується методом `countDown()`. Не може бути скинутий.
>     
> - **CyclicBarrier (Бар'єр)**: На відміну від клямки, є циклічним — його можна використовувати повторно після того, як всі сторони (parties) прибули. Підтримує необов'язкову "бар'єрну дію" (Runnable), яка виконується один раз перед розблокуванням усіх потоків.
>     

---

> [!example] Практичне застосування: Semaphore
> 
> Java
> 
> ```java
> import java.util.concurrent.Semaphore;
> 
> public class Shared {
>     static int count = 0; /* Спільний ресурс */
> }
> 
> public class MyThread extends Thread {
>     Semaphore sem;
>     String threadName;
> 
>     public MyThread(Semaphore sem, String threadName) {
>         super(threadName);
>         this.sem = sem;
>         this.threadName = threadName;
>     }
> 
>     @Override
>     public void run() {
>         try {
>             System.out.println(threadName + " is waiting for a permit.");
>             sem.acquire(); /* Отримання дозволу */
>             System.out.println(threadName + " gets a permit.");
>             
>             for (int i = 0; i < 5; i++) {
>                 if (this.getName().equals("Alfa")) Shared.count++;
>                 else Shared.count--;
>                 System.out.println(threadName + ": " + Shared.count);
>                 Thread.sleep(10);
>             }
>         } catch (InterruptedException exc) {
>             System.out.println(exc.getMessage());
>         } finally {
>             System.out.println(threadName + " releases the permit.");
>             sem.release(); /* Звільнення дозволу */
>         }
>     }
> }
> ```

---

> [!example] **Java Concurrency: Semaphore**
> 
> ##  Аналогія: Охоронець нічного клубу
> 
> Уяви клуб, де кількість місць обмежена. Охоронець (Семафор) стежить за входом:
> 
> 1. **Клуб має місткість $N$ (наприклад, 3).**
>     
> 2. **Поки є вільні місця**, охоронець пропускає відвідувачів (`acquire()`).
>     
> 3. **Коли місць немає**, нові люди стоять у черзі на вулиці.
>     
> 4. **Як тільки хтось виходить** (`release()`), охоронець одразу гукає наступного з черги.
>     
> 
> ## Візуалізація процесу
> 
> |**Стан**|**В середині клубу (3 місця)**|**Черга на вулиці**|
> |---|---|---|
> |**Початок**|[Аліса ✅] [Боб ✅] [Віктор ✅]|Дмитро ❌ (чекає...)|
> |**Дія**|_Аліса виходить_|_Охоронець киває Дмитру_|
> |**Результат**|[Боб ✅] [Віктор ✅] [Дмитро ✅]|(черга порожня)|
> 
> **Ключова ідея** — дозволи **повертаються** і використовуються знову і знову.

---

> [!example] Практичне застосування: CountDownLatch
> 
> Java
> 
> ```java
> import java.util.concurrent.CountDownLatch;
> 
> class PassengerThread implements Runnable {
>     private final String name;
>     private final CountDownLatch latch;
> 
>     public PassengerThread(String name, CountDownLatch latch) {
>         this.name = name;
>         this.latch = latch;
>     }
> 
>     @Override
>     public void run() {
>         /* Імітація приходу пасажира */
>         System.out.println(name + " in the boat.");
>         latch.countDown(); /* Зменшуємо лічильник на 1 */
>     }
> }
> 
> // В Main:
> CountDownLatch latch = new CountDownLatch(5);
> // ... запуск 5 потоків ...
> latch.await(); /* Чекаємо, поки лічильник стане 0 */
> System.out.println("The boat is leaving.");
> ```

---

> [!example] **Java Concurrency: CountDownLatch**
> 
> ## Аналогія: Стартовий пістолет
> 
> Уяви фінал забігу на 100 метрів:
> 
> 1. **Суддя встановлює лічильник** на кількість атлетів (наприклад, 5).
>     
> 2. **Кожен атлет** підходить до колодок і звітує про готовність (`countDown()`).
>     
> 3. **Суддя чекає** (`await()`), поки лічильник не впаде до нуля.
>     
> 4. **Коли лічильник = 0**, лунає постріл, і всі атлети стартують одночасно.
> ## Візуалізація готовності
> 
> |**Подія**|**Стан лічильника**|**Статус судді**|
> |---|---|---|
> |**Старт**|`5`|Чекає...|
> |**Аліса та Боб готові**|`3`|Чекає...|
> |**Всі 5 готові**|`0`|**БАХ! 🔫 Всі біжать**|
> 
> `CountDownLatch` не можна "перезарядити" або скинути. Якщо лічильник досяг нуля, засувка залишається відкритою назавжди.

---

> [!example] Практичне застосування: CyclicBarrier
> 
> Java
> 
> ```java
> import java.util.concurrent.CyclicBarrier;
> import java.util.List;
> import java.util.ArrayList;
> import java.util.Collections;
> 
> class BarrierAction implements Runnable {
>     private final List<Integer> dataList;
>     BarrierAction(List<Integer> dataList) { this.dataList = dataList; }
> 
>     @Override
>     public void run() {
>         /* Виконується один раз, коли всі потоки досягли бар'єру */
>         System.out.println("Barrier triggered. Size: " + dataList.size());
>     }
> }
> 
> class Worker implements Runnable {
>     private final CyclicBarrier cb;
>     private final List<Integer> dataList;
> 
>     Worker(List<Integer> dataList, CyclicBarrier cb) {
>         this.dataList = dataList;
>         this.cb = cb;
>     }
> 
>     @Override
>     public void run() {
>         dataList.add(1);
>         try {
>             cb.await(); /* Очікування інших сторін */
>         } catch (Exception e) { /* ... */ }
>     }
> }
> ```

---

> [!example] **Java Concurrency: CyclicBarrier**
> 
> ## Аналогія: Похід у гори з групою
> 
> Уяви групу з 5 туристів на складному маршруті:
> 
> 1. **Маршрут має кілька привалів.** Ніхто не йде до наступної точки, поки вся група не збереться разом.
>     
> 2. **Швидкі туристи чекають повільних** на кожному привалі (`await()`).
>     
> 3. **Коли останній турист приходить**, бар'єр "ламається", і всі йдуть далі.
>     
> 4. **Бар'єр автоматично відновлюється** для наступного привалу.
>     
> ## Чому ця асоціація точна?
> 
> |**Поняття походу**|**CyclicBarrier**|
> |---|---|
> |**Привал**|Виклик методу `barrier.await()`|
> |**Всі зібрались → рушаємо**|Всі треди дійшли до точки → блокування знімається|
> |**Наступний привал**|Бар'єр автоматично скидається до початкового стану|
> |**Гід оголошує "рушаємо"**|Опціональний `Runnable` (Barrier Action) у конструкторі|

---

> [!warning] Обмеження та ризики
> 
> - **BrokenBarrierException**: Якщо один із потоків у `CyclicBarrier` переривається або виникає таймаут, бар'єр вважається "зламаним" для всіх інших.
>     
> - **Одноразовість CountDownLatch**: Якщо вам потрібно повторно використовувати лічильник після досягнення нуля, `CountDownLatch` не підійде — використовуйте `CyclicBarrier`.
>     
> - **Starvation (Голодування)**: Неправильне налаштування кількості дозволів у `Semaphore` може призвести до того, що потоки вічно очікуватимуть у черзі.
>     

---

> [!tip] Коли використовувати
> 
> Так ✅:
> 
> - `Semaphore`: Коли потрібно обмежити кількість одночасних з'єднань з БД або доступу до API.
>     
> - `CountDownLatch`: Коли основний потік має чекати завершення ініціалізації (наприклад, завантаження драйверів).
>     
> - `CyclicBarrier`: Коли потрібно розбити велике завдання на частини, обробити їх паралельно, а потім об'єднати результати.
>     

---
# Connections:
- [[Синхронізація потоків у Java]]
- [[Синхронізатори Java. Частина 2]]
- [[Багатопотоковість, Процеси та Потоки в Java]]
- [[Java Executors — Thread Pool та submit()]]
- [[Об'єкти блокування (Lock Objects)]]