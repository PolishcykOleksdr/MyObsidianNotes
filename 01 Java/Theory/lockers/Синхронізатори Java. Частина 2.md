> У другій частині розглядаються складніші механізми синхронізації, що дозволяють потокам обмінюватися даними або проходити через багатофазні процеси обробки.

---

> [!abstract] Core Framework
> 
> **Розширені синхронізатори:**
> 
> - **Class Exchanger**: Точка синхронізації, де два потоки можуть обмінюватися об'єктами. Робота блокується до моменту "зустрічі" обох потоків.
>     
> - **Class Phaser**: Гнучкий багаторазовий бар'єр, що підтримує багатофазні обчислення. Дозволяє динамічно реєструвати та скасовувати реєстрацію учасників.
>     

---

> [!info] Key Insights
> 
> - **Exchanger**: Ідеально підходить для сценаріїв типу Producer-Consumer, де один потік заповнює буфер, а інший — споживає, після чого вони обмінюються порожнім і повним контейнерами.
>     
> - **Phaser**: Це гібрид `CountDownLatch` та `CyclicBarrier`. Він дозволяє потокам синхронізуватися на кожному етапі (фазі) і продовжувати роботу разом або завершувати участь у процесі.
>     
> - **Динамічність Phaser**: На відміну від інших бар'єрів, кількість сторін у `Phaser` може змінюватися під час виконання через методи `register()` та `bulkRegister()`.
>     

---

> [!example] Практичне застосування: Exchanger
> 
> Java
> 
> ```java
> import java.util.ArrayList;
> import java.util.List;
> import java.util.concurrent.Exchanger;
> 
> /* >  * Буфер для обміну даними між потоками.
>  */
> class DataBuffer {
>     private final List<String> data = new ArrayList<>();
>     public String getData() { return data.remove(0); }
>     public void addToBuffer(String str) { data.add(str); }
>     public boolean isFull() { return data.size() == 1; }
> }
> 
> class Producer implements Runnable {
>     Exchanger<DataBuffer> exchanger;
>     Producer(Exchanger<DataBuffer> exchanger) { this.exchanger = exchanger; }
> 
>     @Override
>     public void run() {
>         DataBuffer producerBuffer = new DataBuffer();
>         for (int i = 0; i < 3; i++) {
>             producerBuffer.addToBuffer("Producer-" + i);
>             try {
>                 if (producerBuffer.isFull()) {
>                     /* Обмін повного буфера на порожній від Consumer */
>                     producerBuffer = exchanger.exchange(producerBuffer);
>                 }
>             } catch (InterruptedException e) { e.printStackTrace(); }
>         }
>     }
> }
> ```

---
> [!example] **Java Concurrency: Exchanger**
> ## Аналогія: Обмін піддонами на складі
> 
> Уяви складське вікно передачі:
> 
> 1. **Постачальник (Producer)** приносить повний піддон товарів.
>     
> 2. **Споживач (Consumer)** приносить порожній піддон (тару).
>     
> 3. **Зустріч:** Вони зустрічаються біля вікна. Постачальник не може піти, поки не отримає порожній піддон, а Споживач — поки не отримає повний.
>     
> 4. **Обмін:** Вони одночасно передають один одному свої піддони та розходяться.
>     
> ## Чому це важливо?
> 
> - **Синхронність:** Обмін відбувається лише тоді, коли **обидва** треди викликали метод `exchange()`. Той, хто прийшов першим, чекає на другого.
>     
> - **Ефективність:** Це один із найшвидших способів передачі даних між двома потоками, оскільки об'єкти передаються напряму.

---

> [!example] Практичне застосування: Phaser (Багатофазність)
> 
> Java
> 
> ```java
> import lombok.SneakyThrows;
> import java.util.concurrent.Phaser;
> 
> public class PhaserLearn {
>     @SneakyThrows
>     public static void main(String[] args) {
>         Phaser phaser = new Phaser(1);
> 
>         System.out.println("Phase in Main-" + phaser.getPhase() + " started");
> 
>         for (int i = 0; i < 3; i++) {
>             phaser.register();
>             new Thread(new FirstTask("Thread-" + i, phaser)).start();
>         }
> 
>         phaser.arriveAndAwaitAdvance();
>         System.out.println("Phase in Main-" + phaser.getPhase() + " completed");
> 
>         for (int i = 0; i < 2; i++) {
>             phaser.register();
>             new Thread(new SecondTask("Thread-" + i, phaser)).start();
>         }
> 
>         phaser.arriveAndAwaitAdvance();
>         System.out.println("Phase in Main-" + phaser.getPhase() + " completed");
> 
>         phaser.arriveAndDeregister();
>     }
> }
> class FirstTask implements Runnable{
>     private final String threadName;
>     private final Phaser phaser;
> 
>     public FirstTask(String threadName, Phaser phaser) {
>         this.threadName = threadName;
>         this.phaser = phaser;
>     }
> 
>     @Override
>     public void run() {
>         System.out.println("It`s first task... " + threadName);
> 
>         phaser.arriveAndAwaitAdvance();
> 
>         System.out.println("It`s first task, Phase-" + phaser.getPhase() + " completed");
> 
>         phaser.arriveAndDeregister();
>     }
> }
> class SecondTask implements Runnable{
>     private final String threadName;
>     private final Phaser phaser;
> 
>     public SecondTask(String threadName, Phaser phaser) {
>         this.threadName = threadName;
>         this.phaser = phaser;
>     }
> 
>     @Override
>     public void run() {
>         System.out.println("It`s second task... " + threadName);
> 
>         phaser.arriveAndAwaitAdvance();
> 
>         System.out.println("It`s second task, Phase-" + phaser.getPhase() + " completed");
> 
>         phaser.arriveAndDeregister();
>     }
> }
> ```

---

> [!example] **Java Concurrency: Phaser**
> 
> ##  Аналогія: Будівництво хмарочоса
> 
> Кожен новий поверх — це **фаза**. Прораб (`Phaser`) стежить, щоб робота йшла за планом:
> 
> 1. **Поверх 1 (Фаза 0):** Працює бригада фундаменту (5 людей). Прораб чекає на всіх п'ятьох. Коли закінчили — перехід до наступної фази.
>     
> 2. **Поверх 2 (Фаза 1):** Фундаментники пішли, прийшли каркасники (3 людини). `Phaser` тепер чекає тільки на трьох.
>     
> 3. **Поверх 3 (Фаза 2):** До каркасників приєдналися електрики (+4 людини). Тепер у фазі 7 учасників.
>     
> 
> ## Технічні суперсили
> 
> - **Динамічність:** Треди можуть реєструватися (`register()`) або зніматися з реєстрації (`arriveAndDeregister()`) "на льоту".
>     
> - **Гнучкість фаз:** Метод `onAdvance()` дозволяє виконувати логіку при переході між фазами (як-от закупівля матеріалів для нового поверху).
> 
> ## Коли обирати Phaser?
> 
> |**Ситуація**|**Інструмент**|
> |---|---|
> |Одноразова подія (старт системи)|**CountDownLatch**|
> |Циклічна робота, стала кількість потоків|**CyclicBarrier**|
> |**Змінна кількість воркерів на різних етапах**|**Phaser** ✅|
> |**Багатоступеневий пайплайн даних**|**Phaser** ✅|

---

> [!warning] Особливості Phaser
> 
> - **Метод arriveAndAwaitAdvance()**: Потік заявляє про прибуття і блокується до завершення поточної фази всіма учасниками.
>     
> - **Метод arriveAndDeregister()**: Зменшує кількість сторін, необхідних для переходу на наступну фазу.
>     
> - **Управління фазами**: Номер фази автоматично інкрементується при успішному проходженні бар'єру.
>     

---
# Connections:
- [[Синхронізація потоків у Java]]
- [[Синхронізатори Java. Частина 1]]
- [[Багатопотоковість, Процеси та Потоки в Java]]
- [[Java Executors — Thread Pool та submit()]]
- [[Об'єкти блокування (Lock Objects)]]