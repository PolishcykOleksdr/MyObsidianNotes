> Поширеними проблемами багатопотоковості можуть бути: **Стан перегонів (Race condition)** та **Взаємоблокування (Deadlock)**.

---

> [!abstract] **Core Framework**
> 
> Спільні ресурси в багатопотоковому середовищі потребують контролю доступу через критичні секції:
> 
> - **Критична секція** — секція коду, яка виконується кількома потоками та в якій послідовність виконання потоків впливає на результат одночасного виконання.
>     

---

> [!info]
> 
> ### Key Insights
> 
> - **Метафора перегонів.** Термін походить від ідеї, що потоки "пробігають" через критичну секцію, і те, хто прийде першим, змінює фінальний стан програми.
>     
> - **Причина Race Condition.** Виникає, коли результат роботи кількох потоків різниться залежно від непередбачуваної послідовності їх виконання.
>     
> - **Причина Deadlock.** Виникає, коли перший потік очікує блокування об'єкта, що вже утримується другим потоком, а другий потік одночасно очікує на ресурс, заблокований першим.
>     
> - **Синхронізація.** Використання `synchronized` гарантує, що модифікація змінної в одному потоці виконується без втручання інших потоків, забезпечуючи атомарність операцій у критичній секції.
>     

---

> [!example]
> 
> ### Практичне застосування: Race Condition
> 
> Проблема: Непередбачуваний результат. Очікувана поведінка: кожен із 6 потоків має отримати унікальне значення від 1 до 6.
> 
> ```java
> public class Main01 {
>     int counter;
> 
>     public static void main(String[] args) {
>         Main01 main01 = new Main01();
> 
>         for (int i = 1; i <= 6; i++) {
>             new Thread(() -> {
>                 main01.incrementCounter();
>                 /* Зміна та виведення змінної без синхронізації */
>                 System.out.println(Thread.currentThread().getName()
>                         + " get value " + main01.getCounter());
>             }).start();
>         }
>     }
> 
>     public void incrementCounter() {
>         try {
>             /* Штучна затримка збільшує ймовірність перемикання контексту */
>             Thread.sleep(100);
>         } catch (InterruptedException e) {
>             System.out.println("Exception: " + e.getMessage());
>         }
>         counter++;
>     }
> 
>     public int getCounter() {
>         return counter;
>     }
> }
> ```
> ## Результат (приклад): 
> ```
> Thread-3 get value 2 
> Thread-5 get value 2 
> Thread-2 get value 3 
> Thread-0 get value 4 
> Thread-1 get value 5 
> Thread-4 get value 2 (Дублювання та втрата значень) 
> ```

---

> [!solution]
> 
> ### Практичне рішення: Race Condition
> 
> Використання монітора об'єкта (`main02`) гарантує, що лише один потік у конкретний момент часу може виконувати блок коду всередині `synchronized`. Це забезпечує впорядкований доступ і правильний результат.
> 
> ```java
> public class Main02 {
>     int counter;
> 
>     public static void main(String[] args) {
>         Main02 main02 = new Main02();
> 
>         for (int i = 1; i <= 6; i++) {
>             new Thread(() -> {
>                 /* Синхронізація на об'єкті для уникнення перегонів */
>                 synchronized (main02) {
>                     main02.incrementCounter();
>                     System.out.println(Thread.currentThread().getName()
>                             + " get value " + main02.getCounter());
>                 }
>             }).start();
>         }
>     }
> 
>     public void incrementCounter() {
>         try {
>             Thread.sleep(100);
>         } catch (InterruptedException e) {
>             System.out.println("Exception: " + e.getMessage());
>         }
>         counter++;
>     }
> 
>     public int getCounter() {
>         return counter;
>     }
> } 
> ```
> 
> # Результат: 
> ```
> Thread-0 get value 1 
> Thread-5 get value 2 
> Thread-4 get value 3 
> Thread-3 get value 4 
> Thread-2 get value 5 
> Thread-1 get value 6 (Значення унікальні та послідовні) */
> ```

---

> [!example]
> 
> ### Практичне застосування: Deadlock (Взаємне блокування)
> 
> Проблема: програма повністю зупиняє виконання через те, що потоки нескінченно чекають на звільнення ресурсів, які вони самі ж і утримують.
> 
> ```java
> /* Клас, який використовується обома потоками */
> public class Shared {
>     /* Перший синхронізований метод */
>     synchronized void test1(Shared s2) {
>         System.out.println(Thread.currentThread().getName() + " : test1-begin");
>         SleepUtil.sleep(1000); // Даємо час другому потоку захопити свій ресурс
>         
>         /* Спроба заблокувати об'єкт s2, поки утримується монітор поточного об'єкта */
>         s2.test2();
>         System.out.println(Thread.currentThread().getName() + " : test1-end");
>     }
> 
>     /* Другий синхронізований метод */
>     synchronized void test2() {
>         System.out.println(Thread.currentThread().getName() + " : test2-begin");
>         SleepUtil.sleep(1000);
>         System.out.println(Thread.currentThread().getName() + " : test2-end");
>     }
> }
> public class MyThread01 extends Thread {
>     private final Shared s1;
>     private final Shared s2;
> 
>     public MyThread01(Shared s1, Shared s2) {
>         this.s1 = s1;
>         this.s2 = s2;
>     }
> 
>     @Override
>     public void run() {
>         /* Блокує s1 і намагається викликати метод на s2 */
>         s1.test1(s2);
>     }
> }
> 
> public class MyThread02 extends Thread {
>     private final Shared s1;
>     private final Shared s2;
> 
>     public MyThread02(Shared s1, Shared s2) {
>         this.s1 = s1;
>         this.s2 = s2;
>     }
> 
>     @Override
>     public void run() {
>         /* Блокує s2 і намагається викликати метод на s1 -> Виникає Deadlock */
>         s2.test1(s1);
>     }
> }
> public class Main {
>     public static void main(String[] args) {
>         Shared s1 = new Shared();
>         Shared s2 = new Shared();
> 
>         MyThread01 t1 = new MyThread01(s1, s2);
>         MyThread02 t2 = new MyThread02(s1, s2);
> 
>         t1.start();
>         t2.start();
>     }
> }
> ```
> # Результат (приклад):
> ```
> Нічого
> ```
> # Чому?
> Програма зависає:
> t1 тримає s1 і хоче s2
> t2 тримає s2 і хоче s1

---

> [!warning]
> 
> ### Обмеження та ризики
> 
> - **Зависання системи.** При Deadlock потоки перебувають у стані вічного очікування, що призводить до повної зупинки виконання відповідної логіки.
>     
> - **Недетермінованість.** Стан перегонів може проявлятися не при кожному запуску (як у `Main01`), що робить такі помилки надзвичайно складними для відтворення та відлагодження.
>     
> - **Продуктивність.** Надмірна синхронізація вирішує проблему Race Condition, але може сповільнити програму через черги потоків до критичної секції.
>     

---

> [!tip]
> 
> ### Коли використовувати
> 
> - **Синхронізація (synchronized) ✅:** Коли кілька потоків одночасно модифікують спільну змінну або стан об'єкта.
>     
> - **Уникання вкладених блокувань ✅:** Для запобігання Deadlock намагайтеся не викликати синхронізовані методи іншого об'єкта всередині вже заблокованого методу.
>     

---
# Connections:
- [[Багатопотоковість, Процеси та Потоки в Java]]
- [[Міжпотокова взаємодія (Inter-Thread Communication)]]
- [[Синхронізація потоків у Java]]