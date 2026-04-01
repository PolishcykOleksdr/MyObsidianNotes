> **Команда (Command)** — це поведінковий патерн, який інкапсулює запит як об'єкт, дозволяючи параметризувати виконавців різними командами, ставити команди в чергу, логувати їх та реалізовувати операції скасування/повторення.

---

> [!abstract] Core Framework 
> **Command розриває пряме з'єднання між ініціатором і виконавцем через чотири ролі:**
> 
> - **Command** — інтерфейс із методом `execute()`;
> - **ConcreteCommand** — реалізує `execute()`, тримає посилання на `Receiver` і викликає на ньому потрібну дію;
> - **Receiver** — об'єкт, що містить реальну бізнес-логіку (`action()`);
> - **Invoker** — ініціатор, що зберігає команду і викликає `execute()` у потрібний момент.

---

> [!info] Key Insights
> 
> - **Інкапсуляція дії.** Команда — це об'єкт, що несе в собі все необхідне для виконання: що робити (`Receiver`) і як (`action()`). Invoker нічого не знає про деталі.
>     
> - **Відокремлення Invoker від Receiver.** Invoker лише викликає `execute()` — він не знає, хто є Receiver і яка операція виконується.
>     
> - **Розширюваність.** Нові команди додаються як нові класи, що реалізують `Command`, без змін у `Invoker` чи `Receiver`.
>     

---

> [!example] Практичне застосування
> 
> **Структура патерну:**
> 
> - `Command` — інтерфейс із методом `execute()`;
> - `Receiver` — клас із методом `action()`, що містить реальну логіку;
> - `ConcreteCommand` — зв'язує `Receiver` з конкретною дією;
> - `Invoker` — зберігає команду і запускає її через `executeCommand()`.

```java
// Інтерфейс команди
interface Command {
    void execute();
}

// Конкретна команда
class ConcreteCommand implements Command {
    private Receiver receiver;

    ConcreteCommand(Receiver receiver) {
        this.receiver = receiver;
    }

    @Override
    public void execute() {
        receiver.action(); /* делегуємо реальну логіку отримувачу */
    }
}

// Отримувач — містить реальну бізнес-логіку
class Receiver {
    void action() {
        System.out.println("Виконується операція");
    }
}

// Виконавець — не знає деталей команди, лише викликає execute()
class Invoker {
    private Command command;

    void setCommand(Command command) {
        this.command = command;
    }

    void executeCommand() {
        command.execute();
    }
}

public class Main {
    public static void main(String[] args) {
        // Створення отримувача
        Receiver receiver = new Receiver();

        // Створення команди з використанням отримувача
        Command command = new ConcreteCommand(receiver);

        // Створення виконавця та надання йому команди
        Invoker invoker = new Invoker();
        invoker.setCommand(command);

        // Виклик команди
        invoker.executeCommand();
    }
}
```

---

> [!warning] Обмеження та ризики
> 
> - **Збільшення кількості класів.** Кожна нова операція — це новий клас команди. При великій кількості операцій проєкт наповнюється безліччю дрібних класів.
>     
> - **Складність реалізації Undo/Redo.** Для скасування операцій кожна команда має зберігати попередній стан або реалізовувати метод `undo()`, що ускладнює реалізацію.
>     

---

> [!tip] Коли використовувати
> 
> **Так** ✅:
> 
> - Потрібно параметризувати об'єкти операціями або ставити операції в чергу
> - Потрібна підтримка скасування (`undo`) та повторення (`redo`) операцій
> - Потрібно логувати або відкладати виконання операцій
> 
> **Ні** ❌:
> 
> - Операція проста і не потребує інкапсуляції в окремий клас
> - Скасування та черга операцій не потрібні

---
#behavioral #GOF #pattern
# # Connections:
- [[Шаблони GOF (Gang of Four)]]]]