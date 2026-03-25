## Interface List та його реалізації.

`Interface List<E>` , де `E` – тип елементів у цьому списку, містить, засновані на індексі, елементи, методи для вставки та видалення елементів.**

Популярні реалізації `Interface List` : `Class ArrayList` , `Class LinkedList` .

`Class ArrayList<E>`, де `E` – тип елементів у цьому списку, є простим списком об'єктів. Список, який дуже схожий на масив, але має довільну довжину та цілий ряд методів, які дозволяють додавати, видаляти, замінювати елементи списку у будь-який час та у будь-якому місці.

`Class LinkedList<E>`, де `E` – тип елементів у цьому списку, представляє пов'язаний список. `Class LinkedList` реалізує ті самі методи, що і `Class ArrayList`, а також має певні додаткові методи. Для внутрішнього представлення даних використовує двонаправлений список.

**Наведемо приклади реалізацій,** використовуючи `String` або `Integer` у якості типів, з якими працює колекція.

```

import java.util.ArrayList;
import java.util.List;

// Метод add(E e) додає елемент у кінець списку.
public class Main {

    public static void main(String[] args) {
        // Створення List через конструктор ArrayList
        List<String> list = new ArrayList<>();
        // Додавання елементів до List
        list.add("orange");
        list.add("banana");
        list.add("kiwi");
        list.add("banana"); // <- Дублікат дозволено
        list.add("apple");
        // Виведення елементів
        System.out.println("Data: " + list);
    }
}
```


Результат


```
Data: [orange, banana, kiwi, banana, apple]
```

```
import java.util.ArrayList;
import java.util.List;

// Метод add(int index, E element) всталяє елемент
// у вказану позицію (індекс) в списку.
public class Main {

    public static void main(String[] args) {
        // Створення List через конструктор ArrayList
        List<String> list = new ArrayList<>();
        // Додавання елементів до List
        list.add("orange");
        list.add("banana");
        list.add("kiwi");
        // Додавання до певної позиції
        list.add(1, "mango");
        list.add("apple");
        // Виведення елементів
        System.out.println("Data: " + list);
    }
}
```



Результат



```
Data: [orange, mango, banana, kiwi, apple]
```



```
import java.util.ArrayList;
import java.util.List;

// Метод addAll(Collection<? extends E> c)
// вставляє всі елементи одного списку в кінець іншого.
// Метод addAll(int index, Collection<? extends E> c)
// вставляє всі елементи одного списку в інший список,
// починаючи з указаної позиції (індексу).
public class Main {

    public static void main(String[] args) {

        // Список 1.
        List<Integer> list1 = new ArrayList<>();
        // Виведення списку 1. Має бути порожнім.
        System.out.println("1) Data: " + list1);

        // Додавання даних у список 1.
        list1.add(22);
        list1.add(31);
        list1.add(5);
        list1.add(15);
        // Виведення списку 1 після додання даних.
        System.out.println("2) Data: " + list1);

        // Додаємо у список 1 дані за певною позицією.
        list1.add(2, 92);
        // Виведення списку 1 після додання даних за позицією.
        System.out.println("3) Data: " + list1);

        // Список 2.
        List<Integer> list2 = new ArrayList<>();
        // Додавання даних у список 2.
        list2.add(41);
        list2.add(12);

        // Додаємо список 2 у список 1.
        list1.addAll(list2);
        // Виведення списку 1 після додання списку 2.
        System.out.println("4) Data: " + list1);

        // Список 3
        ArrayList<Integer> list3 = new ArrayList<>();
        list3.add(10);
        list3.add(21);

        // Додаємо список 3 в попередньо модифікований
        // список 1 за певним індексом.
        list1.addAll(1, list3);
        // Виведення списку 1 після додання списку 3.
        System.out.println("5) Data: " + list1);

    }
}
```



Результат


```
1) Data: []
2) Data: [22, 31, 5, 15]
3) Data: [22, 31, 92, 5, 15]
4) Data: [22, 31, 92, 5, 15, 41, 12]
5) Data: [22, 10, 21, 31, 92, 5, 15, 41, 12]
```



```
import java.util.ArrayList;
import java.util.List;

// Метод clear() видаляє всі елементи
// зі списку (очищає список).
public class Main {

    public static void main(String[] args) {

        // Створення List через конструктор ArrayList
        List<String> list = new ArrayList<>();
        // Додавання елементів до List
        list.add("orange");
        list.add("banana");
        list.add("kiwi");
        list.add("banana"); // <- Дублікат дозволено
        list.add("apple");

        // Попереднє виведення списку.
        System.out.println("1) Data: " + list);
        // Видалення всіх елементів зі списку (очищення списку).
        list.clear();
        // Виведення списку після очищення.
        // Тобто об'єкт списку становиться просто порожнім,
        // але не видаляється.
        System.out.println("2) Data: " + list);
    }
}
```



Результат



```
1) Data: [orange, banana, kiwi, banana, apple]
2) Data: []
```



```
import java.util.ArrayList;
import java.util.List;

// Метод contains(Object o)
// повертає true, якщо список містить
// вказаний елемент.
public class Main {

    public static void main(String[] args) {

        // Створення List через конструктор ArrayList
        List<Integer> list = new ArrayList<>();
        // Додавання елементів до List
        list.add(23);
        list.add(17);
        list.add(5);
        list.add(38);

        // Виведення елементів
        System.out.println("Data: " + list);

        // Визначення чи містить список певний елемент.
        Integer item = 17;
        if (list.contains(item)) {
            System.out.println("List contains " + item);
        } else {
            System.out.println("List does not contain " + item);
        }
    }
}
```



Результат



```
Data: [23, 17, 5, 38]
List contains 17
```



```
import java.util.ArrayList;
import java.util.List;

// Метод containsAll(Collection<?> c)
// повертає true, якщо один список містить
// усі елементи іншої колекції.
public class Main {

    public static void main(String[] args) {

        // Створення List через конструктор ArrayList
        List<String> list1 = new ArrayList<>();
        // Додавання елементів до List
        list1.add("orange");
        list1.add("banana");
        list1.add("kiwi");
        list1.add("apple");
        list1.add("mango");
        list1.add("grape");
        // Виведення списку 1.
        System.out.println("1) Data: " + list1);

        // Створення List через конструктор ArrayList
        List<String> list2 = new ArrayList<>();
        // Додавання елементів до List
        list2.add("orange");
        list2.add("kiwi");
        list2.add("grape");
        // Виведення списку 2.
        System.out.println("2) Data: " + list2);

        // Визначення чи містить список 1 елементи списку 2.
        if (list1.containsAll(list2)) {
            System.out.println("List contains list 2");
        } else {
            System.out.println("List does not contain list 2");
        }
    }
}
```



Результат


```
1) Data: [orange, banana, kiwi, apple, mango, grape]
2) Data: [orange, kiwi, grape]
List contains list 2
```



```
import java.util.ArrayList;
import java.util.List;

// Метод get(int index) повертає елемент
// у вказаній позиції (індексу) в списку.
// Викидає IndexOutOfBoundsException - якщо
// індекс виходить за межі діапазону індексів
// в списку.
public class Main {

    public static void main(String[] args) {

        // Створення List через конструктор ArrayList
        List<String> list = new ArrayList<>();
        // Додавання елементів до списку
        list.add("orange"); // <- Index 0
        list.add("banana"); // <- Index 1
        list.add("kiwi"); // <- Index 2
        list.add("apple"); // <- Index 3

        // Виведення елементів
        System.out.println("Data: " + list);
        // Отримання елементу з певної позиції в списку
        String item = list.get(2);
        // Виведення елементу
        System.out.println("Result is " + item);

        // Намагаємося отримати елемент за індексом, який
        // є за межами діапазону індексів.
        // Маємо обробити виняток.
        try {
            item = list.get(9);
            System.out.println("Result is " + item);
        } catch (IndexOutOfBoundsException e) {
            System.out.println("No data!");
        }
    }
}
```



Результат



```
Data: [orange, banana, kiwi, apple]
Result is kiwi
No data!
```



```
import java.util.ArrayList;
import java.util.List;

// Метод indexOf(Object o) повертає індекс
// першого входження зазначеного елемента
// в списку або -1, якщо список не містить
// елемент.
public class Main {

    public static void main(String[] args) {

        // Створення List через конструктор ArrayList
        List<Integer> list = new ArrayList<>();
        // Додавання елементів до List
        list.add(25);
        list.add(17); // <- Входження 1 (індекс 1)
        list.add(23);
        list.add(17); // <- Входження 2 (індекс 3)
        list.add(98);
        list.add(5);

        // Виведення елементів
        System.out.println("Data: " + list);
        // Отримання індексу першого входження
        // зазначеного елемента зі списку
        int index = list.indexOf(17);
        // Виведення індексу
        System.out.println("Index is " + index);
    }
}
```



Результат



```
Data: [25, 17, 23, 17, 98, 5]
Index is 1
```



```
import java.util.ArrayList;
import java.util.List;

// Метод lastIndexOf(Object o) повертає індекс
// останнього входження зазначеного елемента
// в списку або -1, якщо список не містить елемент.
public class Main {

    public static void main(String[] args) {

        // Створення List через конструктор ArrayList
        List<Integer> list = new ArrayList<>();
        // Додавання елементів до List
        list.add(25);
        list.add(17); // <- Входження 1 (індекс 1)
        list.add(23);
        list.add(17); // <- Входження 2 (індекс 3)
        list.add(98);
        list.add(5);

        // Виведення елементів
        System.out.println("Data: " + list);
        // Отримання індексу останнього входження
        // зазначеного елемента зі списку
        int index = list.lastIndexOf(17);
        // Виведення індексу
        System.out.println("Index is " + index);
    }
}
```



Результат



```
Data: [25, 17, 23, 17, 98, 5]
Index is 3
```



```
import java.util.ArrayList;
import java.util.List;

// Метод isEmpty()
// повертає true, якщо список не містить елементів.
public class Main {

    public static void main(String[] args) {

        // Створення List через конструктор ArrayList
        List<String> list = new ArrayList<>();

        // Виведення порожнього списку
        System.out.println("1) Data: " + list);

        // Визначення чи містить список елементи.
        if (list.isEmpty()) {
            System.out.println("List is empty");
        } else {
            System.out.println("List is full");
        }

        // Додавання елементів до списку
        list.add("orange");
        list.add("banana");
        list.add("kiwi");
        list.add("apple");

        // Виведення повного списку
        System.out.println("2) Data: " + list);

        // Визначення чи містить список елементи.
        if (list.isEmpty()) {
            System.out.println("List is empty");
        } else {
            System.out.println("List is full");
        }
    }
}
```



Результат



```
1) Data: []
List is empty
2) Data: [orange, banana, kiwi, apple]
List is full
```



```
import java.util.ArrayList;
import java.util.List;

// Метод remove(int index) видаляє елемент
// у вказаній позиції (індексом) в списку.
// Викидає IndexOutOfBoundsException - якщо
// індекс виходить за межі діапазону індексів
// в списку.
// Метод remove(Object o) видаляє перше входження
// зазначеного елемента зі списку, якщо він присутній.
// Викидає NullPointerException - якщо вказаний елемент
// має значення null.
public class Main {

    public static void main(String[] args) {

        // Створення List через конструктор ArrayList
        List<String> list = new ArrayList<>();
        // Додавання елементів до списку
        list.add("orange");
        list.add("banana"); // <- Входження 1
        list.add("mango");
        list.add("kiwi");
        list.add(null);
        list.add("banana"); // <- Входження 2
        list.add("apple");
        // Виведення елементів
        System.out.println("1) Data: " + list);

        // Видалення за індексом
        list.remove(2);
        // Виведення елементів
        System.out.println("2) Data: " + list);

        // Видалення за першим входженням елементу
        list.remove("banana");
        // Виведення елементів
        System.out.println("3) Data: " + list);

        // Видалення за першим (воно ж і єдине) входженням елементу
        list.remove("orange");
        // Виведення елементів
        System.out.println("4) Data: " + list);

        // Видалення елементу, якого немає.
        // Нічого не відбувається.
        list.remove("grape");
        // Виведення елементів
        System.out.println("5) Data: " + list);

        // Видалення елементу, значення якого null.
        list.remove(null);
        // Виведення елементів
        System.out.println("6) Data: " + list);

        // Видалення за індексом, якого немає.
        // Маємо обробити виняток.
        try {
            list.remove(9);
        } catch (IndexOutOfBoundsException e) {
            System.out.println("Exception: " + e.getMessage());
        }
        // Виведення елементів
        System.out.println("7) Data: " + list);
    }
}
```



Результат


```
1) Data: [orange, banana, mango, kiwi, null, banana, apple]
2) Data: [orange, banana, kiwi, null, banana, apple]
3) Data: [orange, kiwi, null, banana, apple]
4) Data: [kiwi, null, banana, apple]
5) Data: [kiwi, null, banana, apple]
6) Data: [kiwi, banana, apple]
Exception: Index 9 out of bounds for length 3
7) Data: [kiwi, banana, apple]
```



```
import java.util.ArrayList;
import java.util.List;

// Метод removeAll(Collection<?> c) видаляє
// зі списку всі його елементи, які містяться
// у вказаній колекції.
public class Main {

    public static void main(String[] args) {

        // Створення списку 1 через конструктор ArrayList
        List<String> list1 = new ArrayList<>();
        // Додавання елементів до списку 1
        list1.add("orange");
        list1.add("banana");
        list1.add("mango");
        list1.add("kiwi");
        list1.add("apple");
        // Виведення елементів списку 1
        System.out.println("1) Data: " + list1);

        // Створення списку 2 через конструктор ArrayList
        List<String> list2 = new ArrayList<>();
        // Додавання елементів до списку 2
        list2.add("banana");
        list2.add("kiwi");
        // Виведення елементів списку 2
        System.out.println("2) Data: " + list2);

        // Видалення елементів списку 1 за списком 2
        list1.removeAll(list2);
        // Виведення елементів оновленого списку 1
        System.out.println("3) Data: " + list1);

    }
}
```



Результат



```
1) Data: [orange, banana, mango, kiwi, apple]
2) Data: [banana, kiwi]
3) Data: [orange, mango, apple]
```



```
import java.util.ArrayList;
import java.util.List;

// Метод retainAll(Collection<?> c) залишає
// лише ті елементи списку, які містяться
// у вказаній колекції.
public class Main {

    public static void main(String[] args) {

        // Створення списку 1 через конструктор ArrayList
        List<String> list1 = new ArrayList<>();
        // Додавання елементів до списку 1
        list1.add("orange");
        list1.add("banana");
        list1.add("mango");
        list1.add("kiwi");
        list1.add("apple");
        // Виведення елементів списку 1
        System.out.println("1) Data: " + list1);

        // Створення списку 2 через конструктор ArrayList
        List<String> list2 = new ArrayList<>();
        // Додавання елементів до списку 2
        list2.add("banana");
        list2.add("kiwi");
        // Виведення елементів списку 2
        System.out.println("2) Data: " + list2);

        // Залишаємо елементи в списку 1 за списком 2
        list1.retainAll(list2);
        // Виведення елементів оновленого списку 1
        System.out.println("3) Data: " + list1);

    }
}
```



Результат



```
1) Data: [orange, banana, mango, kiwi, apple]
2) Data: [banana, kiwi]
3) Data: [banana, kiwi]
```



```
import java.util.ArrayList;
import java.util.List;

// Метод set(int index, E element) замінює
// елемент у вказаній позиції (індексу) в списку
// вказаним елементом.
public class Main {

    public static void main(String[] args) {

        // Створення списку через конструктор ArrayList
        List<Integer> list = new ArrayList<>();
        // Додавання елементів до List
        list.add(23);
        list.add(17);
        list.add(35);
        list.add(8);
        // Виведення елементів
        System.out.println("1) Data: " + list);

        // Заміна елементу за індексом
        list.set(2, 49);
        // Виведення елементів
        System.out.println("2) Data: " + list);

        // Заміна елементу за індексом, якого немає.
        // Маємо обробити виняток.
        try {
            list.set(9, 56);
        } catch (IndexOutOfBoundsException e) {
            System.out.println("Exception: " + e.getMessage());
        }
        // Виведення елементів
        System.out.println("3) Data: " + list);
    }
}
```



Результат



```
1) Data: [23, 17, 35, 8]
2) Data: [23, 17, 49, 8]
Exception: Index 9 out of bounds for length 4
3) Data: [23, 17, 49, 8]
```



```
import java.util.ArrayList;
import java.util.List;

// Метод subList(int fromIndex, int toIndex)
// повертає частину списку між вказаним fromIndex, включно,
// та toIndex, виключно.
// Якщо fromIndex та toIndex рівні, повернутий список порожній.
public class Main {

    public static void main(String[] args) {

        List<Integer> list = new ArrayList<>();
        list.add(23);
        list.add(17);
        list.add(35);
        list.add(8);
        list.add(39);

        // Виведення всіх елементів
        System.out.println("1) Data: " + list);

        // Виведення частини елементів.
        // fromIndex є 1, toIndex є 3.
        System.out.println("2) Data: " + list.subList(1, 3));

        // Виведення порожнього списку,
        // оскільки fromIndex та toIndex рівні.
        System.out.println("3) Data: " + list.subList(2, 2));

    }
}
```



Результат



```
1) Data: [23, 17, 35, 8, 39]
2) Data: [17, 35]
3) Data: []
```



```
import java.util.ArrayList;
import java.util.List;

// Ітерація (перебирання) елементів списку
public class Main {

    public static void main(String[] args) {

        // Створення списку через конструктор ArrayList
        List<String> list = new ArrayList<>();
        // Додавання String елементів до списку
        list.add("orange");
        list.add("banana");
        list.add("kiwi");
        list.add("banana"); // <- Дублікат дозволено
        list.add("apple");

        // Виведення елементів через цикл for.
        // Метод size() повертає кількість елементів
        // у списку.
        // Метод get() повертає елемент у вказаній
        // позиції (індексі) в списку.
        System.out.println("\nA. Data:");
        for (int i = 0; i < list.size(); i++)
            System.out.println(list.get(i));

        // Виведення всіх елементів через цикл for-each.
        System.out.println("\nB. Data:");
        for (String item : list)
            System.out.println(item);

        // Виведення всіх елементів через цикл for-each,
        // з нумерацією елементів.
        int count = 0; // <- Лічильник
        System.out.println("\nC. Data:");
        for (String item : list) {
            count++;
            System.out.println(count + ") " + item);
        }

        // Метод forEach(Consumer<? super E> action)
        // виконує задану дію для кожного елемента списку,
        // доки всі елементи не будуть оброблені або дія
        // не викличе виняткову ситуацію.
        // Тут - виведення всіх елементів списку.
        System.out.println("\nD. Data:");
        list.forEach(
                // Лямбда-вираз, який стосується галузі
                // функціонального програмування (розглядається окремо).
                item -> System.out.println("Item is " + item)
        );

    }
}
```

Результат



```

A. Data:
orange
banana
kiwi
banana
apple

B. Data:
orange
banana
kiwi
banana
apple

C. Data:
1) orange
2) banana
3) kiwi
4) banana
5) apple

D. Data:
Item is orange
Item is banana
Item is kiwi
Item is banana
Item is apple
```



```

import java.util.ArrayList;
import java.util.ConcurrentModificationException;
import java.util.List;

// Якщо під час обробки списку (тут, проходження по списку)
// втручається певна дія над цим списком (тут, видалення елементу),
// то може бути викинутий виняток ConcurrentModificationException.
// Цей виняток може бути створено методами, які виявили одночасну модифікацію
// об’єкта (тут, типу List), коли така модифікація неприпустима.
// Наприклад, загалом заборонено, щоб один потік змінював колекцію, поки інший
// потік виконує її.
// https://docs.oracle.com/javase/8/docs/api/java/util/ConcurrentModificationException.html
// Виняток ConcurrentModificationException торкається теми багатопотоковості
// (розглядається окремо).
public class Main {

    public static void main(String[] args) {
        // Створення списку через конструктор ArrayList
        List<String> list = new ArrayList<>();
        // Додавання елементів до списку
        list.add("orange");
        list.add("banana");
        list.add("mango");
        list.add("kiwi");
        // Виведення списку
        System.out.println("\n1) DATA:");
        for (String item : list) {
            System.out.println(item);
        }

        // Намагаємося пройти списком, одночасно видаляючи
        // певний елемент. Маємо обробити можливий виняток.
        System.out.println("\n2) DATA:");
        try {
            for (String item : list) {
                list.remove(2);
                System.out.println(item);
            }
        } catch (ConcurrentModificationException e) {
            System.out.println("Exception: " + e.getMessage());
        }

        // Елегантне рішення через Collection.removeIf,
        // для запобігання ConcurrentModificationException.
        list.removeIf(
                // Лямбда-вираз, який стосується галузі
                // функціонального програмування (розглядається окремо).
                item -> item.equals("mango")
        );
        // Виведення списку
        System.out.println("\n3) DATA:");
        for (String item : list) {
            System.out.println(item);
        }
    }
}
```


Результат



```

1) DATA:
orange
banana
mango
kiwi

2) DATA:
orange
Exception: null

3) DATA:
orange
banana
kiwi
```


```
import java.util.LinkedList;
import java.util.List;

// Метод add(E e) додає елемент у кінець списку.
public class Main {

    public static void main(String[] args) {

        // Створення List через конструктор LinkedList
        List<String> list = new LinkedList<>();
        // Додавання елементів до List
        list.add("orange");
        list.add("banana");
        list.add("kiwi");
        list.add("banana"); // <- Дублікат дозволено
        list.add("apple");
        // Виведення елементів
        System.out.println("Data: " + list);
    }
}
```



Результат



```
Data: [orange, banana, kiwi, banana, apple]
```



```
import java.util.LinkedList;
import java.util.List;

// Метод addFirst(E e) вставляє вказаний елемент
// на початок списку.
public class Main {

    public static void main(String[] args) {
        // Створення списку через конструктор LinkedList
        List<String> list = new LinkedList<>();
        // Додавання елементів до списку
        list.add("orange");
        list.add("banana");
        list.add("kiwi");
        list.add("apple");
        // Виведення елементів
        System.out.println("1) Data: " + list);
        // Вставлення елементу
        list.addFirst("mango");
        // Виведення елементів
        System.out.println("2) Data: " + list);
    }
}
```



Результат



```
1) Data: [orange, banana, kiwi, apple]
2) Data: [mango, orange, banana, kiwi, apple]
```


```
import java.util.LinkedList;
import java.util.List;

// Метод addLast(E e) додає вказаний елемент
// у кінець списку.
public class Main {

    public static void main(String[] args) {
        // Створення списку через конструктор LinkedList
        List<String> list = new LinkedList<>();
        // Додавання елементів до списку
        list.add("orange");
        list.add("banana");
        list.add("kiwi");
        list.add("apple");
        // Виведення елементів
        System.out.println("1) Data: " + list);
        // Додавання елементу
        list.addLast("mango");
        // Виведення елементів
        System.out.println("2) Data: " + list);
    }
}
```


Результат



```
1) Data: [orange, banana, kiwi, apple]
2) Data: [orange, banana, kiwi, apple, mango]
```


```
import java.util.LinkedList;
import java.util.List;

// Метод getFirst() повертає перший елемент списку.
public class Main {

    public static void main(String[] args) {
        // Створення списку через конструктор LinkedList
        List<Integer> list = new LinkedList<>();
        // Додавання елементів до списку
        list.add(25);
        list.add(17);
        list.add(98);
        list.add(5);
        // Виведення елементів
        System.out.println("Data: " + list);
        // Повернення елементу
        Integer item = list.getFirst();
        // Виведення елементу
        System.out.println("Item: " + item);
    }
}
```



Результат



```
Data: [25, 17, 98, 5]
Item: 25
```



```
import java.util.LinkedList;
import java.util.List;

// Метод getLast() повертає останній елемент списку.
public class Main {

    public static void main(String[] args) {
        // Створення списку через конструктор LinkedList
        List<Integer> list = new LinkedList<>();
        // Додавання елементів до списку
        list.add(25);
        list.add(17);
        list.add(98);
        list.add(5);
        // Виведення елементів
        System.out.println("Data: " + list);
        // Повернення елементу
        Integer item = list.getLast();
        // Виведення елементу
        System.out.println("Item: " + item);
    }
}
```



Результат



```
Data: [25, 17, 98, 5]
Item: 5
```



```
import java.util.LinkedList;
import java.util.List;
import java.util.NoSuchElementException;

// Метод removeFirst() вилучає та повертає
// перший елемент із списку.
// Викидає NoSuchElementException - якщо список
// порожній.
public class Main {

    public static void main(String[] args) {

        // Створення списку через конструктор LinkedList
        List<String> list = new LinkedList<>();
        // Додавання елементів до списку
        list.add("orange");
        list.add("banana");
        list.add("kiwi");
        list.add("apple");
        // Виведення елементів
        System.out.println("1) Data: " + list);

        // Вилучаємо та повертаємо елемент із списку.
        String item = list.removeFirst();
        // Виведення елементу
        System.out.println("Item: " + item);

        // Виведення елементів
        System.out.println("2) Data: " + list);

        // Очищуємо список.
        list.clear();
        // Список - порожній.
        System.out.println("3) Data: " + list);

        // Намагаємося отримати елемент із порожнього списку.
        // Одночасно обробляємо виняток.
        try {
            item = list.removeFirst();
            System.out.println("Item: " + item);
        } catch (NoSuchElementException e) {
            System.out.println("No data!");
        }
    }
}
```



Результат



```
1) Data: [orange, banana, kiwi, apple]
Item: orange
2) Data: [banana, kiwi, apple]
3) Data: []
No data!
```



```
import java.util.LinkedList;
import java.util.List;
import java.util.NoSuchElementException;

// Метод removeLast() вилучає та повертає
// останній елемент із списку.
// Викидає NoSuchElementException - якщо список
// порожній.
public class Main {

    public static void main(String[] args) {

        // Створення списку через конструктор LinkedList
        List<String> list = new LinkedList<>();
        // Додавання елементів до списку
        list.add("orange");
        list.add("banana");
        list.add("kiwi");
        list.add("apple");
        // Виведення елементів
        System.out.println("1) Data: " + list);

        // Вилучаємо та повертаємо елемент із списку.
        String item = list.removeLast();
        // Виведення елементу
        System.out.println("Item: " + item);

        // Виведення елементів
        System.out.println("2) Data: " + list);

        // Очищуємо список.
        list.clear();
        // Список - порожній.
        System.out.println("3) Data: " + list);

        // Намагаємося отримати елемент із порожнього списку.
        // Одночасно обробляємо виняток.
        try {
            item = list.removeLast();
            System.out.println("Item: " + item);
        } catch (NoSuchElementException e) {
            System.out.println("No data!");
        }
    }
}
```


Результат


```
1) Data: [orange, banana, kiwi, apple]
Item: apple
2) Data: [orange, banana, kiwi]
3) Data: []
No data!
```