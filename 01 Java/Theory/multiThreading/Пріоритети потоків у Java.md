> Пріоритети потоків — це цілі числа, які визначають, як слід обробляти один потік по відношенню до інших. Вони використовуються планувальником потоків для розподілу ресурсів центрального процесора (ЦП).

---

> [!abstract] Core Framework
> 
> Пріоритети потоків є механізмом керування черговістю виконання, що базується на наступних принципах:
> 
> - Пріоритети представлені цілими числами в діапазоні від $1$ до $10$.
>     
> - Клас `Thread` визначає стандартні константи:
>     
>     - `Thread.MIN_PRIORITY` = $1$
>         
>     - `Thread.NORM_PRIORITY` = $5$ (за замовчуванням для `main`)
>         
>     - `Thread.MAX_PRIORITY` = $10$
>         
> - Успадкування: кожен новий потік успадковує пріоритет від свого батьківського потоку.
>     

---

> [!info] Key Insights
> 
> - **Попереджувальне планування.** Планувальник зазвичай обирає для виконання потік із найвищим пріоритетом.
>     
> - **Перемикання контексту.** Процес переходу ЦП від одного потоку до іншого. Високопріоритетний потік може витіснити потік із нижчим пріоритетом.
>     
> - **Добровільна поступка.** Потік може самостійно звільнити управління, передавши його готовому до роботи потоку з вищим пріоритетом.
>     
> - **Недетермінованість.** Якщо пріоритети однакові, вибір потоку залежить від реалізації `Thread Scheduler` у конкретному JDK.
>     

---

> [!example] Практичне застосування
> 
> **1. Отримання пріоритету за замовчуванням**
> 
> ```java
> public class MyThread01 extends Thread {
>     public void run() {
>         System.out.println("Thread 1 is running...");
>     }
> }
> public class MyThread02 extends Thread {
> 	public void run() {
> 		System.out.println("Thread 2 is running...");
> 	}
> 
> }
> public class MyThread03 extends Thread {
> 	public void run() {
> 		System.out.println("Thread 3 is running...");
> 	}
> }
> public class Main {
> 
> 	public static void main(String[] args) {
> 		MyThread01 thread01 = new MyThread01();
> 		MyThread02 thread02 = new MyThread02();
> 		MyThread03 thread03 = new MyThread03();
> 		
> 	    thread01.start();
> 	    thread02.start();
> 	    thread03.start();
> 	    
> 	    /* Отримуємо пріоритет за допомогою getPriority() */
> 	    System.out.println("Thread 1 priority is " + thread01.getPriority());
> 	    System.out.println("Thread 2 priority is " + thread02.getPriority());
> 	    System.out.println("Thread 3 priority is " + thread03.getPriority());
> 	}
> }
> ```
> 
> **2. Встановлення пріоритету через константи класу Thread**
> ```java
> public class MyThread01 extends Thread {
>     public void run() {
>         /* Встановлюємо MIN_PRIORITY (1) */
>         this.setPriority(Thread.MIN_PRIORITY);
>         
>         System.out.println("Thread 1 priority is " + this.getPriority());
>     }
> }
> public class MyThread02 extends Thread {
> 	public void run() {
> 		/* Встановлюємо MAX_PRIORITY (10) */
> 		this.setPriority(Thread.MAX_PRIORITY);
> 		
> 		System.out.println("Thread 2 priority is " + this.getPriority());
> 	}
> }
> public class MyThread03 extends Thread {
> 	public void run() {
> 		/* Встановлюємо NORM_PRIORITY (5) */
> 		this.setPriority(Thread.NORM_PRIORITY);
> 		
> 		System.out.println("Thread 3 priority is " + this.getPriority());
> 	}
> }
> ```
> 
> **3. Встановлення пріоритету числовим значенням**
> ```java
> public class Main {
>     public static void main(String[] args) {
>         MyThread01 thread01 = new MyThread01();
>         MyThread02 thread02 = new MyThread02();
>         MyThread03 thread03 = new MyThread03();
>         
> 	    thread01.start();
> 	    thread02.start();
> 	    thread03.start();
> 	    
> 	    /* Пряме призначення пріоритетів цілими числами */
> 	    thread01.setPriority(2);
> 	    thread02.setPriority(9);
> 	    thread03.setPriority(6);
> 	    System.out.println("Thread 1 priority is " + thread01.getPriority() +
> 	            "\nThread 2 priority is " + thread02.getPriority() +
> 	            "\nThread 3 priority is " + thread03.getPriority());
> 	}
> }
> ```

---

> [!warning] Обмеження та ризики
> 
> - **Залежність від ОС та JVM.** Результат виконання сильно залежить від операційної системи та конкретних налаштувань JVM на локальній машині.
>     
> - **Відсутність гарантій.** Планувальник потоків (`Thread Scheduler`) не гарантує однаковий порядок виконання кожного разу. Поведінка планувальника залежить від постачальника JDK.
>     
> - **Пріоритет != Порядок.** Вищий пріоритет збільшує шанси отримати ресурси ЦП першим, але не гарантує сувору черговість.
>     

---

> [!tip] Коли використовувати
> 
> Так ✅:
> 
> - Коли потрібно надати підказку планувальнику щодо важливості певних завдань.
>     
> - Для фонових завдань (низький пріоритет) та критичних операцій (високий пріоритет).
>     
> 
> Ні ❌:
> 
> - Коли логіка програми критично залежить від точного порядку виконання потоків.
>     
> - Якщо очікується однаковий результат на різних платформах (через різницю в реалізації JDK).
>     

---
# Connections:
- [[Багатопотоковість, Процеси та Потоки в Java]]