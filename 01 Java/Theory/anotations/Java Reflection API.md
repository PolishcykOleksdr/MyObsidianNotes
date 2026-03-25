> **Java Reflection API** — це потужний інструмент, який надає засоби для інспекції та маніпулювання структурою програми безпосередньо під час її виконання. Центром цього механізму є клас `Class`, який представляє структурну інформацію про типи даних у JVM.

---

> [!abstract] Core Framework Java Reflection API дозволяє динамічно взаємодіяти з кодом через спеціальні об'єкти-метадані. **Основні можливості:**
> 
> - **Отримання класу**: Доступ до об'єкта `Class` за ім'ям (через рядок) або типом.
>     
> - **Інспекція метаданих**: Отримання інформації про модифікатори, поля, методи та конструктори.
>     
> - **Динамічне управління**: Створення екземплярів, виклик методів та зміна значень полів "на льоту".
>     
> - **Робота з анотаціями**: Зчитування метаданих, асоційованих з елементами коду.
>     
> - **Динамічне завантаження**: Завантаження нових класів у пам'ять під час роботи програми.
>     

---

> [!info] Key Insights
> 
> - **Об'єкт Class**: Це "дзеркало" структури класу. Він містить все необхідне для розуміння того, з чого складається об'єкт.
>     
> - **Runtime Інспекція**: Рефлексія дозволяє дізнатися про склад об'єкта, навіть якщо на етапі компіляції тип був невідомий (наприклад, `Object obj`).
>     
> - **Гнучкість vs Продуктивність**: Хоча інструмент надає безмежну гнучкість, він обходить деякі оптимізації JVM, тому має використовуватись обґрунтовано.
>     

---

> [!example] Практичне застосування: Комплексна інспекція класу Нижче наведено приклад коду, що демонструє повний цикл роботи з рефлексією на прикладі класу `java.lang.String`.
> 
> Java
> 
> ```java
> import java.lang.reflect.*;
> import java.lang.annotation.*;
> public class ReflectionExample { public static void main(String[] args) throws Exception {.
> 	/*Отримання класу за його ім'ям */
> 	Class<\?> clazz = Class.forName("java.lang.String");
> 	
>     // 2. Отримання всіх методів класу
>     Method[] methods = clazz.getDeclaredMethods();
>     System.out.println("Методи класу " + clazz.getName() + ":");
>     for (Method method : methods) {
>         System.out.println("\t" + method.getName());
>     }
>     
>     // 3. Отримання всіх полів класу
>     Field[] fields = clazz.getDeclaredFields();
>     System.out.println("\nПоля класу " + clazz.getName() + ":");
>     for (Field field : fields) {
>         System.out.println("\t" + field.getName());
>     }
>     
>     // 4. Отримання всіх конструкторів класу
>     Constructor<?>[] constructors = clazz.getDeclaredConstructors();
>     System.out.println("\nКонструктори класу " + clazz.getName() + ":");
>     for (Constructor<?> constructor : constructors) {
>         System.out.println("\t" + constructor.getName());
>     }
>     
>     // 5. Створення нового об'єкту класу за допомогою рефлексії
>     Object obj = clazz.getDeclaredConstructor(String.class).newInstance("Hello, Reflection!");
>     System.out.println("\nСтворений об'єкт: " + obj);
>     
>     // 6. Виклик методу класу за допомогою рефлексії
>     Method method = clazz.getMethod("length");
>     int length = (int) method.invoke(obj);
>     System.out.println("Довжина рядка: " + length);
>     
>     // 7. Отримання анотацій класу
>     Annotation[] annotations = clazz.getAnnotations();
>     System.out.println("\nАнотації класу " + clazz.getName() + ":");
>     for (Annotation annotation : annotations) {
>         System.out.println("\t" + annotation.annotationType().getName());
>     }
> }
> ```

---

> [!example] Практичне застосування: Детальна інспекція методів Приклад отримання сигнатур методів (тип повернення, модифікатори) та їх динамічного виклику.
> 
> Java
> 
> ```java
> import java.lang.reflect.Method;
> 
> public class ReflectionExample { public static void main(String[] args) throws Exception { 
> 	// Отримання класу 
> 	Class<?> clazz = Class.forName("java.lang.String");
> 	
>     // Отримання всіх методів класу та їх характеристик
>     Method[] methods = clazz.getDeclaredMethods();
>     for (Method method : methods) {
>         System.out.println("Method name: " + method.getName());
>         System.out.println("Return type: " + method.getReturnType());
>         System.out.println("Modifiers: " + method.getModifiers());
>         System.out.println();
>     }
>     
>     // Створення об'єкту та виклик конкретного методу length()
>     Object obj = clazz.getDeclaredConstructor(String.class).newInstance("Hello, Reflection!");
>     
>     Method lengthMethod = clazz.getMethod("length");
>     int length = (int) lengthMethod.invoke(obj);
>     
>     System.out.println("Length of the string: " + length); // Результат: 17
> }
> }
> ```

---

> [!warning] Обмеження та ризики
> 
> - **Складність коду**: Рефлексія робить логіку менш очевидною для статичного аналізу.
>     
> - **Продуктивність**: Динамічний пошук методів та полів займає більше часу, ніж прямий доступ.
>     
> - **Безпека**: Дозволяє обходити `private` модифікатори, що може порушити внутрішню цілісність об'єктів.
>     

---
# Connections:
- [[Рефлексія в Java]]
- [[Анотації в Java]]