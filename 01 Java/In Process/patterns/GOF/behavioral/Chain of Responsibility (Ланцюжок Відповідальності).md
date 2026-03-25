> **Ланцюжок відповідальності (Chain of Responsibility)** — це **поведінковий патерн**, який передає запит по **ланцюжку обробників**. Кожен обробник вирішує — обробити запит самостійно або передати його далі по ланцюгу.

---

> [!abstract] Core Framework 
> **Chain of Responsibility будує лінійний ланцюг із трьох елементів:**
> 
> - **Handler** — інтерфейс обробника з методом `handleRequest()` і посиланням на наступний обробник (`nextHandler`);
> - **ConcreteHandler** — реалізує логіку перевірки: якщо запит у зоні відповідальності — обробляє, інакше — делегує далі;
> - **Client** — збирає ланцюг і запускає перший обробник.

---

> [!info] Key Insights
> 
> - **Відокремлення відправника від отримувача.** Клієнт не знає, який саме обробник обробить запит — він просто запускає ланцюг.
>     
> - **Динамічна зміна ланцюга.** Обробники можна додавати, видаляти або переставляти без зміни клієнтського коду.
>     
> - **Порядок важливий.** Запит проходить обробників по черзі — перший, хто відповідає за діапазон, бере запит собі.
>     

---

> [!example] Практичне застосування
> 
> **Структура патерну:**
> 
> - `ConcreteHandler1` — обробляє суми до 1000, інакше передає далі;
> - `ConcreteHandler2` — обробляє суми від 1001 до 5000, інакше передає далі;
> - `ConcreteHandler3` — обробляє всі інші суми (кінець ланцюга);
> - `Client` — збирає ланцюг: `Handler1` → `Handler2` → `Handler3`.

```java
// Інтерфейс для обробників із посиланням на наступний
interface Handler {
    void setNext(Handler next); /* встановлює наступний обробник у ланцюгу */
    void handleRequest(int amount);
}

// Базовий абстрактний обробник — зберігає посилання на наступного
abstract class BaseHandler implements Handler {
    private Handler next;

    @Override
    public void setNext(Handler next) {
        this.next = next;
    }

    /* Передає запит наступному обробнику, якщо він існує */
    protected void passToNext(int amount) {
        if (next != null) {
            next.handleRequest(amount);
        } else {
            System.out.println("Жоден обробник не зміг обробити запит з сумою " + amount);
        }
    }
}

// Обробляє суми до 1000
class ConcreteHandler1 extends BaseHandler {
    private static final int THRESHOLD = 1000;

    @Override
    public void handleRequest(int amount) {
        if (amount <= THRESHOLD) {
            System.out.println("ConcreteHandler1 обробляє запит з сумою " + amount);
        } else {
            System.out.println("ConcreteHandler1 передає запит далі");
            passToNext(amount); /* делегуємо наступному */
        }
    }
}

// Обробляє суми від 1001 до 5000
class ConcreteHandler2 extends BaseHandler {
    private static final int THRESHOLD = 5000;

    @Override
    public void handleRequest(int amount) {
        if (amount > 1000 && amount <= THRESHOLD) {
            System.out.println("ConcreteHandler2 обробляє запит з сумою " + amount);
        } else {
            System.out.println("ConcreteHandler2 передає запит далі");
            passToNext(amount); /* делегуємо наступному */
        }
    }
}

// Обробляє всі суми понад 5000 — кінець ланцюга
class ConcreteHandler3 extends BaseHandler {
    @Override
    public void handleRequest(int amount) {
        System.out.println("ConcreteHandler3 обробляє запит з сумою " + amount);
    }
}

// Клас, що збирає і запускає ланцюжок обробників
class Client {
    private Handler chain;

    public Client() {
        buildChain();
    }

    private void buildChain() {
        /* Збираємо ланцюг: Handler1 → Handler2 → Handler3 */
        ConcreteHandler1 h1 = new ConcreteHandler1();
        ConcreteHandler2 h2 = new ConcreteHandler2();
        ConcreteHandler3 h3 = new ConcreteHandler3();

        h1.setNext(h2); /* h1 передає h2 */
        h2.setNext(h3); /* h2 передає h3 */

        chain = h1; /* точка входу — перший обробник */
    }

    public void makeRequest(int amount) {
        chain.handleRequest(amount);
    }
}

public class Main {
    public static void main(String[] args) {
        Client client = new Client();
        client.makeRequest(200);  // ConcreteHandler1 обробляє запит з сумою 200
        client.makeRequest(3000); // ConcreteHandler2 обробляє запит з сумою 3000
        client.makeRequest(7000); // ConcreteHandler3 обробляє запит з сумою 7000
    }
}
```

---

> [!warning] Обмеження та ризики
> 
> - **Запит може залишитися необробленим.** Якщо жоден обробник не взяв запит — він «провалюється» крізь ланцюг. Необхідно передбачати обробник за замовчуванням (як `ConcreteHandler3`).
>     
> - **Складність відлагодження.** При довгому ланцюгу важко простежити, який саме обробник взяв запит і чому.
>     

---

> [!tip] Коли використовувати
> 
> **Так** ✅:
> 
> - Набір обробників та їхній порядок має змінюватися динамічно
> - Потрібно обробляти запити різних типів або рівнів, не знаючи заздалегідь, який обробник підходить
> - Потрібно відокремити відправника запиту від конкретного отримувача
> 
> **Ні** ❌:
> 
> - Логіка обробки проста і не потребує ланцюга
> - Запит завжди має бути гарантовано оброблений конкретним класом

---
#behavioral #GOF #pattern
# Connections:
- [[Шаблони GOF (Gang of Four)]]