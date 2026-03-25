> **Visitor** — поведінковий патерн, що дозволяє визначити нову операцію над об'єктами структури без зміни їхніх класів. Алгоритм відокремлюється від об'єктів, на яких він виконується.

---

> [!abstract] Core Framework 
> **Патерн Visitor будується на чотирьох ролях:**
> 
> - **Element (інтерфейс)** — оголошує метод `accept(Visitor)`, через який об'єкт «приймає» відвідувача.
> - **ConcreteElement** — реалізує `accept()`, викликаючи відповідний метод `visit(this)` на відвідувачі.
> - **Visitor (інтерфейс)** — оголошує перевантажені методи `visit()` для кожного типу елемента.
> - **ConcreteVisitor** — реалізує операцію для кожного типу елемента (наприклад, розрахунок зарплати).

---

> [!info] Key Insights
> 
> - **Подвійна диспетчеризація (Double Dispatch).** Перший виклик — `employee.accept(visitor)` — визначає тип елемента. Другий — `visitor.visit(this)` — визначає конкретну операцію. JVM на основі двох типів вибирає правильну реалізацію.
>     
> - **Відкритість для нових операцій.** Щоб додати нову операцію (наприклад, розрахунок відпускних), достатньо створити новий `ConcreteVisitor`. Класи `Manager` та `Worker` залишаються незмінними.
>     
> - **Закритість для нових типів елементів.** Якщо додати новий тип співробітника (`Consultant`), доведеться додати `visit(Consultant)` до інтерфейсу `Visitor` та оновити всі існуючі реалізації — це основний недолік патерну.
>     
> - **Розділення відповідальності.** Логіка операції (розрахунок зарплати) повністю відокремлена від структури даних (`Manager`, `Worker`). Кожен клас відповідає за своє.
>     

---

> [!example] Практичне застосування
> 
> **Кадровий облік** — різні типи співробітників, різна логіка розрахунку:
> 
> - `Manager` — фіксована зарплата (`salary`)
> - `Worker` — погодинна ставка × кількість годин ($hourlyRate \times hoursWorked$)
> - `SalaryCalculator` як відвідувач знає, як рахувати для кожного типу, не змінюючи самі класи

---

## Реалізація

```java
import java.util.ArrayList;
import java.util.List;

// Абстрактний клас співробітника
interface Employee {
    void accept(Visitor visitor);
}

// Конкретний клас: Менеджер
class Manager implements Employee {
    private String name;
    private double salary;

    public Manager(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }

    public String getName() {
        return name;
    }

    public double getSalary() {
        return salary;
    }

    @Override
    public void accept(Visitor visitor) {
        visitor.visit(this);
    }
}

// Конкретний клас: Робітник
class Worker implements Employee {
    private String name;
    private double hourlyRate;
    private int hoursWorked;

    public Worker(String name, double hourlyRate, int hoursWorked) {
        this.name = name;
        this.hourlyRate = hourlyRate;
        this.hoursWorked = hoursWorked;
    }

    public String getName() {
        return name;
    }

    public double getHourlyRate() {
        return hourlyRate;
    }

    public int getHoursWorked() {
        return hoursWorked;
    }

    @Override
    public void accept(Visitor visitor) {
        visitor.visit(this);
    }
}

// Інтерфейс Відвідувача
interface Visitor {
    void visit(Manager manager);
    void visit(Worker worker);
}

// Конкретний Відвідувач: Розрахунок зарплати
class SalaryCalculator implements Visitor {
    @Override
    public void visit(Manager manager) {
        System.out.println("Зарплата менеджера " + manager.getName() + ": " + manager.getSalary());
    }

    @Override
    public void visit(Worker worker) {
        double salary = worker.getHourlyRate() * worker.getHoursWorked();
        System.out.println("Зарплата робітника " + worker.getName() + ": " + salary);
    }
}

public class Main {
    public static void main(String[] args) {
        // Створення списку співробітників
        List<Employee> employees = new ArrayList<>();
        employees.add(new Manager("Іван", 5000));
        employees.add(new Worker("Петро", 15, 160));

        // Відвідувач для розрахунку зарплати
        Visitor salaryCalculator = new SalaryCalculator();

        // Розрахунок зарплати для кожного співробітника
        for (Employee employee : employees) {
            employee.accept(salaryCalculator);
        }
    }
}

/* Результат:
   Зарплата менеджера Іван: 5000.0
   Зарплата робітника Петро: 2400.0
*/
```

---

> [!warning] Обмеження та ризики
> 
> - **Складність додавання нових типів елементів.** Новий тип (наприклад, `Consultant`) вимагає змін в інтерфейсі `Visitor` та у всіх його реалізаціях — порушується принцип відкритості/закритості для елементів.
>     
> - **Порушення інкапсуляції.** Щоб відвідувач міг виконати свою роботу, елементи часто змушені надавати публічний доступ до своїх внутрішніх даних через геттери — як `getSalary()`, `getHourlyRate()` тощо.
>     
> - **Складність розуміння.** Подвійна диспетчеризація через `accept()`→`visit()` нетривіальна для читання — розробнику потрібно стежити за двома рівнями виклику одночасно.
>     

---

> [!tip] Коли використовувати
> 
> **Так** ✅:
> 
> - Структура об'єктів стабільна, але операції над нею часто додаються
> - Потрібно виконати кілька непов'язаних операцій над об'єктами різних типів
> - Логіка операції залежить від конкретного типу елемента
> 
> **Ні** ❌:
> 
> - Ієрархія елементів часто змінюється — кожне додавання типу ламає всі відвідувачі
> - Операції прості і не залежать від типу елемента
> - Класи елементів не можна або небажано розкривати через геттери

---
#behavioral #GOF #pattern
# Connections:
- [[Шаблони GOF (Gang of Four)]]