> Графи в Java не мають вбудованих спеціалізованих типів у стандартній бібліотеці (JDK), проте їх можна ефективно реалізувати за допомогою класів та стандартних колекцій, моделюючи списки суміжності або використовуючи сторонні рішення.

---

> [!abstract] Core Framework
> 
> Колекції Java — класи, основна мета яких — зберігати набір елементів. У контексті графів вони виступають фундаментом для представлення топології:
> 
> - **Map<K, V>** — використовується як основний контейнер, де ключ ($K$) — це вершина, а значення ($V$) — структура суміжних вершин.
>     
> - **List / Set** — зберігають суміжні вершини або об'єкти ребер.
>     
> - **Абстракція ребра** — створення спеціалізованих класів для зберігання додаткових властивостей (наприклад, ваги).
>     

---

> [!info] Key Insights
> 
> - **Вибір структури.** Використання `List` у значенні `Map` підходить для орієнтованих графів, тоді як `Set` допомагає уникати дублікатів у неорієнтованих структурах.
>     
> - **Двонаправленість.** У неорієнтованих графах кожне ребро має бути представлено двома записами в структурі даних для забезпечення зв'язку в обох напрямках.
>     
> - **Масштабованість.** Для складних алгоритмічних задач (пошук найкоротшого шляху, мінімальний кістяк) ефективніше використовувати спеціалізовані бібліотеки, щоб не "винаходити велосипед" з нуля.
>     
> - **Об'єктна модель.** Введення класу `Edge` дозволяє зберігати метадані (вагу, мітки) безпосередньо у структурі графа.
>     

---

> [!example] Практичне застосування

### 1. Орієнтовані (напрямлені) графи

## **Напрямлені графи (орієнтовані графи)**:

- Ви можете використовувати структуру даних **Map<Integer, List<Integer\>>**, де ключі - це вершини, а значення - це списки суміжних вершин.
- Крім того, ви можете створити спеціалізований клас **DirectedGraph**, який містить список вершин та список ребер, де кожен ребро містить початкову та кінцеву вершину.

Кожна вершина посилається лише на ті вузли, до яких вона безпосередньо спрямована.

```java
import java.util.*;

public class DirectedGraphExample {
    public static void main(String[] args) {
        // Створення напрямленого графа
        Map<Integer, List<Integer>> graph = new HashMap<>();

        // Додавання ребер до графа
        addEdge(graph, 0, 1);
        addEdge(graph, 0, 2);
        addEdge(graph, 1, 3);
        addEdge(graph, 2, 1);
        addEdge(graph, 2, 3);

        // Виведення графа
        System.out.println("Напрямленний граф:");
        for (Map.Entry<Integer, List<Integer>> entry : graph.entrySet()) {
            int source = entry.getKey();
            List<Integer> destinations = entry.getValue();
            for (int destination : destinations) {
                System.out.println(source + " -> " + destination);
            }
        }
    }

    // Метод для додавання ребра до графа
    private static void addEdge(Map<Integer, List<Integer>> graph, int source, int destination) {
        graph.putIfAbsent(source, new ArrayList<>());
        graph.get(source).add(destination);
    }
}
```

```
Напрямленний граф:
0 -> 1
0 -> 2
1 -> 3
2 -> 1
2 -> 3
```

У цьому прикладі кожний рядок виводу вказує на наявність напрямленого зв'язку від однієї вершини до іншої. Наприклад, "0 -> 1" означає, що існує напрямлений зв'язок від вершини 0 до вершини 1.
### 2. Неорієнтовані (ненапрямлені) графи

## **Ненапрямлені графи**:

- Аналогічно до напрямлених графів, ви можете використовувати структуру даних **Map<Integer, List<Integer\>>**, але кожне ребро буде представлено двома записами для обох напрямків.
- Також можна створити клас **UndirectedGraph**, який буде містити список вершин та список ребер, де кожне ребро містить дві вершини.

Ребро діє в обох напрямках. Використання `Set` запобігає дублюванню однакових ребер.

```java
import java.util.*;

public class UndirectedGraphExample {
    public static void main(String[] args) {
        // Створення ненапрямленого графа
        Map<Integer, Set<Integer>> graph = new HashMap<>();

        // Додавання ребер до графа
        addEdge(graph, 0, 1);
        addEdge(graph, 0, 2);
        addEdge(graph, 1, 3);
        addEdge(graph, 2, 1);
        addEdge(graph, 2, 3);

        // Виведення графа
        System.out.println("Ненапрямлений граф:");
        for (Map.Entry<Integer, Set<Integer>> entry : graph.entrySet()) {
            int vertex = entry.getKey();
            Set<Integer> neighbors = entry.getValue();
            for (int neighbor : neighbors) {
                if (vertex < neighbor) {
                    System.out.println(vertex + " -- " + neighbor);
                }
            }
        }
    }

    // Метод для додавання ребра до графа
    private static void addEdge(Map<Integer, Set<Integer>> graph, int vertex1, int vertex2) {
        graph.putIfAbsent(vertex1, new HashSet<>());
        graph.putIfAbsent(vertex2, new HashSet<>());
        graph.get(vertex1).add(vertex2);
        graph.get(vertex2).add(vertex1);
    }
}
```

```
Ненапрямлений граф:
0 -- 1
0 -- 2
1 -- 3
2 -- 3
```

У цьому прикладі кожний рядок виводу вказує на наявність ребра між двома вершинами. Наприклад, "0 -- 1" означає, що існує ребро між вершинами 0 і 1 в обидва напрямки, оскільки граф ненапрямлений.
### 3. Ваговані графи

## **Ваговані графи**:

- Ви можете розширити структури для напрямлених або ненапрямлених графів, додавши до кожного ребра поле для збереження ваги.
- Наприклад, клас **WeightedEdge** може мати додаткове поле **weight**, а потім використовувати структуру **Map<Integer, List<WeightedEdge\>>** для зберігання списків ребер для кожної вершини.

Використання допоміжного класу для збереження вартості переходу.

```java
import java.util.*;

// Клас, що представляє ребро графа з вагою
class Edge {
    int source;
    int destination;
    int weight;

    Edge(int source, int destination, int weight) {
        this.source = source;
        this.destination = destination;
        this.weight = weight;
    }
}

public class WeightedGraphExample {
    public static void main(String[] args) {
        // Створення вагованого графа
        Map<Integer, List<Edge>> graph = new HashMap<>();

        // Додавання ребер до графа
        addEdge(graph, 0, 1, 5);
        addEdge(graph, 0, 2, 3);
        addEdge(graph, 1, 3, 6);
        addEdge(graph, 1, 4, 7);
        addEdge(graph, 2, 5, 9);
        addEdge(graph, 3, 6, 2);
        addEdge(graph, 4, 6, 1);
        addEdge(graph, 5, 6, 4);

        // Виведення графа
        System.out.println("Вагований граф:");
        for (Map.Entry<Integer, List<Edge>> entry : graph.entrySet()) {
            int vertex = entry.getKey();
            List<Edge> edges = entry.getValue();
            for (Edge edge : edges) {
                System.out.println(vertex + " --(" + edge.weight + ")-- " + edge.destination);
            }
        }
    }

    // Метод для додавання ребра до графа
    private static void addEdge(Map<Integer, List<Edge>> graph, int source, int destination, int weight) {
        graph.putIfAbsent(source, new ArrayList<>());
        graph.get(source).add(new Edge(source, destination, weight));
    }
}
```

```
Вагований граф:
0 --(5)-- 1
0 --(3)-- 2
1 --(6)-- 3
1 --(7)-- 4
2 --(9)-- 5
3 --(2)-- 6
4 --(1)-- 6
5 --(4)-- 6
```

У цьому прикладі кожний рядок виводу показує наявність ребра між двома вершинами разом із їхньою вагою. Наприклад, "0 --(5)-- 1" означає, що існує ребро між вершинами 0 і 1 з вагою 5.
### 4. Бібліотека JGraphT

## **JGraphT**
- це бібліотека для роботи з графами у Java. Вона надає різноманітні інтерфейси та імплементації для роботи з графами, включаючи різні типи графів, алгоритми пошуку шляхів, алгоритми мінімального каркасу і т. д.

Професійне рішення для складних операцій.

```java
import org.jgrapht.*;
import org.jgrapht.graph.*;

public class JGraphTExample {
    public static void main(String[] args) {
        // Створення напрямленого графа
        DirectedWeightedGraph<Integer, DefaultWeightedEdge> graph =
                new DirectedWeightedGraph<>(DefaultWeightedEdge.class);

        // Додавання вершин до графа
        for (int i = 0; i < 7; i++) {
            graph.addVertex(i);
        }

        // Додавання ребер до графа
        graph.addEdge(0, 1);
        graph.setEdgeWeight(graph.addEdge(0, 1), 5); // Вага ребра від вершини 0 до 1
        graph.setEdgeWeight(graph.addEdge(0, 2), 3); // Вага ребра від вершини 0 до 2
        graph.setEdgeWeight(graph.addEdge(1, 3), 6);
        graph.setEdgeWeight(graph.addEdge(1, 4), 7);
        graph.setEdgeWeight(graph.addEdge(2, 5), 9);
        graph.setEdgeWeight(graph.addEdge(3, 6), 2);
        graph.setEdgeWeight(graph.addEdge(4, 6), 1);
        graph.setEdgeWeight(graph.addEdge(5, 6), 4);

        // Виведення графа
        System.out.println("Напрямлений вагований граф:");
        System.out.println(graph);
    }
}
```

```
Напрямлений вагований граф:
([0, 1, 2, 3, 4, 5, 6], [{0,1}, {1,3}, {1,4}, {2,5}, {3,6}, {4,6}, {5,6}])
```

У цьому прикладі ми використали **DirectedWeightedGraph**, який представляє напрямлений граф з вагами ребер. Ми додали вершини та ребра до графа, встановивши їхні ваги за допомогою методу **setEdgeWeight**.

---

> [!warning] Обмеження та ризики
> 
> - **Відсутність нативної підтримки.** Оскільки в стандартній Java немає типу `Graph`, розробник відповідальний за підтримку цілісності даних (наприклад, видалення ребер при видаленні вершини).
>     
> - **Продуктивність.** При використанні `Map<Integer, List<Integer>>` операція перевірки існування ребра має складність $O(k)$, де $k$ — ступінь вершини. Для дуже щільних графів це може бути менш ефективно, ніж матриця суміжності.
>     
> - **Складність реалізації.** Самостійне написання алгоритмів (Dijkstra, BFS/DFS) підвищує ризик помилок у порівнянні з використанням JGraphT.
>     

---

> [!tip] Коли використовувати
> 
> Так ✅:
> 
> - Потрібна легка структура для простого збереження зв'язків.
>     
> - Граф розріджений (sparse graph) — `Map` економить пам'ять.
>     
> - Необхідна гнучкість у визначенні атрибутів вершин та ребер.
>     
> 
> Ні ❌:
> 
> - Потрібні складні математичні обчислення (краще JGraphT).
>     
> - Граф надзвичайно великий і вимагає специфічної оптимізації пам'яті на рівні примітивів.
>     

---
# Connections:
- [[Ациклічний граф (Acyclic Graph)]]
- [[Графи (Graph)]]
- [[Дерева (Trees)]]
- [[Створення дерев (Trees)]]
- [[Колекції (Collections)]]
- [[Java Collections Framework (Interfaces)]]