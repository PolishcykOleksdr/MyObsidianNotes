## Оператори стріму в Java надають багато можливостей для обробки та маніпулювання даними в колекціях. Вони поділяються на дві категорії: проміжні та термінальні операції.

# Типи операцій:

>[!info] Проміжні Операції:
> 1. **filter(Predicate<T\> predicate)**: Відбір елементів за певною умовою.
> 2. **map(Function<T, R> mapper)**: Трансформація елементів за допомогою заданої функції.
> 3. **flatMap(Function<T, Stream<R\>> mapper)**: Розгортання вкладених стрімів в одномірний стрім.
> 4. **distinct()**: Вилучення дублікатів елементів у стрімі.
> 5. **sorted()**: Сортування елементів у стрімі.
> 6. **limit(int n):** Обмежує кількість елементів у стримі.
> 7. **skip(int n):** Пропускає вказану кількість елементів.

---

> [!info] Термінальні Операції:
> 
> 1. **forEach(Consumer<T\> action)**: Застосування дії до кожного елемента стріму.
> 2. **collect(Collector<T, A, R> collector)**: Збір елементів стріму за допомогою заданого колектора.
> 3. **count()**: Підрахунок кількості елементів у стрімі.
> 4. **anyMatch(Predicate<T\> predicate)**: Перевірка, чи задовольняє хоча б один елемент стріму певній умові.
> 5. **allMatch(Predicate<T\> predicate)**: Перевірка, чи всі елементи стріму задовольняють певній умові.
> 6. **noneMatch(Predicate<T\> predicate)**: Перевірка, чи жоден елемент стріму не задовольняє певній умові.
> 7. **findAny()**: Повертає будь-який елемент стріму.
> 8. **findFirst()**: Повертає перший елемент стріму.
> 9. **reduce(BinaryOperator<T\> accumulator)**: Перетворення всього списку елементів на один результат шляхом їх послідовного об'єднання за правилом.
> 10. **min(Comparator<T\> comparator)**: Знаходження мінімального елемента стріму за допомогою компаратора.
> 11. **max(Comparator<T\> comparator)**: Знаходження максимального елемента стріму за допомогою компаратора.

---
# Приклади

## Проміжні Операції:

## 1. **filter(Predicate<T\> predicate)**

Відбір елементів за певною умовою.

```java
List<String> fruits = Arrays.asList("apple", "orange", "banana", "grape");

List<String> filteredFruits = fruits.stream()
                                   .filter(fruit -> fruit.length() > 5)
                                   .collect(Collectors.toList());
// Результат: ["orange", "banana"]
```

---
## 2. **map(Function<T, R> mapper)**

Трансформація елементів за допомогою заданої функції.

```java
List<String> fruits = Arrays.asList("apple", "orange", "banana", "grape");

List<String> upperCaseFruits = fruits.stream()
                                     .map(String::toUpperCase)
                                     .collect(Collectors.toList());
// Результат: ["APPLE", "ORANGE", "BANANA", "GRAPE"]
```

---
## 3. **flatMap(Function<T, Stream<R\>> mapper)**

Розгортання вкладених стрімів в одномірний стрім.

```java
List<List<Integer>> numbers = Arrays.asList(Arrays.asList(1, 2, 3), Arrays.asList(4, 5, 6));

List<Integer> flatNumbers = numbers.stream()
                                   .flatMap(List::stream)
                                   .collect(Collectors.toList());
// Результат: [1, 2, 3, 4, 5, 6]
```

---
## 4. **distinct()**

Вилучення дублікатів елементів у стрімі.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 2, 4, 5, 3);

List<Integer> distinctNumbers = numbers.stream()
                                      .distinct()
                                      .collect(Collectors.toList());
// Результат: [1, 2, 3, 4, 5]
```

---
## 5. **sorted()**

Сортування елементів у стрімі.

```java
List<Integer> numbers = Arrays.asList(3, 1, 4, 1, 5, 9, 2, 6, 5);

List<Integer> sortedNumbers = numbers.stream()
                                    .sorted()
                                    .collect(Collectors.toList());
// Результат: [1, 1, 2, 3, 4, 5, 5, 6, 9]
```

---
## 6. **limit(int n)**

Вилучення дублікатів елементів у стрімі.

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

List<Integer> limited = numbers.stream()
    .limit(5) // Беремо перші 5 елементів
    .toList(); 

// Результат: [1, 2, 3, 4, 5]
```

---
## 7. **skip(int n)**

Сортування елементів у стрімі.

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

List<Integer> skipped = numbers.stream()
    .skip(7) // Пропускаємо перші 7 елементів
    .toList();

// Результат: [8, 9, 10]
```

---

## Термінальні Операції:

## 1. **forEach(Consumer<T\> action)**

Застосування дії до кожного елемента стріму.

```java
List<String> fruits = Arrays.asList("apple", "orange", "banana", "grape");

fruits.stream().forEach(System.out::println);
// Результат: виведе кожен фрукт на екран
```

---
## 2. **collect(Collector<T, A, R> collector)**

Збір елементів стріму за допомогою заданого колектора.

```java
List<String> fruits = Arrays.asList("apple", "orange", "banana", "grape");

List<String> collectedFruits = fruits.stream().collect(Collectors.toList());
// Результат: ["apple", "orange", "banana", "grape"]
```

---
## 3. **count()**

Підрахунок кількості елементів у стрімі.

```java
List<String> fruits = Arrays.asList("apple", "orange", "banana", "grape");

long count = fruits.stream().count();
// Результат: 4
```

---
## 4. **anyMatch(Predicate<T\> predicate)**

Перевірка, чи задовольняє хоча б один елемент стріму певній умові.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

boolean anyMatch = numbers.stream().anyMatch(n -> n > 3);
// Результат: true (бо є хоча б один елемент більший за 3)
```

---
## 5. **allMatch(Predicate<T\> predicate)**

Перевірка, чи всі елементи стріму задовольняють певній умові.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

boolean allMatch = numbers.stream().allMatch(n -> n > 0);
// Результат: true (бо всі елементи позитивні)
```

---
## 6. **noneMatch(Predicate<T\> predicate)**

Перевірка, чи жоден елемент стріму не задовольняє певній умові.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

boolean noneMatch = numbers.stream().noneMatch(n -> n < 0);
// Результат: true (бо всі елементи не від'ємні)
```

---
## 7. **findAny()**

Повертає будь-який елемент стріму.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

Optional<Integer> anyNumber = numbers.stream().findAny();
// Результат: може повернути будь-який елемент, наприклад, 3
```

---
## 8. **findFirst()**

Повертає перший елемент стріму.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

Optional<Integer> firstNumber = numbers.stream().findFirst();
// Результат: поверне перший елемент, тобто 1
```

---
## 9. **reduce(BinaryOperator<T\> accumulator)**

Результат елементів стріму за допомогою заданого оператора.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

Optional<Integer> sum = numbers.stream().reduce((a, b) -> a + b);
// Результат: Optional[15] (сума всіх чисел)
```

---
## 10. **min(Comparator<T\> comparator)**

Знаходження мінімального елемента стріму за допомогою компаратора.

```java
List<Integer> numbers = Arrays.asList(3, 1, 4, 1, 5, 9, 2, 6, 5);

Optional<Integer> minNumber = numbers.stream().min(Integer::compare);
// Результат: Optional[1] (мінімальне число)
```

---
### 11. **max(Comparator<T\> comparator)**

Знаходження максимального елемента стріму за допомогою компаратора.

```java
List<Integer> numbers = Arrays.asList(3, 1, 4, 1, 5, 9, 2, 6, 5);

Optional<Integer> maxNumber = numbers.stream().max(Integer::compare);
// Результат: Optional[9] (максимальне число)
```

---
### Ці приклади ілюструють різноманітні варіанти використання операторів стріму в Java для обробки даних.

---
# Connections:
- [[Стріми (Stream API)]]]
- [[Паралельні стріми (Parrallel Stream API)]]
- [[Лямбда-вирази]]
- [[Функції вищого порядку (Higher-Order Functions)]]
- [[Optional]]
- [[Пакет java.util.function]]
- [[Посилання на методи]]