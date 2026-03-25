> **Легковаговик (Flyweight)** — це **структурний патерн**, який дозволяє **ефективно використовувати пам'ять** при роботі з великою кількістю схожих об'єктів, розділяючи між ними спільні (незмінні) дані замість зберігання дублікатів у кожному екземплярі.

---

> [!abstract] Core Framework 
> **Flyweight поділяє стан об'єкта на два типи:**
> 
> - **Внутрішній стан (intrinsic)** — незмінні дані, що поділяються між об'єктами і зберігаються у Flyweight (`character` у `ConcreteFlyweight`);
> - **Зовнішній стан (extrinsic)** — контекстні дані, що передаються ззовні при кожному виклику і не зберігаються у Flyweight;
> - **FlyweightFactory** — фабрика-кеш, що повертає існуючий екземпляр або створює новий, якщо його ще немає.

---

> [!info] Key Insights
> 
> - **Пул об'єктів.** `FlyweightFactory` зберігає вже створені екземпляри у `Map` — повторний запит того самого символу повертає вже існуючий об'єкт, а не створює новий.
>     
> - **Економія пам'яті.** Замість $N$ окремих об'єктів для $N$ однакових символів у пам'яті існує лише один об'єкт на кожен унікальний символ.
>     
> - **Незмінність внутрішнього стану.** Flyweight-об'єкти мають бути незмінними (immutable), оскільки вони поділяються між різними клієнтами.
>     

---

> [!example] Практичне застосування
> 
> **Структура патерну:**
> 
> - `Flyweight` — інтерфейс із методом `print(String character)`;
> - `ConcreteFlyweight` — зберігає символ як внутрішній стан, виводить його при виклику `print()`;
> - `FlyweightFactory` — `HashMap`-кеш, що видає унікальні екземпляри по ключу-символу;
> - `Main` — ітерує рядок, запитує Flyweight для кожного символу через фабрику.

```java
import java.util.HashMap;
import java.util.Map;

// Інтерфейс легковаговика
interface Flyweight {
    void print(String character);
}

// Конкретний легковаговик
class ConcreteFlyweight implements Flyweight {
    private String character;

    public ConcreteFlyweight(String character) {
        this.character = character;
    }

    public void print(String character) {
        System.out.print(this.character);
    }
}

// Фабрика легковаговиків
class FlyweightFactory {
    private Map<String, Flyweight> flyweights = new HashMap<>();

    public Flyweight getFlyweight(String character) {
        if (!flyweights.containsKey(character)) {
            flyweights.put(character, new ConcreteFlyweight(character));
        }
        return flyweights.get(character);
    }
}

public class Main {
    public static void main(String[] args) {
        FlyweightFactory factory = new FlyweightFactory();

        String text = "Привіт, світ!";
        for (char c : text.toCharArray()) {
            Flyweight flyweight = factory.getFlyweight(String.valueOf(c));
            flyweight.print(String.valueOf(c));
        }
    }
}
```

---

> [!warning] Обмеження та ризики
> 
> - **Ускладнення коду.** Розділення стану на внутрішній та зовнішній додає складності — зовнішній стан треба передавати явно при кожному виклику.
>     
> - **Незмінність обов'язкова.** Якщо Flyweight-об'єкт буде змінено одним клієнтом — це вплине на всіх інших, хто його використовує.
>     
> - **Виграш тільки при великій кількості об'єктів.** Для невеликої кількості екземплярів патерн додає складність без реальної економії пам'яті.
>     

---

> [!tip] Коли використовувати
> 
> **Так** ✅:
> 
> - Система створює дуже велику кількість об'єктів ($10^4+$), що спричиняє нестачу пам'яті
> - Більша частина стану об'єкта може бути винесена назовні як зовнішній стан
> - Після виділення спільного стану більшість об'єктів стають ідентичними
> 
> **Ні** ❌:
> 
> - Кількість об'єктів невелика і пам'ять не є обмеженням
> - Об'єкти мають унікальний стан, який не можна розділити

---
#structual #GOF #pattern
# Connections:
- [[Шаблони GOF (Gang of Four)]]