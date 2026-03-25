У Java, функції не можна просто передавати як значення (на відміну від JavaScript). Функціональні інтерфейси — це спосіб упакувати логіку в об'єкт, який можна передати, зберегти або виконати пізніше. Це як створити контейнер для інструкції: "зроби ось так, коли настане час".

---
> [!abstract] Core Framework
> Пакет `java.util.function` містить готові шаблони для типових завдань: створювати, змінювати, перевіряти, перетворювати дані.
> 
> **Метафора**: Це набір формочок для печива. Кожна має свою форму (сигнатуру): одна приймає тісто й нічого не повертає, інша приймає два інгредієнти й дає результат.

---

# Основні Функціональні Інтерфейси

> [!abstract] Суть
> Пакет `java.util.function` надає 6 базових шаблонів для роботи з функціями як з об'єктами. Кожен інтерфейс описує конкретний патерн: що приймає → що повертає.

---
## Типи інтерфейсів

> [!info]- Supplier\<T>
> **Роль**: Постачальник значення
> 
> **Сигнатура**: `() -> T`
> - Вхід: нічого
> - Вихід: значення типу `T`
> 
> **Метод**: `T get()`
> 
> **Коли використовувати**: Ледаче створення об'єктів, генератори, фабрики значень
# Приклад:

```java
import java.util.function.Supplier;

public class SupplierExample {
    public static void main(String[] args) {
        // Приклад використання Supplier для створення випадкового числа
        Supplier<Integer> randomIntegerSupplier = () -> (int) (Math.random() * 100);
        System.out.println("Випадкове число: " + randomIntegerSupplier.get());
        //random number from 0 to 100
    }
}
```
---

> [!info]- Consumer\<T>
> **Роль**: Споживач значення
> 
> **Сигнатура**: `T -> void`
> - Вхід: значення типу `T`
> - Вихід: нічого (побічний ефект)
> 
> **Метод**: `void accept(T t)`
> 
> **Коли використовувати**: Виведення в консоль, запис у файл, зміна стану
# Приклад:

```java
import java.util.function.Consumer;

public class ConsumerExample {
    public static void main(String[] args) {
        // Приклад використання Consumer для виведення рядка в верхньому регістрі
        Consumer<String> toUpperCaseConsumer = s -> System.out.println(s.toUpperCase());
        toUpperCaseConsumer.accept("Hello, World!"); //HELLO, WORLD!
    }
}
```
---

> [!info]- Predicate\<T>
> **Роль**: Перевірка умови
> 
> **Сигнатура**: `T -> boolean`
> - Вхід: значення типу `T`
> - Вихід: `true` або `false`
> 
> **Метод**: `boolean test(T t)`
> 
> **Коли використовувати**: Фільтрація колекцій, валідація даних, умовна логіка
# Приклад:
```java
import java.util.function.Predicate;

public class PredicateExample {
    public static void main(String[] args) {
        // Приклад використання Predicate для перевірки четності числа
        Predicate<Integer> isEven = n -> n % 2 == 0;
        System.out.println("Число 5 є парним: " + isEven.test(5));  // false
        System.out.println("Число 8 є парним: " + isEven.test(8));  // true
    }
}
```
---

> [!info]- Function\<T, R>
> **Роль**: Перетворювач типів
> 
> **Сигнатура**: `T -> R`
> - Вхід: значення типу `T`
> - Вихід: значення типу `R`
> 
> **Метод**: `R apply(T t)`
> 
> **Коли використовувати**: Маппінг даних, конвертація типів, трансформація об'єктів
# Приклад:
```java
import java.util.function.Function;

public class FunctionExample {
    public static void main(String[] args) {
        // Приклад використання Function для конвертації рядка в число
        Function<String, Integer> convertToInteger = Integer::valueOf;
        int result = convertToInteger.apply("123");
        System.out.println("Результат конвертації: " + result); //int - 123
    }
}
```
---

> [!info]- UnaryOperator\<T>
> **Роль**: Унарна операція (спеціалізація `Function<T, T>`)
> 
> **Сигнатура**: `T -> T`
> - Вхід: значення типу `T`
> - Вихід: значення типу `T`
> 
> **Метод**: `T apply(T t)`
> 
> **Коли використовувати**: Модифікація значення (подвоєння, інкремент, округлення)
# Приклад:
```java
import java.util.function.UnaryOperator;

public class UnaryOperatorExample {
    public static void main(String[] args) {
        // Приклад використання UnaryOperator для подвоєння числа
        UnaryOperator<Integer> doubleNumber = n -> n * 2;
        System.out.println("Подвоєне число: " + doubleNumber.apply(5)); //10
    }
}
```
---

> [!info]- BinaryOperator\<T>
> **Роль**: Бінарна операція (спеціалізація `BiFunction<T, T, T>`)
> 
> **Сигнатура**: `(T, T) -> T`
> - Вхід: два значення типу `T`
> - Вихід: значення типу `T`
> 
> **Метод**: `T apply(T t1, T t2)`
> 
> **Коли використовувати**: Агрегація (сума, максимум, конкатенація)
# Приклад:
```java
import java.util.function.BinaryOperator;

public class BinaryOperatorExample {
    public static void main(String[] args) {
        // Приклад використання BinaryOperator 
        // для знаходження максимуму двох чисел
        BinaryOperator<Integer> max = Integer::max;
        System.out.println("Максимум: " + max.apply(7, 3)); //7
    }
}
```

---
# Коротко

| **Інтерфейс**          | **Сигнатура**  | **Опис**                                              |
| ---------------------- | -------------- | ----------------------------------------------------- |
| **Supplier\<T>**       | `() -> T`      | Генерація або надання об'єкта.                        |
| **Consumer\<T>**       | `T -> void`    | Виконання дії над об'єктом без повернення результату. |
| **Predicate\<T>**      | `T -> boolean` | Перевірка умови (фільтрація).                         |
| **Function<T, R>**     | `T -> R`       | Перетворення об'єкта одного типу в інший.             |
| **UnaryOperator\<T>**  | `T -> T`       | Обробка об'єкта, де результат має той самий тип.      |
| **BinaryOperator\<T>** | `(T, T) -> T`  | Об'єднання двох об'єктів одного типу в один.          |

---
# **Заключення**

Пакет **java.util.function** в Java надає набір функціональних інтерфейсів, які дозволяють зручно використовувати функціональне програмування в мові Java. Ці інтерфейси стають основою для реалізації функцій, які можуть бути передані як параметри методів, повертати результати чи використовувати у функціональних конструкціях.

Кожен інтерфейс виконує конкретну функцію, починаючи від постачання значень до виконання операцій з двома аргументами. Застосування цих інтерфейсів робить код більш зрозумілим, гнучким та підтримує сучасні концепції програмування.

З використанням **Supplier**, **Consumer**, **Predicate**, **Function**, **UnaryOperator** та **BinaryOperator** ви можете легко використовувати функціональні підходи для обробки даних, що робить ваш код більш ефективним та елегантним.