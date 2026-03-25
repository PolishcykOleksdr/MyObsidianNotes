> Головний потік — це основа виконання будь-якого Java-додатку. Він створюється автоматично JVM у момент запуску програми та служить точкою входу, де викликається метод `main()`.

---

> [!abstract] Core Framework
> 
> Головний потік (Main Thread) має наступні характеристики:
> 
> - **Точка входу:** JVM ініціалізує клас і шукає метод `public static void main(String[] args)`.
>     
> - **Управління:** Для контролю потоку використовується метод `Thread.currentThread()`.
>     
> - **Автоматизація:** Створюється системою, не потребує ручного виклику `start()`.
>     
> - **Життєвий цикл:** Часто завершується останнім, оскільки виконує фінальні операції та координує дочірні потоки.
>     

---

> [!info] Key Insights
> 
> - **Ієрархія:** Головний потік є батьківським для всіх потоків, що створюються всередині методу `main()`.
>     
> - **Успадкування властивостей:** Дочірні потоки отримують (успадковують) пріоритет та статус (daemon/non-daemon) від батьківського потоку в момент створення.
>     
> - **Динамічна зміна:** Ви можете змінювати ім'я (`setName`) та пріоритет (`setPriority`) головного потоку прямо під час його виконання.
>     
> - **Пріоритет за замовчуванням:** Становить $5$ (`NORM_PRIORITY`).
>     

---

> [!example] Практичне застосування
> 
> ```java
> /* Потік, який є дочірнім для головного потоку */
> public class MyThread01 extends Thread {
> 	@Override
> 	public void run() {
> 	    for (int i = 1; i < 4; i++) {
> 	        System.out.println("Thread 1 - " + i);
> 	    }
> 	}
> }
> public class Main extends Thread {
> 	public static void main(String[] args) {
> 	    /* Отримання посилання на поточний (основний) потік */
> 	    Thread mainThread = Thread.currentThread();
> 	    
> 	    /* Робота з ім'ям потоку */
> 	    System.out.println("Current thread name is " + mainThread.getName());
> 	   
> 	    mainThread.setName("MySuperMain");
> 	    
> 	    System.out.println("After change, name is " + mainThread.getName());
> 	    
> 	    /* Робота з пріоритетом */
> 	    System.out.println("Main Thread priority is " + mainThread.getPriority());
>     
> 	    /* Встановлюємо максимальний пріоритет (10) */
> 	    mainThread.setPriority(MAX_PRIORITY);
> 	    
> 	    System.out.println("Main Thread new priority is " + mainThread.getPriority());
> 	    
> 	    for (int i = 1; i < 4; i++) {
> 	        System.out.println("Main Thread - " + i);
> 	    }
> 	    
> 	    /* Створення дочірнього потоку */
> 	    MyThread01 thread01 = new MyThread01();
> 	    
> 	    /* Пріоритет успадковується від mainThread (на даний момент він 10) */
> 	    System.out.println("Thread 1 priority is " + thread01.getPriority());
> 	    
> 	    /* Зміна пріоритету дочірнього потоку на MIN (1) */
> 	    thread01.setPriority(MIN_PRIORITY);
> 	
> 	    System.out.println("Thread 1 new priority is " + thread01.getPriority());
> 	    
> 	    /* Запуск дочірнього потоку */
> 	    thread01.start();
> 	}
> }
> ```

---

> [!warning] Важливо
> 
> - **Точка входу:** Починаючи з JDK 6, наявність методу `main()` є обов'язковою для запуску окремого Java-додатку.
>     
> - **Успадкування:** Якщо змінити пріоритет головного потоку до створення дочірнього, дочірній отримає саме це нове значення. Наприклад, якщо `main` має пріоритет $10$, то `new Thread()` також отримає $10$.
>     

---

> [!tip] Переваги та особливості
> 
> Так ✅:
> 
> - Використовуйте `main` як центральний диспетчер для ініціалізації сервісів.
>     
> - Змінюйте імена потоків (`setName`) для зручного дебагінгу та аналізу логів.
>     
> 
> Ні ❌:
> 
> - Не забивайте головний потік важкими обчисленнями в GUI-додатках, щоб не "заморозити" інтерфейс.
>     

---
# Connections:
- [[Пріоритети потоків у Java]]
- [[Багатопотоковість, Процеси та Потоки в Java]]