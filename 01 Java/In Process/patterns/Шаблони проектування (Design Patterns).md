> **Шаблони проектування (Design Patterns)** — це **загальні рецепти або шаблони** для розв'язання типових проблем у розробці програмного забезпечення. Вони не є конкретними реалізаціями або готовими бібліотеками, а **набором правил та ідей**, які можна використовувати для створення власних рішень.

---

> [!abstract] Core Framework 
> **Шаблони проектування поділяються на три основні категорії:**
> 
> - **Шаблони створення (Creational Patterns)** — допомагають у створенні об'єктів з мінімальним залученням конкретних класів, підтримуючи гнучку архітектуру.
> - **Шаблони структури (Structural Patterns)** — описують способи композиції об'єктів для утворення більших структур, що визначають ієрархії класів або об'єднують об'єкти для реалізації нового функціоналу.
> - **Шаблони поведінки (Behavioral Patterns)** — описують способи організації співпраці між об'єктами, що дозволяє забезпечити взаємодію між ними без зайвого зв'язку.
> 
> **Приклади шаблонів:** Singleton, Factory, Builder _(створення)_ · Adapter, Decorator, Composite _(структура)_ · Observer, Strategy, Template Method _(поведінка)_

---

> [!info] Key Insights
> 
> - **Реюзабельність.** Шаблони надають загальний підхід до розв'язання типових проблем, що дозволяє використовувати їх у різних контекстах та проектах.
>     
> - **Розширюваність.** Добре розроблені шаблони дозволяють легко вносити зміни в систему, зберігаючи її структуру. Нові стратегії чи алгоритми можна додавати без зміни клієнтського коду.
>     
> - **Структура коду.** Шаблони сприяють структурованню програмного коду, що полегшує його розуміння та підтримку. Логічний поділ на `model`, `service`, `dao` — прямий наслідок застосування патернів.
>     

---
## Принцип 1 — Реюзабельність: Singleton

Singleton — шаблон створення, що гарантує існування **тільки одного екземпляру** класу, доступного з будь-якої точки програми. Використовується для кешу, пулу з'єднань або конфігураційного менеджера.

```java
public class Singleton {
    private static Singleton instance;

    // Приватний конструктор, щоб уникнути створення об'єктів ззовні класу
    private Singleton() {}

    // Метод для отримання єдиного екземпляру класу
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }

    // Додаткові методи класу
    public void showMessage() {
        System.out.println("Hello, I am a Singleton instance!");
    }
}
```

---

## Принцип 2 — Розширюваність: Strategy

Strategy — шаблон поведінки, що визначає **сімейство алгоритмів**, упакованих у вигляді окремих об'єктів. Дозволяє замінювати один алгоритм іншим під час виконання програми без впливу на клієнтський код.

```java
// Інтерфейс для всіх конкретних алгоритмів
interface SortingStrategy {
    void sort(int[] array);
}

// Конкретна реалізація алгоритму сортування "Бульбашкою"
class BubbleSort implements SortingStrategy {
    @Override
    public void sort(int[] array) {
        // Реалізація алгоритму сортування "Бульбашкою"
        System.out.println("Sorting array using Bubble Sort algorithm");
    }
}

// Конкретна реалізація алгоритму сортування "Швидке сортування"
class QuickSort implements SortingStrategy {
    @Override
    public void sort(int[] array) {
        // Реалізація алгоритму швидкого сортування
        System.out.println("Sorting array using Quick Sort algorithm");
    }
}

// Клас контексту, який використовує вибраний алгоритм сортування
class SortingContext {
    private SortingStrategy strategy;

    public void setStrategy(SortingStrategy strategy) {
        this.strategy = strategy;
    }

    public void performSort(int[] array) {
        // Виклик вибраного алгоритму сортування
        strategy.sort(array);
    }
}

public class StrategyPatternExample {
    public static void main(String[] args) {
        int[] array = {5, 2, 8, 3, 1, 9};

        SortingContext context = new SortingContext();

        // Використання алгоритму сортування "Бульбашкою"
        context.setStrategy(new BubbleSort());
        context.performSort(array);

        // Заміна алгоритму сортування на "Швидке сортування"
        context.setStrategy(new QuickSort());
        context.performSort(array);
    }
}
```

---

## Принцип 3 — Структура коду: Model / Service / DAO

Шаблони проектування диктують **логічну організацію пакетів** у Java-проекті. Типова структура Maven-проекту:

```
project
│
├── src
│   ├── main
│   │   ├── java
│   │   │   └── com
│   │   │       └── example
│   │   │           ├── model         // Пакет з класами, що визначають моделі даних
│   │   │           │   ├── User.java
│   │   │           │   └── Product.java
│   │   │           ├── service       // Пакет з бізнес-логікою та сервісами
│   │   │           │   ├── UserService.java
│   │   │           │   └── ProductService.java
│   │   │           ├── dao           // Пакет з класами доступу до даних (Data Access Objects)
│   │   │           │   ├── UserDao.java
│   │   │           │   └── ProductDao.java
│   │   │           └── Main.java     // Головний клас додатку
│   │   │
│   │   └── resources
│   │       ├── config                // Конфігураційні файли
│   │       │   └── application.properties
│   │       └── static                // Статичні ресурси (HTML, CSS, JavaScript)
│   │           └── index.html
│   │
│   └── test                         // Тестові класи
│       └── java
│           └── com
│               └── example
│                   ├── model
│                   │   ├── UserTest.java
│                   │   └── ProductTest.java
│                   ├── service
│                   │   ├── UserServiceTest.java
│                   │   └── ProductServiceTest.java
│                   └── dao
│                       ├── UserDaoTest.java
│                       └── ProductDaoTest.java
│
└── pom.xml                          // Файл конфігурації Maven
```

Реалізація шаблонів Model, DAO та Service на практиці:

```java
// Пакет з класами, що визначають моделі даних
package com.example.model;

// Клас моделі користувача
public class User {
    private String username;
    private String email;

    // Конструктор, геттери та сеттери
}

// Клас моделі продукту
public class Product {
    private String name;
    private double price;

    // Конструктор, геттери та сеттери
}

// Пакет з класами доступу до даних (Data Access Objects)
package com.example.dao;

// Інтерфейс DAO для роботи з користувачами
public interface UserDao {
    User findById(int id);
    void save(User user);
}

// Інтерфейс DAO для роботи з продуктами
public interface ProductDao {
    Product findById(int id);
    void save(Product product);
}

// Пакет з класами для бізнес-логіки та сервісів
package com.example.service;

// Інтерфейс для сервісу користувачів
public interface UserService {
    void registerUser(User user);
    User findUserById(int id);
}

// Імплементація сервісу користувачів
public class UserServiceImpl implements UserService {
    private UserDao userDao;

    // Конструктор, ініціалізація userDao

    // Реалізація методів інтерфейсу UserService
}

// Інтерфейс для сервісу продуктів
public interface ProductService {
    void addProduct(Product product);
    Product findProductById(int id);
}

// Імплементація сервісу продуктів
public class ProductServiceImpl implements ProductService {
    private ProductDao productDao;

    // Конструктор, ініціалізація productDao

    // Реалізація методів інтерфейсу ProductService
}

// Головний клас додатку
package com.example;

public class Main {
    public static void main(String[] args) {
        // Тут можна ініціалізувати сервіси та викликати їх методи
    }
}
```

---

> [!example] Практичне застосування
> 
> **Singleton** — кеш, пул з'єднань з базою даних, конфігураційний менеджер: будь-який ресурс, де потрібен рівно один глобальний об'єкт.
> 
> **Strategy** — підміна алгоритмів сортування, стратегій оплати або маршрутизації без зміни клієнтського коду. Контекст (`SortingContext`) не знає, яку реалізацію використовує — він лише викликає `strategy.sort(array)`.
> 
> **DAO + Service** — розділення відповідальностей: `UserDao` займається лише SQL-запитами, `UserService` — бізнес-логікою. Тести для кожного шару пишуться незалежно.

---

> [!warning] Обмеження та ризики
> 
> - **Singleton — прихований глобальний стан.** Якщо екземпляр мутується з різних потоків, необхідна синхронізація (`synchronized` або `volatile`). Наведена реалізація не є потокобезпечною.
>     
> - **Strategy збільшує кількість класів.** Кожен алгоритм — окремий клас. У великих системах це може ускладнити навігацію по коду.
>     
> - **Шаблони — не срібна куля.** Вони вирішують типові проблеми, але надмірне їх застосування (over-engineering) робить просту задачу складнішою, ніж вона є.
>     

---

> [!tip] Коли використовувати
> 
> **Так** ✅:
> 
> - Є типова проблема, яку шаблон розв'язує напряму (один екземпляр, сімейство алгоритмів, ієрархія об'єктів)
> - Система повинна бути розширена без зміни існуючого коду
> - Потрібна чітка структура для командної розробки
> 
> **Ні** ❌:
> 
> - Задача проста і шаблон додасть зайву складність
> - Команда незнайома з патерном — це знизить читабельність
> - Реалізація «заради патерну» без реальної необхідності

---
# Connections:
- [[Шаблони GOF (Gang of Four)]]
- [[Ієрархія Шаблонів Проектування GOF]]