> Class TreeMap — це реалізація інтерфейсу `NavigableMap`, що базується на структурі **червоно-чорного дерева**. Вона забезпечує впорядкування ключів за їхнім природним порядком або за допомогою спеціального компаратора.

---

> [!abstract] Core Framework
> 
> **TreeMap** — це колекція, що зберігає пари "ключ-значення" у формі збалансованого двійкового дерева пошуку.
> 
> **Ключові інтерфейси:**
> 
> - `SortedMap<K,V>`: забезпечує повне впорядкування ключів.
>     
> - `NavigableMap<K,V>`: розширює `SortedMap` методами навігації для пошуку найближчих відповідностей (`floor`, `ceiling`, `lower`, `higher`).
>     
> 
> **Червоно-чорне дерево (Red-Black Tree):**
> 
> Це самозбалансоване дерево, яке гарантує часову складність для основних операцій:
> 
> - Пошук/Вставка/Видалення: $O(\log n)$.
>     

---

> [!info] Key Insights
> 
> - **Унікальність:** Містить лише унікальні елементи (ключі).
>     
> - **Null-політика:** **Не може** мати `null` ключ (викидає `NullPointerException`), але дозволяє зберігати кілька `null` значень.
>     
> - **Синхронізація:** Клас несинхронізований. Кілька потоків можуть одночасно модифікувати об'єкт, що вимагає зовнішньої синхронізації.
>     
> - **Порядок:** Підтримує зростаючий порядок впорядкування ключів.
>     
> - **Навігація:** Завдяки `NavigableMap`, підтримує методи пошуку "найближчих" сусідів для заданого ключа.
>     

---

> [!example] Практичне застосування
> 
> ### 1. Робота з межами (firstKey, lastKey)
> ```java
> import java.util.NoSuchElementException;
> import java.util.SortedMap;
> import java.util.TreeMap;
> 
> /* Метод firstKey() повертає найнижчий ключ, lastKey() - найвищий */
> 
> public class Main {
> 
> public static void main(String[] args) {
> 
> 	SortedMap<String, String> map = new TreeMap<>();
> 
> 	map.put("abc1", "orange");
> 	map.put("dvc3", "apricot");
> 	map.put("sat2", "pepper");
> 
> 	map.put("est8", null); // <- null значення
>     System.out.println("1) Data: " + map);
>     
>     /* Результат: {abc1=orange, dvc3=apricot, est8=null, sat2=pepper} */
>     
>     System.out.println("Result 1 (first): " + map.firstKey()); // abc1
>     System.out.println("Result 1 (last): " + map.lastKey());   // sat2
>     
>     map.clear();
>     
>     try {
>         map.firstKey(); /* Викидає NoSuchElementException, якщо порожній */
>     } catch (NoSuchElementException e) {
>         System.out.println("Result 2 is " + e.getMessage());
>     }
> }
> }
> ```
> 
> ### 2. Перегляди підмножин (headMap, tailMap)
> ```java
> import java.util.SortedMap;
> import java.util.TreeMap;
> public class Main {
> 
> public static void main(String[] args) {
> 
> 	SortedMap<String, String> map = new TreeMap<>();
> 
> 	map.put("abc1", "orange");
> 	map.put("dvc3", "apricot");
> 	map.put("sat2", "pepper");
> 	map.put("est8", null);
> 
> 	map.put("kyo7", "kiwi");
>     
>     /* headMap(K toKey): ключі < toKey */
>     SortedMap<String, String> head = map.headMap("est8");
>     System.out.println("HeadMap: " + head); // {abc1=orange, dvc3=apricot}
>     
>     /* tailMap(K fromKey): ключі >= fromKey */
>     SortedMap<String, String> tail = map.tailMap("est8");
>     
>     System.out.println("TailMap: " + tail); // {est8=null, kyo7=kiwi, sat2=pepper}
> }
> }
> ```
> 
> ### 3. Навігаційні методи (ceiling, floor, higher, lower)
> ```java
> import java.util.Map;
> import java.util.NavigableMap;
> import java.util.TreeMap;
> 
> public class Main {
> 
> 	public static void main(String[] args) {
> 
> 	NavigableMap<String, String> map = new TreeMap<>();
> 
> 	map.put("abc1", "orange");
> 	map.put("dvc3", "apricot");
> 	map.put("sat2", "pepper");
> 	map.put("est8", null);
> 	map.put("kyo7", "kiwi");
> 	
>     /* ceilingEntry: найменший ключ >= заданого */
>     System.out.println("Ceiling 'feo5': " + map.ceilingEntry("feo5")); // kyo7=kiwi
>     
>     /* floorEntry: найбільший ключ <= заданого */
>     System.out.println("Floor 'feo5': " + map.floorEntry("feo5")); // est8=null
>     
>     /* higherEntry: найменший ключ > заданого */
>     System.out.println("Higher 'est8': " + map.higherEntry("est8")); // kyo7=kiwi
>     
>     /* lowerEntry: найбільший ключ < заданого */
>     System.out.println("Lower 'est8': " + map.lowerEntry("est8")); // dvc3=apricot
> }
> }
> ```

---
# Унікальні методи:
### **Межі мапи (Крайні точки)**

- **`firstKey()`** — знаходить **найперший** (найменший) ключ. Якщо мапа порожня — "свариться" помилкою.
    
- **`lastKey()`** — знаходить **найвранішній** (найбільший) ключ. Також видасть помилку, якщо мапа порожня.
### **Робота з діапазонами (Зрізи)**

- **`headMap(toKey)`** — показує початок мапи: всі записи, де ключі **менші** за вказаний (не включаючи сам `toKey`).
    
- **`tailMap(fromKey)`** — показує "хвіст" мапи: всі записи, починаючи з `fromKey` і до самого кінця (**включаючи** сам `fromKey`).
### **Пошук "сусідів" (Навігація)**

Тут важливо розрізняти "суворо менше/більше" та "дорівнює".

- **`ceilingEntry(key)`** — шукає ключ, який **більший або дорівнює** заданому. Це як "стеля": перше значення, що не нижче за ваше.
    
- **`floorEntry(key)`** — шукає ключ, який **менший або дорівнює** заданому. Це як "підлога": найближче значення знизу.
    
- **`higherEntry(key)`** — шукає ключ, який **суворо більший** за ваш (наступний у черзі).
    
- **`lowerEntry(key)`** — шукає ключ, який **суворо менший** за ваш (попередній у черзі).

---

> [!note] Пояснення
> ### Як виглядає ваша мапа всередині (відсортована):
> 
> Перед пошуком Java розставляє ваші ключі в такому порядку:
> 
> 1. `abc1` (orange)
>     
> 2. `dvc3` (apricot)
>     
> 3. `est8` (null) — _зверніть увагу: значення null, але ключ існує!_
>     
> 4. `kyo7` (kiwi)
>     
> 5. `sat2` (pepper)
>     
> 
> ---
> 
> ### Покроковий розбір результатів:
> 
> #### 1. `ceilingEntry("feo5")` → `kyo7=kiwi`
> 
> - **Логіка:** Шукаємо найменший ключ, який **більший або дорівнює** (`>=`) "feo5".
>     
> - Алфавітно "feo5" іде після "est8", але перед "kyo7".
>     
> - Ключі, що більші за "feo5": `kyo7` та `sat2`.
>     
> - Найменший серед них — **`kyo7`**.
>     
> 
> #### 2. `floorEntry("feo5")` → `est8=null`
> 
> - **Логіка:** Шукаємо найбільший ключ, який **менший або дорівнює** (`<=`) "feo5".
>     
> - Ключі, що менші за "feo5": `abc1`, `dvc3`, `est8`.
>     
> - Найбільший (найближчий до "feo5" знизу) — **`est8`**.
>     
> - _Чому вивело `null`?_ Тому що значення (`value`) для ключа `est8` у вашому коді було задано як `null`.
>     
> 
> #### 3. `higherEntry("est8")` → `kyo7=kiwi`
> 
> - **Логіка:** Шукає найменший ключ, який **суворо більший** (`>`) за "est8".
>     
> - Метод `higher` ігнорує сам ключ "est8", навіть якщо він є в мапі.
>     
> - Наступний ключ у списку після "est8" — **`kyo7`**.
>     
> 
> #### 4. `lowerEntry("est8")` → `dvc3=apricot`
> 
> - **Логіка:** Шукає найбільший ключ, який **суворо менший** (`<`) за "est8".
>     
> - Ключ "est8" знову ігнорується.
>     
> - Попередній ключ перед "est8" у відсортованому списку — **`dvc3`**.
>     
> 
> ---
> 
> ### Підсумок (різниця між ними):
> 
> - **`ceiling` / `floor`** включають сам ключ у пошук (якщо ви введете "est8", вони повернуть "est8").
>     
> - **`higher` / `lower`** завжди шукають сусіда, ігноруючи вказаний ключ.

---

> [!warning] Обмеження та ризики
> 
> - **NoSuchElementException:** Методи `firstKey()` та `lastKey()` викидають виняток, якщо дерево порожнє. Необхідна перевірка `isEmpty()` або обробка `try-catch`.
>     
> - **NullPointerException:** При спробі вставити `null` як ключ, `TreeMap` видасть помилку, оскільки не зможе викликати метод порівняння для впорядкування.
>     
> - **Продуктивність:** Хоча $O(\log n)$ — це дуже швидко, `HashMap` з її $O(1)$ зазвичай швидша для простих операцій, якщо порядок не має значення.
>     

---

> [!tip] Коли використовувати
> 
> **Так ✅:**
> 
> - Потрібен постійний сортований порядок ключів.
>     
> - Необхідно виконувати діапазонні запити (наприклад, отримати всі елементи від "А" до "Б").
>     
> - Потрібен функціонал пошуку найближчих сусідів (`NavigableMap`).
>     
> 
> **Ні ❌:**
> 
> - Порядок елементів не має значення (використовуйте `HashMap`).
>     
> - Потрібна максимальна швидкість вставки та пошуку (в середньому $O(1)$ у `HashMap` краще за $O(\log n)$).
>     
> - У ключах часто зустрічається `null`.
>     

---
# Connections:
- [[Interface Map]]