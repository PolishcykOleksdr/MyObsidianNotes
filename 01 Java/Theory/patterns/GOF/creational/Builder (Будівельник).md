> **Будівельник (Builder)** — це **породжувальний патерн**, який **відокремлює процес конструювання** складного об'єкта від його кінцевого представлення, дозволяючи одному й тому самому процесу побудови створювати різні варіанти об'єктів.

---

> [!abstract] Core Framework 
> **Builder розділяє відповідальність між чотирма учасниками:**
> 
> - **Director** — керує порядком викликів будівельника, не знаючи деталей реалізації;
> - **Builder** (інтерфейс) — оголошує кроки конструювання, спільні для всіх варіантів;
> - **ConcreteBuilder** — реалізує кроки і накопичує стан об'єкта, що будується;
> - **Product** — фінальний складний об'єкт, який є результатом побудови.

---

> [!info] Key Insights
> 
> - **Покроковість.** Об'єкт будується поступово — крок за кроком — що дозволяє контролювати кожен етап і варіювати результат.
>     
> - **Відокремлення Director від Product.** Director знає лише послідовність кроків, але не знає, який саме клас будується — це дозволяє легко підміняти ConcreteBuilder.
>     
> - **Метод `reset()`.** Скидання стану будівельника перед кожною новою побудовою гарантує, що залишки попереднього об'єкта не потраплять у наступний.
>     

---

> [!example] Практичне застосування
> 
> **Структура патерну:**
> 
> - `Director` — керує процесом через метод `constructProduct()`;
> - `Builder` — інтерфейс з методами `reset()`, `setPartA()`, `setPartB()`, `setPartC()`;
> - `ConcreteBuilder` — реалізує Builder і накопичує частини в `Product`;
> - `Product` — містить список частин і вміє їх виводити.

```java
// Клас, який будує об'єкт (Директор)
public class Director {
    private Builder builder;

    // Конструктор, який приймає будівельника
    public Director(Builder builder) {
        this.builder = builder;
    }

    // Метод, що керує процесом конструювання об'єкта
    public void constructProduct() {
        builder.reset(); // Спочатку скидаємо стан будівельника
        builder.setPartA("Частина А"); // Додаємо першу складову частину
        builder.setPartB("Частина Б"); // Додаємо другу складову частину
        builder.setPartC("Частина В"); // Додаємо третю складову частину
    }
}

// Абстрактний будівельник
public interface Builder {
    void reset();
    void setPartA(String partA);
    void setPartB(String partB);
    void setPartC(String partC);
}

// Конкретний будівельник, який будує об'єкт
public class ConcreteBuilder implements Builder {
    private Product product;

    // Конструктор, який створює об'єкт, який будується
    public ConcreteBuilder() {
        reset();
    }

    // Скидає стан будівельника
    public void reset() {
        product = new Product();
    }

    // Встановлює першу складову частину
    public void setPartA(String partA) {
        product.addPart(partA);
    }

    // Встановлює другу складову частину
    public void setPartB(String partB) {
        product.addPart(partB);
    }

    // Встановлює третю складову частину
    public void setPartC(String partC) {
        product.addPart(partC);
    }

    // Повертає побудований об'єкт
    public Product getProduct() {
        return product;
    }
}

// Клас, який будується
public class Product {
    private List<String> parts = new ArrayList<>();

    // Додає нову складову частину до об'єкта
    public void addPart(String part) {
        parts.add(part);
    }

    // Виводить усі складові частини у консоль
    public void showParts() {
        System.out.println("Складові частини продукту:");
        for (String part : parts) {
            System.out.println(part);
        }
    }
}

public class Main {
    public static void main(String[] args) {
        ConcreteBuilder builder = new ConcreteBuilder(); // Створюємо будівельника
        Director director = new Director(builder); // Створюємо директора і передаємо йому будівельника

        director.constructProduct(); // Керуємо процесом конструювання об'єкта
        Product product = builder.getProduct(); // Отримуємо побудований об'єкт

        product.showParts(); // Виводимо усі складові частини у консоль
    }
}
```

---

> [!warning] Обмеження та ризики
> 
> - **Надлишкова складність для простих об'єктів.** Якщо продукт має 2–3 поля — патерн додає зайві класи без реальної користі.
>     
> - **Зв'язаність Director і Builder.** Director залежить від конкретного набору методів Builder, тому зміна інтерфейсу будівельника вимагає змін і в Director.
>     

---

> [!tip] Коли використовувати
> 
> **Так** ✅:
> 
> - Об'єкт має багато параметрів або складний процес ініціалізації
> - Потрібно будувати різні варіанти одного і того самого об'єкта
> - Потрібно чітко відокремити логіку конструювання від бізнес-логіки самого об'єкта
> 
> **Ні** ❌:
> 
> - Об'єкт простий і має фіксований набір полів
> - Варіантів конфігурації об'єкта немає або вони мінімальні

---
#creational #GOF #pattern
# Connections:
- [[Шаблони GOF (Gang of Four)]]