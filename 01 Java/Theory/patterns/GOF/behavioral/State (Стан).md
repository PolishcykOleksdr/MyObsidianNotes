> **State** — поведінковий патерн, що дозволяє об'єкту змінювати свою поведінку залежно від внутрішнього стану. Зовні це виглядає так, ніби об'єкт змінив свій клас.

---

> [!abstract] Core Framework 
> **Патерн State будується на трьох ролях:**
> 
> - **State (інтерфейс)** — визначає набір методів, які реалізує кожен конкретний стан.
> - **ConcreteState** — конкретна реалізація стану з власною поведінкою для кожного методу.
> - **Context** — зберігає посилання на поточний стан і делегує йому всі виклики методів.

---

> [!info] Key Insights
> 
> - **Поліморфізм замість умовних операторів.** Замість громіздких `if/else` або `switch` по стану, кожен стан інкапсулюється в окремий клас — контекст просто викликає метод на поточному об'єкті-стані.
>     
> - **Зміна класу під час виконання.** Виклик `setState()` на `VendingMachine` фактично замінює об'єкт, що обробляє запити — поведінка змінюється миттєво.
>     
> - **Кожен стан відповідає за свою поведінку.** `ReadyState` і `WaitForSelectionState` по-різному реагують на одні й ті самі методи (`insertCoin`, `ejectCoin`, `dispense`) — логіка не розмазана по контексту.
>     
> - **Розширюваність.** Новий стан — це новий клас, що реалізує інтерфейс `State`. Існуючий код контексту не змінюється.
>     

---

> [!example] Практичне застосування
> 
> **Автомат з напоями** — об'єкт з двома станами:
> 
> - `ReadyState` — автомат очікує на монету: `insertCoin()` спрацьовує, `dispense()` відмовляє
> - `WaitForSelectionState` — монета вставлена: `dispense()` спрацьовує, `insertCoin()` повідомляє про дублювання

---

## Реалізація

```java
// Клас, що представляє стан автомата з напоями
interface State {
    void insertCoin();
    void ejectCoin();
    void dispense();
}

// Конкретний стан: автомат готовий до продажу
class ReadyState implements State {
    @Override
    public void insertCoin() {
        System.out.println("Монета вставлена.");
    }

    @Override
    public void ejectCoin() {
        System.out.println("Монета відхилена.");
    }

    @Override
    public void dispense() {
        System.out.println("Напій не може бути вибраний без вставлення монети.");
    }
}

// Конкретний стан: автомат очікує вибір напою
class WaitForSelectionState implements State {
    @Override
    public void insertCoin() {
        System.out.println("Монета вже вставлена.");
    }

    @Override
    public void ejectCoin() {
        System.out.println("Монета відхилена.");
    }

    @Override
    public void dispense() {
        System.out.println("Виберіть напій.");
    }
}

// Клас автомата з напоями
class VendingMachine {
    private State currentState;

    public VendingMachine() {
        currentState = new ReadyState();
    }

    public void setState(State state) {
        currentState = state;
    }

    public void insertCoin() {
        currentState.insertCoin();
    }

    public void ejectCoin() {
        currentState.ejectCoin();
    }

    public void dispense() {
        currentState.dispense();
    }
}

public class Main {
    public static void main(String[] args) {
        VendingMachine vendingMachine = new VendingMachine();

        vendingMachine.insertCoin(); // Монета вставлена.
        vendingMachine.ejectCoin();  // Монета відхилена.
        vendingMachine.dispense();   // Напій не може бути вибраний без вставлення монети.

        vendingMachine.setState(new WaitForSelectionState());

        vendingMachine.insertCoin(); // Монета вже вставлена.
        vendingMachine.ejectCoin();  // Монета відхилена.
        vendingMachine.dispense();   // Виберіть напій.
    }
}

/* Результат:
   Монета вставлена.
   Монета відхилена.
   Напій не може бути вибраний без вставлення монети.
   Монета вже вставлена.
   Монета відхилена.
   Виберіть напій.
*/
```

---

> [!warning] Обмеження та ризики
> 
> - **Вибух кількості класів.** При $n$ станах отримуємо $n$ класів. Для простих випадків з 2–3 станами `enum` + `switch` може бути читабельнішим рішенням.
>     
> - **Управління переходами.** У прикладі вище `setState()` викликається ззовні — у реальних системах переходи між станами краще інкапсулювати всередині самих класів-станів, передаючи їм посилання на контекст.
>     
> - **Розподілена логіка.** Поведінка системи розподілена між кількома класами-станами, що може ускладнити розуміння загального потоку без діаграми переходів.
>     

---

> [!tip] Коли використовувати
> 
> **Так** ✅:
> 
> - Об'єкт має чітко визначені стани з різною поведінкою
> - Є багато умовних операторів, що перевіряють поточний стан
> - Переходи між станами мають власну логіку
> 
> **Ні** ❌:
> 
> - Станів лише 2–3 і логіка проста — `if/else` буде зрозумілішим
> - Стани не мають суттєво відмінної поведінки
> - Потрібна висока продуктивність — кожен перехід створює новий об'єкт

---
#behavioral #GOF #pattern
# Connections:
- [[Шаблони GOF (Gang of Four)]]