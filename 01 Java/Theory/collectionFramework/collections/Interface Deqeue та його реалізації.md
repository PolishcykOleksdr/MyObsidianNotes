>Реалізації: LinkedList, ArrayDeque, LinkedBlockingDeque

>**Уявіть вузьку кімнату з двома дверима на протилежних кінцях. Ви можете заходити та виходити як через передні двері, так і через задні. Це робить `Deque` універсальним інструментом: він може працювати як звичайна черга (хто зайшов першим, той першим і вийшов) або як стопка тарілок (останню покладену тарілку забирають першою).

---

### 1. Концептуальний фундамент

> [!abstract] Core Framework
> **Головна ідея**: `Deque` (Double Ended Queue) — це лінійна колекція, що дозволяє маніпуляції (вставку, видалення, перегляд) з обох кінців.
> 
> **Механіка**:
> 
> - **Крок 1**: Вибір точки доступу — `First` (голова) або `Last` (хвіст).
>     
> - **Крок 2**: Виконання операції. В `ArrayDeque` це зміна індексів у циклічному масиві. В `LinkedList` — переприв'язка посилань `next`/`prev` вузла.
>     
> - **Крок 3**: Контроль ємності. `LinkedBlockingDeque` може заблокувати потік, якщо черга повна (при додаванні) або порожня (при вилученні).
>     
> 
> **Метафора**: Палуба корабля (Deck), де вантаж можна завантажувати та розвантажувати як з носа, так і з корми.

---

### 2. Ключові принципи та логіка

> [!info] Key Insights
> **Принцип 1: Dual-Purpose** — `Deque` замінює застарілий клас `Stack`, оскільки надає повний функціонал LIFO (Last-In-First-Out) через методи `push()`/`pop()`, які є аліасами для `addFirst()`/`removeFirst()`.
> 
> **Принцип 2: Capacity Management** — На відміну від стандартних реалізацій, `LinkedBlockingDeque` дозволяє задати жорсткий ліміт елементів для запобігання переповненню пам'яті (OutOfMemory).
> 
> **Логічний зв'язок**: `ArrayDeque` використовує "циклічний буфер", що робить операції з обома кінцями $O(1)$, уникаючи дорогого зсуву елементів масиву.

---

### 3. Критичні межі та безпека

> [!warning] Limits & Constraints
> - **Null Policy**: `ArrayDeque` категорично забороняє `null`. Це критично, оскільки `poll()` повертає `null` як маркер того, що черга порожня.
>     
> - **Capacity**: `ArrayDeque` автоматично подвоює розмір масиву, коли він заповнюється. Це займає $O(n)$ часу в момент розширення.
>     

> [!danger] Common Pitfalls
> **IllegalStateException у BlockingDeque**
> 
> - **Причина**: Виклик `addFirst()` у `LinkedBlockingDeque` з обмеженою ємністю, коли вона вже заповнена.
>     
> - **Наслідок**: Раптове завершення потоку з помилкою.
>     
> - **Рішення**: Використовувати `offerFirst()`, який повертає `false`, або блокуючий метод `putFirst()`.
>     

---
### 4. Implementation Details

> [!note] Method Signature: public boolean offerFirst(E e)
> **Логіка**: Намагається вставити елемент у голову. На відміну від `addFirst`, цей метод не "панікує" (не кидає Exception) при обмеженні ємності, а просто повертає логічне "брехня".
> 
> **Ризики**: `NullPointerException`, якщо передано `null` (для `ArrayDeque`).

# Java

```java
import java.util.*;
import java.util.concurrent.LinkedBlockingDeque;

/* Senior-level Deque Logic Breakdown */
public class DequeEngine {
    public static void main(String[] args) {
        // 1. ArrayDeque: Оптимальний вибір для стеків та черг (непотокобезпечний)
        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(10); // addFirst
        stack.push(20);
        System.out.println(stack.pop()); // 20 (LIFO)

        // 2. LinkedBlockingDeque: Потокобезпечна черга з лімітом
        // Встановлюємо ліміт у 2 елементи
        BlockingDeque<String> bounded = new LinkedBlockingDeque<>(2);
        
        bounded.offer("Alpha");
        bounded.offer("Beta");
        
        /* Ця спроба поверне false, а не виняток */
        boolean added = bounded.offer("Gamma"); 
        System.out.println("Is Gamma added? " + added); // false

        // 3. LinkedList як Deque: Коли потрібна ітерація в обидва боки
        Deque<String> listDeque = new LinkedList<>();
        listDeque.addFirst("Head");
        listDeque.addLast("Tail");
        
        /* Використання "під капотом" */
        // pollFirst() видаляє вузол і повертає значення
        String head = listDeque.pollFirst(); 
    }
}
```

---
# Time complexity

| Метод                        | ArrayDeque (Best/Worst) | LinkedList (Best/Worst) | Нюанс                                |
| ---------------------------- | ----------------------- | ----------------------- | ------------------------------------ |
| addFirst(e) / addLast(e)     | $O(1) / O(n)$           | $O(1) / O(1)$           | $O(n)$ у ArrayDeque лише при ресайзі |
| offerFirst(e) / offerLast(e) | $O(1) / O(n)$           | $O(1) / O(1)$           | Безпечна версія без Exception        |
| removeFirst() / removeLast() | $O(1) / O(1)$           | $O(1) / O(1)$           | LinkedList просто відв'язує вузол    |
| pollFirst() / pollLast()     | $O(1) / O(1)$           | $O(1) / O(1)$           | Повертає `null`, якщо порожньо       |
| getFirst() / getLast()       | $O(1) / O(1)$           | $O(1) / O(1)$           | Прямий доступ до посилань            |
| peekFirst() / peekLast()     | $O(1) / O(1)$           | $O(1) / O(1)$           | Без видалення                        |
| contains(o)                  | $O(n) / O(n)$           | $O(n) / O(n)$           | Повний перебір масиву/списку         |
|                              |                         |                         |                                      |
> [!quote] Що де краще?
> 
> **ArrayDeque** (Стек та стандартні черги)
> 
> Найкраще підходить для використання як **Stack** (заміна застарілому `Stack`) або **Queue**.
> 
> - **Чому**: Використовує циклічний масив, що забезпечує найвищу швидкість доступу до голови та хвоста.
>     
> - **Перевага**: Елементи лежать у пам'яті послідовно, що ідеально для кешу процесора (**Cache Locality**).
>     
> - **Операції**: `push`, `pop`, `poll`, `offer`.
>     
> 
> **LinkedList** (Складні маніпуляції з ітераторами)
> 
> Вигідніший лише тоді, коли вам потрібні специфічні можливості **двонаправленого списку**.
> 
> - **Чому**: Дозволяє видаляти елементи "на місці" під час ітерації без копіювання масиву.
>     
> - **Перевага**: Немає затримок на розширення (resize) масиву, пам'ять виділяється порційно.
>     
> - **Операції**: `remove()` під час ітерації через `DescendingIterator` або `ListIterator`.

---
# Connections:
 - [[Java Collections Framework (Interfaces)]]
 - [[Колекції (Collections)]]