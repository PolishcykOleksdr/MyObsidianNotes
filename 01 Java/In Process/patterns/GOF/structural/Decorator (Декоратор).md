> **Декоратор (Decorator)** — це **структурний патерн**, який дозволяє **динамічно додавати нову функціональність** об'єктам, обгортаючи їх у спеціальні класи-декоратори, зберігаючи при цьому оригінальний інтерфейс незмінним.

---

> [!abstract] Core Framework 
> **Decorator розширює поведінку через ланцюжок обгорток:**
> 
> - **Component** — інтерфейс, спільний для базового об'єкта і всіх декораторів (`Coffee`);
> - **ConcreteComponent** — базова реалізація без жодних розширень (`SimpleCoffee`);
> - **BaseDecorator** — абстрактний декоратор, що тримає посилання на обгорнутий компонент (`CoffeeDecorator`);
> - **ConcreteDecorator** — конкретна обгортка, що додає нову поведінку (`MilkDecorator`).

---

> [!info] Key Insights
> 
> - **Динамічне розширення.** Нова поведінка додається в рантаймі шляхом обгортання об'єкта, а не через успадкування на етапі компіляції.
>     
> - **Ланцюжок декораторів.** Декоратори можна накладати один на одного в будь-якій комбінації та кількості — кожен делегує виклик попередньому і додає свою частину.
>     
> - **Прозорість для клієнта.** Обгорнутий об'єкт і декоратор реалізують однаковий інтерфейс, тому клієнт працює з ними ідентично.
>     

---

> [!example] Практичне застосування
> 
> **Структура патерну:**
> 
> - `Coffee` — інтерфейс із методами `getDescription()` і `cost()`;
> - `SimpleCoffee` — базова кава за 30 грн;
> - `CoffeeDecorator` — абстрактний декоратор, делегує виклики обгорнутому об'єкту;
> - `MilkDecorator` — додає `", молоко"` до опису і `+10` до ціни.

```java
// Компонент - інтерфейс, що описує базовий об'єкт та його метод
interface Coffee {
    String getDescription();
    double cost();
}

// Конкретна реалізація компонента
class SimpleCoffee implements Coffee {
    @Override
    public String getDescription() {
        return "Кава";
    }

    @Override
    public double cost() {
        return 30; // Ціна простої кави
    }
}

// Базовий декоратор
abstract class CoffeeDecorator implements Coffee {
    protected Coffee decoratedCoffee;

    public CoffeeDecorator(Coffee decoratedCoffee) {
        this.decoratedCoffee = decoratedCoffee;
    }

    @Override
    public String getDescription() {
        return decoratedCoffee.getDescription();
    }

    @Override
    public double cost() {
        return decoratedCoffee.cost();
    }
}

// Додаткова добавка - декоратор
class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee decoratedCoffee) {
        super(decoratedCoffee);
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", молоко";
    }

    @Override
    public double cost() {
        return super.cost() + 10; // Додати вартість молока
    }
}

public class Main {
    public static void main(String[] args) {
        // Створення базової кави
        Coffee coffee = new SimpleCoffee();
        System.out.println("Опис: " + coffee.getDescription() + ", Ціна: " + coffee.cost() + " грн");

        // Додавання молока до кави
        coffee = new MilkDecorator(coffee);
        System.out.println("Опис: " + coffee.getDescription() + ", Ціна: " + coffee.cost() + " грн");
    }
}
```

---

> [!warning] Обмеження та ризики
> 
> - **Складність відлагодження.** Глибокий ланцюжок декораторів важко простежити у стек-трейсі — виклик проходить через кілька рівнів обгорток.
>     
> - **Велика кількість дрібних класів.** При великій кількості комбінацій декораторів проєкт наповнюється безліччю схожих класів, що ускладнює навігацію.
>     
> - **Порядок декораторів має значення.** Різний порядок обгортання може давати різний результат — це не завжди очевидно.
>     

---

> [!tip] Коли використовувати
> 
> **Так** ✅:
> 
> - Потрібно гнучко додавати або прибирати функціональність об'єкта в рантаймі
> - Розширення через успадкування призведе до вибуху кількості підкласів
> - Потрібні різні комбінації поведінок для одного базового об'єкта
> 
> **Ні** ❌:
> 
> - Комбінацій небагато і вони стабільні — простіше зробити кілька підкласів
> - Порядок застосування розширень не є важливим і не контролюється

---
#structual #GOF #pattern
# Connections:
- [[Шаблони GOF (Gang of Four)]]