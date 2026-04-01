> **GRASP (ч.2)** розглядає три наступні принципи: **Low Coupling**, **High Cohesion** та **Polymorphism** — вони разом формують основу стабільної, гнучкої та розширюваної архітектури об'єктно-орієнтованих систем.

---

> [!abstract] Core Framework 
> **Три принципи другої частини GRASP:**
> 
> - **Low Coupling** — класи слабко залежать один від одного; зміна в одному не тягне каскадних змін у інших;
> - **High Cohesion** — клас виконує одну чітко визначену функцію; всі його методи пов'язані між собою;
> - **Polymorphism** — об'єкти різних класів можуть бути використані через спільний інтерфейс або батьківський клас, поводячись по-різному залежно від конкретного типу.

---

> [!info] Key Insights
> 
> - **Low Coupling через інтерфейс.** Залежність від абстракції (інтерфейсу), а не від конкретного класу — ключовий прийом зниження зчеплення. Нові реалізації додаються без зміни залежних класів.
>     
> - **High Cohesion — одна відповідальність.** Клас з високою когерентністю легше тестувати, підтримувати та розширювати, бо його методи утворюють єдину логічну групу навколо однієї цілі.
>     
> - **Polymorphism — диспетчеризація у рантаймі.** JVM визначає, який саме метод викликати, лише під час виконання (dynamic dispatch) — через таблицю віртуальних методів (vtable). Це і є основа поліморфізму в Java.
>     
> - **Low Coupling + High Cohesion = стійка архітектура.** Ці два принципи зазвичай розглядаються в парі: слабке зчеплення між модулями і сильна зв'язаність всередині модуля — ідеальна комбінація.
>     
> - **Polymorphism виключає `instanceof`.** Якщо код перевіряє тип об'єкта через `instanceof` і гілкується — це сигнал замінити умови поліморфізмом.
>     

---

## 4. Low Coupling (Низьке зчеплення)

Зменшуйте залежність між класами, максимально **розділяючи їх функціональність**. Зміни в одному класі не повинні призводити до змін у багатьох інших.

> [!example] Практичне застосування
> 
> `MessageService` залежить від інтерфейсу `MessageCreator`, а не від конкретних реалізацій. Нові типи повідомлень можна додавати без жодних змін у `MessageService`.
> 
> ```java
> import java.util.ArrayList;
> import java.util.List;
> 
> /* Інтерфейс для створення повідомлень.
>    MessageService знає лише про цей контракт —
>    не про конкретні класи, що його реалізують. */
> interface MessageCreator {
>     String createMessage();
> }
> 
> /* Конкретна реалізація — привітальне повідомлення */
> class GreetingMessageCreator implements MessageCreator {
>     @Override
>     public String createMessage() {
>         return "Привіт!";
>     }
> }
> 
> /* Конкретна реалізація — прощальне повідомлення */
> class FarewellMessageCreator implements MessageCreator {
>     @Override
>     public String createMessage() {
>         return "До побачення!";
>     }
> }
> 
> // Клас, який використовує створювачі повідомлень
> class MessageService {
>     /* Зберігаємо список абстракцій (MessageCreator),
>        а не конкретних класів — це і є Low Coupling */
>     private List<MessageCreator> creators = new ArrayList<>();
> 
>     /* Метод приймає будь-яку реалізацію MessageCreator */
>     public void addMessageCreator(MessageCreator creator) {
>         creators.add(creator);
>     }
> 
>     /* Ітерація по абстракціях — MessageService нічого
>        не знає про внутрішню логіку кожного creator */
>     public void displayMessages() {
>         for (MessageCreator creator : creators) {
>             System.out.println(creator.createMessage());
>         }
>     }
> }
> 
> public class Main {
>     public static void main(String[] args) {
>         MessageService service = new MessageService();
> 
>         /* Додаємо різні реалізації — MessageService не змінюється */
>         service.addMessageCreator(new GreetingMessageCreator());
>         service.addMessageCreator(new FarewellMessageCreator());
> 
>         service.displayMessages();
> 
>         /* Результат:
>            Привіт!
>            До побачення! */
>     }
> }
> ```

---

## 5. High Cohesion (Висока когерентність)

Концентруйте **пов'язані функції та дані** в одному класі. Клас повинен виконувати одну конкретну функцію, а всі його методи — бути спрямовані на цю ціль.

> [!example] Практичне застосування
> 
> Клас `PersonalDataManager` має **високу когерентність**: усі його методи — `addUser`, `deleteUser`, `getUsers` — обертаються навколо єдиної задачі керування персональними даними.
> 
> ```java
> import java.util.ArrayList;
> import java.util.List;
> 
> /* Клас для керування персональними даними користувачів.
>    Висока когерентність: усі методи пов'язані з однією ціллю —
>    зберіганням та маніпуляцією даними користувачів. */
> class PersonalDataManager {
>     private List<String> usernames;
>     private List<String> passwords;
>     private List<String> emails;
> 
>     public PersonalDataManager() {
>         usernames = new ArrayList<>();
>         passwords = new ArrayList<>();
>         emails = new ArrayList<>();
>     }
> 
>     /* Метод для додавання нового користувача.
>        Всі три списки змінюються синхронно — дані тримаються разом. */
>     public void addUser(String username, String password, String email) {
>         usernames.add(username);
>         passwords.add(password);
>         emails.add(email);
>     }
> 
>     /* Метод для видалення користувача за індексом */
>     public void deleteUser(int index) {
>         usernames.remove(index);
>         passwords.remove(index);
>         emails.remove(index);
>     }
> 
>     /* Метод для отримання списку користувачів у читабельному форматі.
>        Пароль навмисно не включається — клас відповідальний
>        за дані, але поважає приватність. */
>     public List<String> getUsers() {
>         List<String> users = new ArrayList<>();
>         for (int i = 0; i < usernames.size(); i++) {
>             users.add("Username: " + usernames.get(i) + ", Email: " + emails.get(i));
>         }
>         return users;
>     }
> }
> 
> public class Main {
>     public static void main(String[] args) {
>         PersonalDataManager manager = new PersonalDataManager();
> 
>         /* Додавання двох користувачів */
>         manager.addUser("john_doe", "password123", "john_doe@example.com");
>         manager.addUser("jane_smith", "password456", "jane_smith@example.com");
> 
>         /* Видалення другого користувача (індекс 1) */
>         manager.deleteUser(1);
> 
>         /* Виведення залишку користувачів */
>         List<String> users = manager.getUsers();
>         for (String user : users) {
>             System.out.println(user);
>         }
> 
>         /* Результат:
>            Username: john_doe, Email: john_doe@example.com */
>     }
> }
> ```

---

## 6. Polymorphism (Поліморфізм)

Використовуйте поліморфізм для реалізації **різноманітності об'єктів та відносин між ними**. Об'єкти різних класів можуть бути використані через спільний тип, поводячись по-різному залежно від свого реального типу.

> [!example] Практичне застосування
> 
> Масив типу `Shape[]` зберігає як `Circle`, так і `Rectangle`. При виклику `calculateArea()` JVM через механізм dynamic dispatch визначає, який саме метод запустити — круга чи прямокутника.
> 
> ```java
> /* Абстрактний батьківський клас для геометричних фігур.
>    abstract — не можна створити об'єкт Shape напряму,
>    лише через підкласи. */
> abstract class Shape {
>     /* abstract-метод — кожен підклас зобов'язаний
>        надати власну реалізацію */
>     public abstract double calculateArea();
> }
> 
> // Підклас круга
> class Circle extends Shape {
>     private double radius;
> 
>     public Circle(double radius) {
>         this.radius = radius;
>     }
> 
>     /* Площа круга: S = π * r²
>        Math.PI — константа з точністю до ~15 знаків */
>     @Override
>     public double calculateArea() {
>         return Math.PI * radius * radius;
>     }
> }
> 
> // Підклас прямокутника
> class Rectangle extends Shape {
>     private double length;
>     private double width;
> 
>     public Rectangle(double length, double width) {
>         this.length = length;
>         this.width = width;
>     }
> 
>     /* Площа прямокутника: S = a * b */
>     @Override
>     public double calculateArea() {
>         return length * width;
>     }
> }
> 
> public class Main {
>     public static void main(String[] args) {
>         /* Масив типу Shape[] — зберігає посилання на батьківський тип.
>            Фактичні об'єкти — Circle і Rectangle. */
>         Shape[] shapes = new Shape[2];
>         shapes[0] = new Circle(5);
>         shapes[1] = new Rectangle(3, 4);
> 
>         /* JVM під час виконання (runtime) визначає реальний тип
>            кожного об'єкта та викликає відповідний calculateArea().
>            Це і є dynamic dispatch — ядро поліморфізму в Java. */
>         for (Shape shape : shapes) {
>             System.out.println("Площа фігури: " + shape.calculateArea());
>         }
> 
>         /* Результат:
>            Площа фігури: 78.53981633974483   (π * 5² = π * 25)
>            Площа фігури: 12.0                (3 * 4) */
>     }
> }
> ```
> 
> Формули площ:
> 
> - Коло: $S = \pi r^2$
> - Прямокутник: $S = a \times b$

---

> [!warning] Обмеження та ризики
> 
> - **Low Coupling не означає «без залежностей».** Нульова залежність неможлива. Мета — залежати від **абстракцій**, а не від конкретних реалізацій.
>     
> - **High Cohesion може бути переоцінена.** Надмірне дроблення на дрібні класи («анемічна модель») теж шкодить — клас стає настільки малим, що архітектура стає незрозумілою.
>     
> - **Polymorphism і `instanceof`.** Використання `instanceof` для розгалуження логіки — антипатерн. Якщо бачиш `if (obj instanceof Circle)` — це сигнал замінити умову поліморфним методом.
>     
> - **Накладні витрати dynamic dispatch.** JVM оптимізує більшість викликів через JIT, але у критичних циклах ($O(n)$ з мільйонами ітерацій) варто враховувати, що кожен поліморфний виклик потребує пошуку в vtable.
>     

---

> [!tip] Коли використовувати
> 
> **Low Coupling** ✅:
> 
> - Є кілька реалізацій одного контракту (інтерфейсу)
> - Потрібна можливість підміни реалізації без зміни залежного класу
> - Пишеш бібліотеку або API, яке використовуватимуть інші
> 
> **High Cohesion** ✅:
> 
> - Клас розростається і методи починають стосуватись різних речей — час розбивати
> - Хочеш полегшити юніт-тестування: клас з однією відповідальністю тестується ізольовано
> 
> **Polymorphism** ✅:
> 
> - Є кілька типів об'єктів з однаковою поведінкою, але різною реалізацією
> - Хочеш позбутись довгих `if-else` або `switch` по типу об'єкта
> - Розширюваність важлива: нові типи додаються без зміни існуючого коду

---
#GRASP 
# Connections:
- [[Шаблони проектування GRASP (ч.1)]]
- [[Шаблони проектування GRASP (ч.3)]]