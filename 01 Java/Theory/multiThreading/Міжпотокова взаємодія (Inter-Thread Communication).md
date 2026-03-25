> **Міжпотокова взаємодія** — це механізм, що дозволяє синхронізованим потокам комунікувати один з одним. В основі лежить можливість потоку призупинити своє виконання у критичному розділі, звільнити монітор і дати можливість іншому потоку увійти до цього ж розділу.

---

> [!abstract] Core Framework 
> Взаємодія реалізується на рівні класу `Object` за допомогою трьох основних методів:
> 
> - `wait()`: Змушує поточний потік звільнити монітор і чекати, поки інший потік не викличе `notify()` або `notifyAll()`.
>     
> - `notify()`: Пробуджує один випадковий потік, який очікує на моніторі цього об'єкта.
>     
> - `notifyAll()`: Пробуджує всі потоки, що знаходяться в стані очікування на даному моніторі.
>     
> 
> **Концепція монітора:** Це своєрідний "контейнер", де одночасно може перебувати лише один потік. Всі інші повинні очікувати його виходу.

---

> [!info] Key Insights
> 
> - **Володіння монітором:** Методи `wait`, `notify` та `notifyAll` повинні викликатися **тільки з синхронізованого контексту** (методу або блоку), інакше буде викинуто виняток.
>     
> - **Звільнення ресурсів:** `wait()` не просто зупиняє потік, а тимчасово знімає блокування об'єкта, дозволяючи іншим потокам виконати свою роботу.
>     
> - **Довільність пробудження:** При використанні `notify()` вибір потоку для пробудження залежить від реалізації JVM і є довільним.
>     
> - **Ефективність:** `notifyAll()` слід використовувати, коли всі очікуючі потоки можуть виконати корисну роботу після завершення умови очікування.
>     

---

> [!example] Практичне застосування
> 
> #### 1. Ризик використання `notify()`
> 
> Метод `notify()` обирає **лише один** випадковий потік із черги очікування. Якщо потоків два або більше, решта залишиться в стані `WAITING` назавжди, що призведе до часткового зависання програми.
> 
> ```java
> /* Потік, який вміє чекати на власному моніторі */
> public class MyThread01 extends Thread {
>     public void run() {
>         synchronized (this) {
>             System.out.println(Thread.currentThread().getName() + "...starts");
>             try {
>                 this.wait(); // Звільняє монітор і чекає
>             } catch (InterruptedException e) {
>                 System.out.println(e.getMessage());
>             }
>             System.out.println(Thread.currentThread().getName() + "...notified");
>         }
>     }
> }
> 
> /* Потік, який чекає на моніторі об'єкта thread01 */
> public class MyThread02 extends Thread {
>     final MyThread01 thread01;
>     
>     MyThread02(MyThread01 thread01) { 
> 	    this.thread01 = thread01; 
>     }
> 
>     public void run() {
>         synchronized (this.thread01) {
>             System.out.println(Thread.currentThread().getName() + "...starts");
>             try {
>                 this.thread01.wait();
>             } catch (InterruptedException e) {
>                 System.out.println(e.getMessage());
>             }
>             System.out.println(Thread.currentThread().getName() + "...notified");
>         }
>     }
> }
> 
> /* Потік-будильник (сигналізатор) */
> public class MyThread03 extends Thread {
>     final MyThread01 thread01;
>     
>     MyThread03(MyThread01 thread01) { 
> 	    this.thread01 = thread01; 
>     }
> 
>     public void run() {
>         synchronized (this.thread01) {
>             System.out.println(Thread.currentThread().getName() + "...starts");
>             
>             /* УВАГА: Пробуджує лише ОДИН потік. 
>                Інший залишиться в стані wait() назавжди */
>             this.thread01.notify();
>             System.out.println(Thread.currentThread().getName() + "...notified");
>         }
>     }
> }
> ```
> 
> #### 2. Рішення: Використання `notifyAll()`
> 
> Метод `notifyAll()` надсилає сигнал **усім** потокам, які наразі перебувають у черзі очікування (`wait set`) цього конкретного об’єкта.
> 
> 
> ```java
> public class MyThread03All extends Thread {
>     final MyThread01 thread01;
>     
>     MyThread03All(MyThread01 thread01) { 
> 	    this.thread01 = thread01; 
>     }
> 
>     public void run() {
>         synchronized (this.thread01) {
>             System.out.println(Thread.currentThread().getName() + "...starts");
>             
>             /* ГАРАНТІЯ: Пробуджує ВСІ потоки на моніторі thread01 */
>             this.thread01.notifyAll();
>             System.out.println(Thread.currentThread().getName() + "...notified");
>         }
>     }
> }
> ```

---

> [!warning] Важливе
> 
> - **Зависання програми:** Неправильне використання `notify()` замість `notifyAll()` у складних системах часто призводить до того, що окремі потоки "засинають" назавжди.
>     
> - **Зайві пробудження:** `notifyAll()` може розбудити потоки, які ще не можуть продовжити роботу (наприклад, умова все ще не виконана), що створює зайве навантаження на CPU.
>     
> - **InterruptedException:** Метод `wait()` вимагає обробки цього винятку, оскільки потік може бути перерваний під час очікування.
>     

---

> [!tip] Коли використовувати 
> **notify() ✅:**
> 
> - Тільки один потік може виконати наступну дію.
>     
> - Використовується для взаємовиключного блокування.
>     
> 
> **notifyAll() ✅:**
> 
> - Всі очікувані потоки мають продовжити роботу (наприклад, завершення спільного завдання).
>     
> - Ви не впевнені, скільки потоків очікує і чи достатньо буде пробудити один.
>     

---
# Connections:
- [[Монітор у Java (Synchronization Monitor)]]
- [[Синхронізація потоків у Java]]
- [[Багатопотоковість, Процеси та Потоки в Java]]
- [[Методи класу Object]]