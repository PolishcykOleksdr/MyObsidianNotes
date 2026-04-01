> **Interpreter** — поведінковий патерн, що визначає граматику мови та надає інтерпретатор для обробки речень цієї мови. Кожне правило граматики представлено окремим класом.

---

> [!abstract] Core Framework 
> **Патерн Interpreter будується на чотирьох ролях:**
> 
> - **AbstractExpression** — інтерфейс з методом `interpret(context)`, який реалізують усі вузли граматики.
> - **TerminalExpression** — листовий вузол, що не містить інших виразів. Реалізує безпосередню інтерпретацію (наприклад, числа або змінні).
> - **NonTerminalExpression** — складений вузол, що містить інші вирази та реалізує правило граматики (наприклад, операція додавання).
> - **Context** — зовнішня інформація, що передається під час інтерпретації (наприклад, значення змінних).

---

> [!info] Key Insights
> 
> - **Граматика як ієрархія класів.** Кожне правило мови — окремий клас. Додати нову операцію означає додати новий клас, не змінюючи існуючих.
>     
> - **Дерево синтаксичного розбору (AST).** Вирази компонуються у дерево: листи — термінальні символи (числа), вузли — операції. Виклик `interpret()` рекурсивно обходить дерево знизу вгору.
>     
> - **Спорідненість з Composite.** Структура NonTerminalExpression, що містить список AbstractExpression, є прямим застосуванням патерну Composite — дерево виразів будується за тими самими принципами.
>     
> - **Простота розширення граматики.** Нова операція (`multiply`, `modulo`) — новий клас. Клієнтський код і існуючі вирази залишаються незмінними.
>     

---

> [!example] Практичне застосування
> 
> **Калькулятор простих арифметичних виразів** — вираз $3 + 5 - 2$ представляється як дерево:
> 
> - `NumberExpression(3)`, `NumberExpression(5)`, `NumberExpression(2)` — термінальні вузли
> - `AddExpression` та `SubtractExpression` — нетермінальні вузли, що рекурсивно викликають `interpret()` на своїх операндах

---

## Реалізація

```java
// AbstractExpression: інтерфейс для всіх виразів
interface Expression {
    int interpret();
}

// TerminalExpression: числовий літерал
class NumberExpression implements Expression {
    private final int number;

    public NumberExpression(int number) {
        this.number = number;
    }

    @Override
    public int interpret() {
        return number;
    }
}

// NonTerminalExpression: операція додавання
class AddExpression implements Expression {
    private final Expression left;
    private final Expression right;

    public AddExpression(Expression left, Expression right) {
        this.left = left;
        this.right = right;
    }

    @Override
    public int interpret() {
        return left.interpret() + right.interpret();
    }
}

// NonTerminalExpression: операція віднімання
class SubtractExpression implements Expression {
    private final Expression left;
    private final Expression right;

    public SubtractExpression(Expression left, Expression right) {
        this.left = left;
        this.right = right;
    }

    @Override
    public int interpret() {
        return left.interpret() - right.interpret();
    }
}

public class Main {
    public static void main(String[] args) {
        // Побудова дерева виразу: (3 + 5) - 2
        Expression expression = new SubtractExpression(
            new AddExpression(
                new NumberExpression(3),
                new NumberExpression(5)
            ),
            new NumberExpression(2)
        );

        // Інтерпретація: рекурсивний обхід дерева знизу вгору
        System.out.println("Результат: " + expression.interpret());
    }
}

/* Результат:
   Результат: 6
*/
```

---

> [!warning] Обмеження та ризики
> 
> - **Вибух кількості класів.** Кожне правило граматики — окремий клас. Для складних мов із десятками правил кількість класів стає некерованою.
>     
> - **Погана продуктивність.** Рекурсивний обхід AST для складних або глибоко вкладених виразів може бути суттєво повільнішим за спеціалізований парсер. Не підходить для інтерпретації великих програм.
>     
> - **Підходить лише для простих граматик.** GOF прямо застерігає: якщо граматика містить більше 10–15 правил — краще використати спеціалізований генератор парсерів (ANTLR, JavaCC).
>     

---

> [!tip] Коли використовувати
> 
> **Так** ✅:
> 
> - Граматика мови проста і стабільна (SQL-підмножина, математичні вирази, регулярні вирази)
> - Речення мови потрібно інтерпретувати в дереві об'єктів
> - Розширюваність граматики важливіша за продуктивність
> 
> **Ні** ❌:
> 
> - Граматика складна або часто змінюється — краще ANTLR або подібні інструменти
> - Потрібна висока продуктивність парсингу
> - Вирази прості настільки, що достатньо `eval()` або `switch`

---
#behavioral #GOF #pattern
# Connections:
- [[Шаблони GOF (Gang of Four)]]