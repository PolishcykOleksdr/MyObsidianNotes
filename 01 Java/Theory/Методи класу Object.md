> Клас **`Object`** — це **кореневий клас усієї ієрархії Java**. Кожен клас у Java автоматично успадковує його методи. Це означає, що будь-який об'єкт у JVM гарантовано має набір базових можливостей: порівняння, хешування, рядкове представлення, синхронізація потоків та управління пам'яттю.

---

> [!abstract] Core Framework 
> **Клас `Object` визначає 11 методів (фактично - 9, не deprecated - 8), які утворюють фундаментальний контракт будь-якого об'єкта в JVM:**
> 
> - `equals(Object obj)` — перевірка логічної рівності
> - `hashCode()` — числовий ідентифікатор для хеш-структур
> - `toString()` — рядкове представлення об'єкта
> - `getClass()` — метадані класу під час виконання
> - `clone()` — копіювання об'єкта
> - `finalize()` — хук перед збором GC (застарілий)
> - `wait()`, `wait(long)`, `wait(long, int)` — призупинення потоку на моніторі
> - `notify()` — пробудження одного потоку
> - `notifyAll()` — пробудження всіх потоків

---

> [!info] Key Insights
> 
> - **Контракт `equals` і `hashCode`.** Якщо два об'єкти рівні за `equals`, їхні `hashCode` **зобов'язані** збігатися. Порушення цього правила ламає `HashMap`, `HashSet` та будь-яку іншу хеш-структуру.
> - **`equals` за замовчуванням — це `==`.** Базова реалізація порівнює посилання, а не вміст. Для value-об'єктів (DTO, Entity) завжди треба перевизначати.
> - **`hashCode` за замовчуванням.** JVM генерує його на основі адреси пам'яті об'єкта (або через внутрішній лічильник — залежить від реалізації JVM). Після перевизначення `equals` потрібно обов'язково перевизначити й `hashCode`.
> - **`clone()` — поверхнева копія.** Метод копіює лише примітиви та посилання, а не об'єкти, на які ці посилання вказують. Для глибокого клонування потрібна власна реалізація.
> - **`wait/notify` потребують монітора.** Виклик поза `synchronized`-блоком кидає `IllegalMonitorStateException`. Ці методи — основа низькорівневої міжпотокової комунікації в Java.
> - **`finalize()` є застарілим з Java 9.** Не гарантує виклику і може затримати GC. Для звільнення ресурсів використовуй `AutoCloseable` + try-with-resources або `Cleaner`.
> - **`getClass()` — завжди клас фактичного об'єкта,** а не типу посилання. `((Object) "hello").getClass()` повертає `String.class`.

---

> [!example] **Методи**
> ## 1. `equals(Object obj)`
> 
> Визначає логічну рівність двох об'єктів. Базова реалізація порівнює посилання (`this == obj`).
> 
> java
> 
> ```java
> public class Point {
>     int x, y;
> 
>     Point(int x, int y) {
>         this.x = x;
>         this.y = y;
>     }
> 
>     /* Контракт: рефлексивність, симетрія, транзитивність, консистентність, null-безпека */
>     @Override
>     public boolean equals(Object obj) {
>         if (this == obj) return true;           /* 1. та сама адреса — гарантовано рівні */
>         if (obj == null) return false;          /* 2. null ніколи не рівний нічому */
>         if (getClass() != obj.getClass())       /* 3. різні класи — різні об'єкти */
>             return false;
>         Point other = (Point) obj;
>         return x == other.x && y == other.y;   /* 4. порівнюємо поля */
>     }
> }
> ```
> 
> 
> ## 2. `hashCode()`
> 
> Повертає ціле число — хеш-код об'єкта. Використовується як індекс у `HashMap`, `HashSet`, `Hashtable`.
> 
> java
> 
> ```java
> public class Point {
>     int x, y;
> 
>     /* Правило: якщо equals() повертає true — hashCode() МУСИТЬ бути однаковим */
>     @Override
>     public int hashCode() {
>         return 31 * x + y;
>         /* Число 31 — непарне просте, мінімізує колізії.
>            JVM оптимізує множення на 31 як (x << 5) - x */
>     }
> }
> ```
> 
> java
> 
> ```java
> /* Сучасний спосіб через Objects.hash() (Java 7+) */
> @Override
> public int hashCode() {
>     return Objects.hash(x, y); /* генерує hashCode на основі всіх переданих полів */
> }
> ```
> 
> 
> ## 3. `toString()`
> 
> Повертає рядкове представлення об'єкта. Базова реалізація: `getClass().getName() + "@" + Integer.toHexString(hashCode())`.
> 
> java
> 
> ```java
> public class Point {
>     int x, y;
> 
>     @Override
>     public String toString() {
>         return "Point{x=" + x + ", y=" + y + "}";
>         /* Без перевизначення System.out.println(point) виводить Point@1b6d3586 */
>     }
> }
> ```
> 
> java
> 
> ```java
> /* toString() викликається неявно при конкатенації рядків */
> Point p = new Point(3, 4);
> System.out.println("Точка: " + p); /* Точка: Point{x=3, y=4} */
> System.out.println(p.toString());  /* Point{x=3, y=4} */
> ```
> 
> 
> ## 4. `getClass()`
> 
> Повертає об'єкт `Class<?>` — метадані про фактичний клас об'єкта під час виконання. Метод `final`, перевизначити неможливо.
> 
> java
> 
> ```java
> Object str = "hello";
> Object num = 42;
> 
> System.out.println(str.getClass());           /* class java.lang.String */
> System.out.println(str.getClass().getName()); /* java.lang.String */
> System.out.println(num.getClass());           /* class java.lang.Integer */
> 
> /* getClass() повертає ФАКТИЧНИЙ тип, а не тип посилання */
> Number n = new Integer(5);
> System.out.println(n.getClass()); /* class java.lang.Integer, а не Number */
> 
> /* Використання в equals() для перевірки сумісності типів */
> if (getClass() != obj.getClass()) return false;
> 
> /* Отримання класу без об'єкта (статично) */
> Class<String> c = String.class;
> ```
> 
> 
> ## 5. `clone()`
> 
> Створює та повертає поверхневу копію об'єкта. Клас зобов'язаний реалізувати маркерний інтерфейс `Cloneable`, інакше кидається `CloneNotSupportedException`.
> 
> java
> 
> ```java
> public class ArrayContainer implements Cloneable {
>     int[] data;
> 
>     ArrayContainer(int[] data) {
>         this.data = data;
>     }
> 
>     @Override
>     public ArrayContainer clone() {
>         try {
>             ArrayContainer copy = (ArrayContainer) super.clone();
>             /* super.clone() — ПОВЕРХНЕВА копія: скопіює посилання data,
>                але не масив, на який воно вказує */
> 
>             copy.data = this.data.clone(); /* глибоке копіювання масиву вручну */
>             return copy;
>         } catch (CloneNotSupportedException e) {
>             throw new AssertionError(); /* не відбудеться, бо реалізуємо Cloneable */
>         }
>     }
> }
> ```
> 
> java
> 
> ```java
> ArrayContainer original = new ArrayContainer(new int[]{1, 2, 3});
> ArrayContainer copy = original.clone();
> 
> copy.data[0] = 99;
> System.out.println(original.data[0]); /* 1 — оригінал не змінився завдяки глибокому копіюванню */
> ```
> 
> 
> ## 6. `finalize()`
> 
> Викликається GC перед тим, як об'єкт буде зібраний. **Застарілий з Java 9, видалений з Java 18.**
> 
> java
> 
> ```java
> public class LegacyResource {
> 
>     /* ЗАСТАРІЛО: не використовуй у новому коді.
>        Проблеми:
>        - виклик не гарантований (GC може ніколи не зібрати об'єкт)
>        - може призупинити GC (finalizer thread — однопотоковий)
>        - може "воскресити" об'єкт (зберегти this в статичному полі) */
>     @Override
>     @Deprecated(since = "9", forRemoval = true)
>     protected void finalize() throws Throwable {
>         try {
>             System.out.println("Прибираємо ресурс");
>         } finally {
>             super.finalize();
>         }
>     }
> }
> ```
> 
> java
> 
> ```java
> /* СУЧАСНА АЛЬТЕРНАТИВА: AutoCloseable + try-with-resources */
> public class ModernResource implements AutoCloseable {
>     @Override
>     public void close() {
>         System.out.println("Ресурс звільнено"); /* викликається гарантовано */
>     }
> }
> 
> try (ModernResource r = new ModernResource()) {
>     /* робота з ресурсом */
> } /* close() викликається автоматично */
> ```
> 
> 
> ## 7. `wait()`, `wait(long timeout)`, `wait(long timeout, int nanos)`
> 
> Переводить поточний потік у стан очікування на моніторі об'єкта. Потік звільняє монітор і чекає виклику `notify()` / `notifyAll()` або закінчення таймауту.
> 
> java
> 
> ```java
> class SharedBuffer {
>     private String data;
>     private boolean ready = false;
> 
>     /* Споживач: чекає на дані */
>     public synchronized String consume() throws InterruptedException {
>         while (!ready) {
>             /* wait() МУСИТЬ бути всередині synchronized-блоку на ЦЬОМУ об'єкті.
>                Потік звільняє монітор і засипає.
>                При пробудженні — повторно захоплює монітор і перевіряє умову (while, не if!) */
>             wait();
>         }
>         ready = false;
>         return data;
>     }
> 
>     /* Виробник: кладе дані і будить споживача */
>     public synchronized void produce(String value) {
>         this.data = value;
>         this.ready = true;
>         notify(); /* будить ONE потік, що чекає на моніторі цього об'єкта */
>     }
> }
> ```
> 
> java
> 
> ```java
> /* wait(long timeout) — чекає не більше вказаних мілісекунд */
> synchronized (lock) {
>     lock.wait(5000); /* прокинеться через 5 секунд або раніше після notify() */
> }
> 
> /* wait(long timeout, int nanos) — додаткова точність у наносекундах (рідко використовується) */
> synchronized (lock) {
>     lock.wait(1000, 500000); /* 1 секунда + 500_000 нс = 1.5 мс точніше */
> }
> ```
> 
> 
> ## 8. `notify()`
> 
> Пробуджує **один** довільний потік, що очікує на моніторі цього об'єкта. Вибір потоку — на розсуд JVM.
> 
> java
> 
> ```java
> class TaskQueue {
>     private final Queue<String> tasks = new LinkedList<>();
> 
>     public synchronized void addTask(String task) {
>         tasks.offer(task);
>         notify(); /* будимо ОДИН потік-споживач */
>         /* Увага: якщо 3 потоки чекають, прокинеться лише один — непередбачено який */
>     }
> 
>     public synchronized String takeTask() throws InterruptedException {
>         while (tasks.isEmpty()) {
>             wait();
>         }
>         return tasks.poll();
>     }
> }
> ```
> 
> 
> ## 9. `notifyAll()`
> 
> Пробуджує **всі** потоки, що очікують на моніторі цього об'єкта. Усі вони конкурують за монітор — лише один захопить його першим.
> 
> java
> 
> ```java
> class EventBus {
>     private String lastEvent;
> 
>     public synchronized void publishEvent(String event) {
>         lastEvent = event;
>         notifyAll(); /* будимо ВСІХ підписників */
>         /* Використовуй notifyAll() коли:
>            - декілька потоків чекають різних умов
>            - не знаєш, скільки потоків треба розбудити
>            - хочеш уникнути "загубленого пробудження" */
>     }
> 
>     public synchronized String waitForEvent() throws InterruptedException {
>         while (lastEvent == null) {
>             wait();
>         }
>         String event = lastEvent;
>         lastEvent = null;
>         return event;
>     }
> }
> ```

---

> [!example] Практичне застосування
> 
> **Повний приклад: клас зі всіма перевизначеними методами:**
> 
> java
> 
> ```java
> import java.util.Objects;
> 
> public class Employee implements Cloneable {
>     private final int id;
>     private final String name;
> 
>     public Employee(int id, String name) {
>         this.id = id;
>         this.name = name;
>     }
> 
>     /* equals: два Employee рівні якщо id однаковий */
>     @Override
>     public boolean equals(Object obj) {
>         if (this == obj) return true;
>         if (!(obj instanceof Employee)) return false;
>         Employee other = (Employee) obj;
>         return id == other.id;
>     }
> 
>     /* hashCode: має бути консистентним з equals */
>     @Override
>     public int hashCode() {
>         return Objects.hash(id);
>     }
> 
>     /* toString: зручне логування та дебаг */
>     @Override
>     public String toString() {
>         return "Employee{id=" + id + ", name='" + name + "'}";
>     }
> 
>     /* clone: поверхнева копія (поля — примітиви і immutable String — безпечно) */
>     @Override
>     public Employee clone() {
>         try {
>             return (Employee) super.clone();
>         } catch (CloneNotSupportedException e) {
>             throw new AssertionError();
>         }
>     }
> 
>     /* getClass(): використовується в equals та для рефлексії */
>     public void printClassInfo() {
>         System.out.println(getClass().getSimpleName()); /* Employee */
>         System.out.println(getClass().getSuperclass()); /* class java.lang.Object */
>     }
> }
> ```
> 
> **Використання в хеш-структурах:**
> 
> java
> 
> ```java
> Set<Employee> team = new HashSet<>();
> Employee e1 = new Employee(1, "Alice");
> Employee e2 = new Employee(1, "Alice copy"); /* той самий id */
> 
> team.add(e1);
> team.add(e2); /* НЕ додасться — equals і hashCode кажуть, що вони рівні */
> System.out.println(team.size()); /* 1 */
> ```
> 
> **Міжпотокова синхронізація через wait/notifyAll:**
> 
> java
> 
> ```java
> Object lock = new Object();
> 
> Thread producer = new Thread(() -> {
>     synchronized (lock) {
>         System.out.println("Дані готові");
>         lock.notifyAll(); /* будимо всіх, хто чекає на lock */
>     }
> });
> 
> Thread consumer = new Thread(() -> {
>     synchronized (lock) {
>         try {
>             lock.wait(); /* звільняємо монітор і чекаємо */
>             System.out.println("Отримали сигнал");
>         } catch (InterruptedException e) {
>             Thread.currentThread().interrupt();
>         }
>     }
> });
> 
> consumer.start();
> Thread.sleep(100);
> producer.start();
> ```

---

> [!warning] Обмеження та ризики
> 
> - **Порушення контракту `equals`/`hashCode`.** Перевизначення лише `equals` без `hashCode` (або навпаки) ламає `HashMap` та `HashSet`: об'єкт кладеться в одну "корзину", а шукається в іншій.
> - **`wait()` поза `synchronized`.** Виклик `wait()`, `notify()` або `notifyAll()` без утримання монітора об'єкта завжди кидає `IllegalMonitorStateException`.
> - **Spurious wakeup (фіктивне пробудження).** JVM може розбудити потік без виклику `notify()`. Тому `wait()` завжди слід розміщувати всередині `while`-циклу, а не `if`.
> - **`clone()` — поверхнева копія.** Для об'єктів із полями-колекціями або масивами необхідно вручну клонувати кожне таке поле, інакше оригінал і копія ділять спільний стан.
> - **`finalize()` — непередбачуваний.** GC не гарантує його виклику взагалі. Якщо `finalize()` кидає виняток — він мовчки ігнорується. Ніколи не використовуй для звільнення критичних ресурсів.
> - **`getClass()` vs `instanceof` в `equals`.** Використання `instanceof` замість `getClass()` порушує симетрію при наслідуванні: `child.equals(parent)` може повернути `true`, а `parent.equals(child)` — `false`.
> - **`notify()` vs `notifyAll()`.** `notify()` будить лише один потік — якщо він не може продовжити роботу (умова ще не виконана), решта потоків залишаться у сплячці назавжди. `notifyAll()` безпечніший у більшості сценаріїв.

---

> [!tip] Коли використовувати
> 
> **`equals` + `hashCode`** ✅:
> 
> - Value-об'єкти (DTO, Entity, Record)
> - Клас використовується як ключ у `HashMap` або елемент `HashSet`
> - Потрібна логічна, а не фізична рівність
> 
> **`toString`** ✅:
> 
> - Завжди перевизначай для зручності логування та дебагу
> 
> **`clone`** ❌:
> 
> - Надавай перевагу конструктору копіювання або фабричному методу
> - `Cloneable` вважається "broken API" в спільноті Java
> 
> **`wait/notify/notifyAll`** ❌ для нового коду:
> 
> - Використовуй `java.util.concurrent` (`ReentrantLock`, `Condition`, `BlockingQueue`)
> - `wait/notify` — низькорівневий механізм, схильний до помилок
> 
> **`finalize`** ❌:
> 
> - Не використовуй взагалі
> - Замінюй на `AutoCloseable` + `try-with-resources` або `java.lang.ref.Cleaner`

---
# Connections: