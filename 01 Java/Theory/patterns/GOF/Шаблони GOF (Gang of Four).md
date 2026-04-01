> **Шаблони GOF** — це набір із **23 шаблонів проектування**, запропонованих Еріхом Гаммою, Річардом Хелмом, Ральфом Джонсоном та Джоном Вліссідесом у книзі _"Design Patterns: Elements of Reusable Object-Oriented Software"_ (1994) — одній із ключових праць у сфері об'єктно-орієнтованого програмування.

---

> [!abstract] Core Framework 
> **23 шаблони GOF поділяються на три категорії:**
> 
> - **Creational Patterns** — управляють процесом створення об'єктів, забезпечуючи більш гнучкий підхід. _(Factory Method, Abstract Factory, Builder, Prototype, Singleton)_
> - **Structural Patterns** — визначають способи структурування класів та об'єктів у більш складних структурах. _(Adapter, Bridge, Composite, Decorator, Facade, Flyweight, Proxy)_
> - **Behavioral Patterns** — вирішують задачі взаємодії між об'єктами та розподілу обов'язків між ними. _(Chain of Responsibility, Command, Iterator, Mediator, Memento, Observer, State, Strategy, Template Method, Visitor)_

---

> [!info] Key Insights
> 
> - **Перевірені рішення.** Кожен з 23 шаблонів вирішує конкретну проблему в програмуванні, яка виникає знову і знову в різних проектах.
>     
> - **Повторне використання коду.** Шаблони підвищують повторне використання коду, забезпечуючи гнучкість і спрощуючи розробку.
>     
> - **Спільна мова.** GOF-шаблони формують спільний словник для розробників: сказати «тут потрібен Decorator» зрозуміліше, ніж пояснювати ідею з нуля.
>     
> - **Три ролі у кожному шаблоні.** Creational — _як створити_, Structural — _як скласти_, Behavioral — _як взаємодіяти_. Разом вони покривають повний lifecycle об'єктів.
>     

---
![[patterns.png]]

---
## Creational — Factory Method

Factory Method — фабричний метод, що визначається в базовому класі та **перевизначається у підкласах** для створення конкретних об'єктів. Клієнтський код працює з абстракцією і не залежить від конкретних класів.

```java
// Базовий клас для створення транспортного засобу
abstract class TransportCreator {
    // Фабричний метод для створення транспортного засобу
    abstract Transport createTransport();

    // Деякі інші методи, спільні для всіх підкласів
}

// Конкретний підклас, який реалізує фабричний метод
class CarCreator extends TransportCreator {
    // Перевизначення фабричного методу для створення автомобіля
    @Override
    Transport createTransport() {
        return new Car();
    }
}

// Конкретний підклас, який реалізує фабричний метод
class PlaneCreator extends TransportCreator {
    // Перевизначення фабричного методу для створення літака
    @Override
    Transport createTransport() {
        return new Plane();
    }
}

// Базовий клас для всіх видів транспортних засобів
interface Transport {
    void deliver();
}

// Конкретний клас автомобіля
class Car implements Transport {
    @Override
    public void deliver() {
        System.out.println("Доставка автомобілем");
    }
}

// Конкретний клас літака
class Plane implements Transport {
    @Override
    public void deliver() {
        System.out.println("Доставка літаком");
    }
}

public class Main {
    public static void main(String[] args) {
        // Створення автомобіля через фабричний метод CarCreator
        TransportCreator carCreator = new CarCreator();
        Transport car = carCreator.createTransport();
        car.deliver(); // Виведе: Доставка автомобілем

        // Створення літака через фабричний метод PlaneCreator
        TransportCreator planeCreator = new PlaneCreator();
        Transport plane = planeCreator.createTransport();
        plane.deliver(); // Виведе: Доставка літаком
    }
}

/*
Результат:
Доставка автомобілем
Доставка літаком
*/
```

---
## Structural — Decorator

Decorator — декоратор, що дозволяє **динамічно розширювати функціональність об'єкта** шляхом обгортання його в інший об'єкт-декоратор. Декоратори можна комбінувати у ланцюжок без зміни вихідного класу.

```java
// Базовий інтерфейс для компонентів, які можуть бути декоровані
interface Coffee {
    String getDescription();
    double getCost();
}

// Конкретна реалізація компонента - кава без декорацій
class SimpleCoffee implements Coffee {
    @Override
    public String getDescription() {
        return "Проста кава";
    }

    @Override
    public double getCost() {
        return 1.0;
    }
}

// Абстрактний декоратор
abstract class CoffeeDecorator implements Coffee {
    protected final Coffee decoratedCoffee;

    public CoffeeDecorator(Coffee decoratedCoffee) {
        this.decoratedCoffee = decoratedCoffee;
    }

    @Override
    public String getDescription() {
        return decoratedCoffee.getDescription();
    }

    @Override
    public double getCost() {
        return decoratedCoffee.getCost();
    }
}

// Конкретний декоратор - додає молоко до кави
class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee decoratedCoffee) {
        super(decoratedCoffee);
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", з молоком";
    }

    @Override
    public double getCost() {
        return super.getCost() + 0.5;
    }
}

// Конкретний декоратор - додає цукор до кави
class SugarDecorator extends CoffeeDecorator {
    public SugarDecorator(Coffee decoratedCoffee) {
        super(decoratedCoffee);
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", з цукром";
    }

    @Override
    public double getCost() {
        return super.getCost() + 0.2;
    }
}

public class Main {
    public static void main(String[] args) {
        // Створення простої кави
        Coffee simpleCoffee = new SimpleCoffee();
        System.out.println("Кава: " + simpleCoffee.getDescription());
        System.out.println("Ціна: " + simpleCoffee.getCost() + " грн");

        // Додавання молока до кави
        Coffee milkCoffee = new MilkDecorator(simpleCoffee);
        System.out.println("Кава з молоком: " + milkCoffee.getDescription());
        System.out.println("Ціна: " + milkCoffee.getCost() + " грн");

        // Додавання молока та цукру до кави
        Coffee sugarMilkCoffee = new SugarDecorator(milkCoffee);
        System.out.println("Кава з молоком і цукром: " + sugarMilkCoffee.getDescription());
        System.out.println("Ціна: " + sugarMilkCoffee.getCost() + " грн");
    }
}

/*
Результат:
Кава: Проста кава
Ціна: 1.0 грн
Кава з молоком: Проста кава, з молоком
Ціна: 1.5 грн
Кава з молоком і цукром: Проста кава, з молоком, з цукром
Ціна: 1.7 грн
*/
```

---
## Behavioral — Observer

Observer (Спостерігач) — шаблон, у якому **Subject** (Суб'єкт) підтримує список **Observers** (Спостерігачів) і автоматично сповіщає їх про зміну свого стану. Спостерігачів можна динамічно додавати та видаляти.

```java
import java.util.ArrayList;
import java.util.List;

// Інтерфейс для спостерігача
interface Observer {
    void update(String message);
}

// Конкретний спостерігач
class ConcreteObserver implements Observer {
    private String name;

    public ConcreteObserver(String name) {
        this.name = name;
    }

    @Override
    public void update(String message) {
        System.out.println(name + " отримав повідомлення: " + message);
    }
}

// Інтерфейс для суб'єкта
interface Subject {
    void registerObserver(Observer observer);
    void removeObserver(Observer observer);
    void notifyObservers(String message);
}

// Конкретний суб'єкт
class ConcreteSubject implements Subject {
    private List<Observer> observers = new ArrayList<>();

    @Override
    public void registerObserver(Observer observer) {
        observers.add(observer);
    }

    @Override
    public void removeObserver(Observer observer) {
        observers.remove(observer);
    }

    @Override
    public void notifyObservers(String message) {
        for (Observer observer : observers) {
            observer.update(message);
        }
    }

    public void doSomething() {
        // Якщо сталася якась подія, сповістити всіх спостерігачів
        notifyObservers("Подія відбулася");
    }
}

public class Main {
    public static void main(String[] args) {
        // Створюємо суб'єкт
        ConcreteSubject subject = new ConcreteSubject();

        // Створюємо спостерігачів
        Observer observer1 = new ConcreteObserver("Спостерігач 1");
        Observer observer2 = new ConcreteObserver("Спостерігач 2");

        // Реєструємо спостерігачів на суб'єкті
        subject.registerObserver(observer1);
        subject.registerObserver(observer2);

        // Суб'єкт виконує якусь дію, після якої сповіщає своїх спостерігачів
        subject.doSomething();

        // Видаляємо одного з спостерігачів
        subject.removeObserver(observer2);

        // Знову виконуємо дію, спостерігає лише один спостерігач
        subject.doSomething();
    }
}

/*
Результат:
Спостерігач 1 отримав повідомлення: Подія відбулася
Спостерігач 2 отримав повідомлення: Подія відбулася
Спостерігач 1 отримав повідомлення: Подія відбулася
*/
```

---

> [!example] Практичне застосування
> 
> **Factory Method** — фреймворки, де базова бібліотека визначає скелет алгоритму, а користувач підставляє конкретні реалізації. Наприклад, `CarCreator` та `PlaneCreator` не знають один про одного, але обидва реалізують один контракт `createTransport()`.
> 
> **Decorator** — Java I/O: `BufferedReader(new InputStreamReader(new FileInputStream(...)))` — класичний ланцюжок декораторів. Кожен шар додає поведінку (буферизацію, кодування) без зміни попереднього.
> 
> **Observer** — UI-події, системи сповіщень, event bus. Коли `Subject` (модель) змінюється, всі підписані `View` автоматично оновлюються — основа патерну MVC.

---

> [!warning] Обмеження та ризики
> 
> - **Factory Method вимагає підкласів.** Для кожного нового типу продукту потрібен окремий `Creator`-підклас. У великих системах кількість класів зростає швидко.
>     
> - **Decorator ускладнює відлагодження.** Глибокий ланцюжок декораторів важко трасувати: помилка може виникнути на будь-якому шарі, а стек-трейс виглядає як набір анонімних обгорток.
>     
> - **Observer — витоки пам'яті.** Якщо спостерігач зареєстрований, але не видалений через `removeObserver`, Subject утримує посилання і заважає GC очистити об'єкт.
>     
> - **Observer — порядок сповіщень не гарантований.** Якщо спостерігачі залежать від порядку виклику — це архітектурна помилка.
>     

---

> [!tip] Коли використовувати
> 
> **Factory Method** ✅:
> 
> - Заздалегідь невідомо, який конкретний клас потрібно створити
> - Розширення системи має відбуватись без зміни існуючого коду
> 
> **Decorator** ✅:
> 
> - Потрібно динамічно додавати поведінку об'єкту без зміни його класу
> - Наслідування дало б комбінаторний вибух підкласів
> 
> **Observer** ✅:
> 
> - Зміна одного об'єкта повинна автоматично оновлювати інші
> - Кількість залежних об'єктів заздалегідь невідома
> 
> **Будь-який GOF** ❌:
> 
> - Задача проста і патерн додає зайву абстракцію
> - Команда незнайома з патерном — читабельність постраждає

---

# Connections:
- [[Ієрархія Шаблонів Проектування GOF]]
- [[Шаблони проектування (Design Patterns)]]