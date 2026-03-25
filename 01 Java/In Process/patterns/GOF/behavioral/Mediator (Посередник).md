>**Посередник (Mediator)** — це поведінковий патерн, який визначає об'єкт-посередника, що централізує комунікацію між компонентами системи, усуваючи прямі залежності між ними.

---

> [!abstract] Core Framework 
> **Mediator замінює мережу прямих зв'язків між об'єктами єдиним центральним вузлом:**
> 
> - **Mediator** — інтерфейс посередника з методом `sendMessage()`;
> - **ConcreteMediator** — реалізує логіку маршрутизації: отримує повідомлення від одного колеги і доставляє всім іншим;
> - **Colleague** — абстрактний клас учасника, що знає лише про посередника, але не про інших учасників;
> - **ConcreteColleague** — реалізує `send()` і `receiveMessage()`, спілкуючись виключно через Mediator.

---

> [!info] Key Insights
> 
> - **Зниження зв'язності.** Колеги не знають один про одного — вони знають лише про Mediator. Це перетворює граф залежностей із $O(n^2)$ зв'язків на $O(n)$.
>     
> - **Централізована логіка взаємодії.** Вся логіка координації зосереджена в Mediator — змінити правила взаємодії можна в одному місці.
>     
> - **Аналогія.** Як чат у месенджері: учасники не пишуть один одному напряму — вони пишуть у чат (Mediator), який доставляє повідомлення всім іншим.
>     

---

> [!example] Практичне застосування
> 
> **Структура патерну:**
> 
> - `Mediator` — інтерфейс із `sendMessage(String message, Colleague colleague)`;
> - `ConcreteMediator` — зберігає список колег, розсилає повідомлення всім окрім відправника;
> - `Colleague` — абстрактний клас із посиланням на `Mediator`;
> - `ConcreteColleague` — надсилає через `mediator.sendMessage()` і приймає через `receiveMessage()`.

```java
import java.util.ArrayList;
import java.util.List;

// Інтерфейс посередника
interface Mediator {
    void sendMessage(String message, Colleague colleague);
}

// Конкретний посередник
class ConcreteMediator implements Mediator {
    private List<Colleague> colleagues;

    ConcreteMediator() {
        this.colleagues = new ArrayList<>();
    }

    void addColleague(Colleague colleague) {
        colleagues.add(colleague);
    }

    @Override
    public void sendMessage(String message, Colleague colleague) {
        for (Colleague col : colleagues) {
            if (col != colleague) {
                col.receiveMessage(message); /* доставляємо всім, крім відправника */
            }
        }
    }
}

// Колега — знає лише про посередника
abstract class Colleague {
    protected Mediator mediator;

    Colleague(Mediator mediator) {
        this.mediator = mediator;
    }

    abstract void send(String message);

    abstract void receiveMessage(String message);
}

// Конкретний колега
class ConcreteColleague extends Colleague {
    ConcreteColleague(Mediator mediator) {
        super(mediator);
    }

    @Override
    void send(String message) {
        System.out.println("Відправляю повідомлення: " + message);
        mediator.sendMessage(message, this); /* передаємо через посередника */
    }

    @Override
    void receiveMessage(String message) {
        System.out.println("Отримано повідомлення: " + message);
    }
}

public class Main {
    public static void main(String[] args) {
        // Створення посередника
        ConcreteMediator mediator = new ConcreteMediator();

        // Створення колег
        Colleague colleague1 = new ConcreteColleague(mediator);
        Colleague colleague2 = new ConcreteColleague(mediator);

        // Додавання колег до посередника
        mediator.addColleague(colleague1);
        mediator.addColleague(colleague2);

        // Взаємодія колег через посередника
        colleague1.send("Привіт, як справи?");
        colleague2.send("Все добре, дякую!");
    }
}
```

---

> [!warning] Обмеження та ризики
> 
> - **Mediator може стати «Богом».** Якщо в системі багато компонентів, вся логіка взаємодії концентрується в одному класі, який стає складним і важким для підтримки.
>     
> - **Єдина точка відмови.** При помилці в Mediator уся комунікація між колегами порушується.
>     

---

> [!tip] Коли використовувати
> 
> **Так** ✅:
> 
> - Компоненти системи занадто сильно пов'язані між собою через прямі посилання
> - Потрібно централізувати складну логіку взаємодії між багатьма об'єктами
> - Зміна поведінки одного компонента вимагає змін у багатьох інших
> 
> **Ні** ❌:
> 
> - Компонентів мало і їхня взаємодія проста
> - Централізація створить надмірно складний Mediator

---
#behavioral #GOF #pattern
# Connections:
- [[Шаблони GOF (Gang of Four)]]