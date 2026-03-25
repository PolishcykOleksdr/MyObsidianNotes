> Lock Objects (Об'єкти блокування) працюють аналогічно до неявних блокувань, що використовуються в синхронізованому коді, але надають розширені можливості для керування доступом до спільних ресурсів. Ключове слово `synchronized` внутрішньо використовує вбудоване блокування об'єкта для отримання монопольного доступу до його полів.

---

> [!abstract] Core Framework 
> Механізми синхронізації в Java Concurrency API надають альтернативу ключовому слову `synchronized`:
> 
> - **Interface Lock**: Гнучка альтернатива внутрішнім блокуванням. Дозволяє явне керування (lock/unlock) та підтримує різні стратегії доступу.
>     
> - **Interface ReadWriteLock**: Спеціалізований інтерфейс, що розділяє доступ на читання (одночасне для багатьох) та запис (монопольне).
>     
> - **Class ReentrantLock**: Основна реалізація `Lock` з реентерабельною поведінкою (потік може повторно входити у блокування, яке вже утримує).
>     

---

> [!info] Key Insights
> 
> - **Гнучкість vs Жорсткість**: `synchronized` є досить жорстким — потік бере блокування лише раз, відсутні механізми черги, що може призвести до "голодування" (starvation) потоків.
>     
> - **Точний контроль**: Java Concurrency API дозволяє переривати очікування блокування, пробувати отримати його без очікування або встановлювати таймаут.
>     
> - **Реентерабельність**: Потік, якому належить `ReentrantLock`, може отримувати його більше одного разу без виникнення взаємного блокування (deadlock) самого себе.
>     
> - **Паралелізм читання**: `ReadWriteLock` підвищує продуктивність у системах, де читання відбувається значно частіше за запис, дозволяючи потокам не блокувати один одного під час читання.
>     

---

> [!example] Практичне застосування: ReentrantLock
> 
> Java
> 
> ```java
> import java.util.concurrent.locks.Lock;
> import java.util.concurrent.locks.ReentrantLock;
> 
> /* >  * Будь-який потік, що викликає метод increment(), спершу отримає
>  * блокування, а потім модифікує змінну num.
>  * Блок finally гарантує зняття блокування навіть при виключеннях.
>  */
> public class LockerUtil {
> 
>     private final Lock lock = new ReentrantLock();
> 
>     // Потокове безпечне інкрементування
>     public int increment(int num) {
>         lock.lock(); /* Явне отримання блокування */
>         try {
>             // Інкрементація значення
>             num = num + 1;
>             return num;
>         } finally {
>             lock.unlock(); /* Обов'язкове звільнення у finally */
>         }
>     }
> }
> 
> public class Main {
>     public static void main(String[] args) {
>         int[] numbers = new int[] {5, 3, 8};
>         LockerUtil lockerUtil = new LockerUtil();
>         for (int num : numbers) {
>             System.out.println("Initial value is " + num);
>             int newNum = lockerUtil.increment(num);
>             System.out.println("New value is " + newNum);
>         }
>     }
> }
> /* >  * Результат:
>  * Initial value is 5, New value is 6
>  * Initial value is 3, New value is 4
>  * Initial value is 8, New value is 9
>  */
> ```

---

> [!example] Практичне застосування: ReadWriteLock
> 
> Java
> 
> ```java
> import java.util.concurrent.locks.ReadWriteLock;
> import java.util.concurrent.locks.ReentrantReadWriteLock;
> 
> /* >  * Кілька потоків можуть читати одночасно, поки немає запису.
>  * Запис зупиняє всі потоки читання.
>  */
> public class LockerUtil {
> 
>     private final ReadWriteLock lock = new ReentrantReadWriteLock();
>     private int number;
> 
>     // Метод інкрементує (записує) значення
>     public int writeNumber(int value) {
>         lock.writeLock().lock(); /* Ексклюзивне блокування запису */
>         try {
>             number = value + 1;
>             return number;
>         } finally {
>             lock.writeLock().unlock();
>         }
>     }
> 
>     // Метод читає значення
>     public int readNumber() {
>         lock.readLock().lock(); /* Роздільне блокування читання */
>         try {
>             return number;
>         } finally {
>             lock.readLock().unlock();
>         }
>     }
> }
> ```

---

> [!warning] Обмеження та ризики
> 
> - **Ризик витоку блокувань**: Якщо `unlock()` не викликати у блоці `finally`, блокування може залишитися утримуваним назавжди при виникненні `Exception`.
>     
> - **Баланс викликів**: Метод `unlock()` повинен викликатися рівно стільки разів, скільки й `lock()`, інакше блокування не буде знято.
>     
> - **Пропускна здатність**: У програмах з дуже великою кількістю потоків загальна пропускна здатність може знизитися через складність механізмів керування чергами.
>     
> - **Явне управління**: На відміну від `synchronized`, розробник сам несе відповідальність за життєвий цикл блокування.
>     

---

> [!tip] Коли використовувати Так ✅:
> 
> - Потрібна можливість переривання очікування блокування.
>     
> - Потрібен неблокуючий спроб захоплення (`tryLock`).
>     
> - Необхідно розділити операції читання та запису для оптимізації продуктивності.
>     
> 
> Ні ❌:
> 
> - Достатньо простої синхронізації одного методу (краще використовувати `synchronized` для простоти).
>     
> - Немає можливості гарантувати виклик `unlock()` у блоці `finally`.
>     

---
# Connections:
- [[Синхронізація потоків у Java]]
- [[Синхронізатори Java. Частина 1]]
- [[Синхронізатори Java. Частина 2]]
- [[Багатопотоковість, Процеси та Потоки в Java]]
- [[Java Executors — Thread Pool та submit()]]