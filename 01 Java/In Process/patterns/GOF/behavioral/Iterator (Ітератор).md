> **Iterator** — поведінковий патерн, що надає механізм послідовного доступу до елементів колекції без розкриття її внутрішньої реалізації.

---

> [!abstract] Core Framework 
> **Патерн Iterator будується на двох ролях:**
> 
> - **Iterable (агрегат)** — колекція, що реалізує метод `iterator()`, який повертає об'єкт-ітератор.
> - **Iterator** — об'єкт із методами `hasNext()` (чи є наступний елемент) та `next()` (отримати наступний елемент), що приховує деталі обходу.

---

> [!info] Key Insights
> 
> - **Приховування внутрішньої структури.** Клієнт отримує елементи через `hasNext()`/`next()` і не знає, чи всередині `ArrayList`, масив, дерево або будь-яка інша структура.
>     
> - **Вбудована підтримка в Java.** Реалізація інтерфейсу `Iterable<T>` автоматично дозволяє використовувати колекцію у циклі `for-each` — компілятор розгортає його у виклики `iterator()`, `hasNext()`, `next()` під капотом.
>     
> - **Делегування вбудованому ітератору.** У прикладі `EmployeeList` не реалізує власний ітератор — вона делегує `employees.iterator()` з `ArrayList`. Це найпростіший і найбезпечніший підхід, коли внутрішнє сховище вже ітерабельне.
>     
> - **Єдина точка обходу.** Логіка перебору інкапсульована в ітераторі. Якщо потрібно змінити структуру зберігання — клієнтський код обходу залишається незмінним.
>     

---

> [!example] Практичне застосування
> 
> **Список працівників** — клієнт перебирає елементи через `Iterator<String>`, не знаючи що всередині `ArrayList`:
> 
> - `employeeList.iterator()` повертає ітератор
> - `iterator.hasNext()` перевіряє наявність наступного елемента
> - `iterator.next()` повертає поточний елемент і зсуває курсор

---

## Реалізація

```java
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

// Клас-контейнер, який містить список працівників
class EmployeeList implements Iterable<String> {
    private List<String> employees;

    public EmployeeList() {
        employees = new ArrayList<>();
    }

    public void addEmployee(String employee) {
        employees.add(employee);
    }

    @Override
    public Iterator<String> iterator() {
        return employees.iterator();
    }
}

public class Main {
    public static void main(String[] args) {
        // Створення списку працівників
        EmployeeList employeeList = new EmployeeList();
        employeeList.addEmployee("Іван");
        employeeList.addEmployee("Марія");
        employeeList.addEmployee("Петро");

        // Ітерація по списку працівників за допомогою ітератора
        Iterator<String> iterator = employeeList.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }
}

/* Результат:
   Іван
   Марія
   Петро
*/
```

---

> [!warning] Обмеження та ризики
> 
> - **`ConcurrentModificationException`.** Якщо змінювати колекцію (додавати/видаляти елементи) під час ітерації — Java кине виняток. Для безпечного видалення під час обходу використовуй `iterator.remove()`.
>     
> - **Однонаправленість.** Стандартний `Iterator` рухається лише вперед. Для двонаправленого обходу потрібен `ListIterator` з методами `hasPrevious()` та `previous()`.
>     
> - **Не потокобезпечний.** `ArrayList.iterator()` не синхронізований. У багатопотоковому середовищі потрібно використовувати `CopyOnWriteArrayList` або явну синхронізацію.
>     

---

> [!tip] Коли використовувати
> 
> **Так** ✅:
> 
> - Потрібно приховати внутрішню структуру колекції від клієнта
> - Потрібно підтримати кілька способів обходу однієї колекції
> - Потрібна єдина уніфікована точка доступу до різних типів колекцій
> 
> **Ні** ❌:
> 
> - Колекція проста і доступ по індексу цілком достатній
> - Потрібен випадковий доступ — ітератор не підтримує `get(i)`
> - Продуктивність критична — прямий доступ до масиву швидший за ітератор

---
#behavioral #GOF #pattern
# Connections:
- [[Шаблони GOF (Gang of Four)]]