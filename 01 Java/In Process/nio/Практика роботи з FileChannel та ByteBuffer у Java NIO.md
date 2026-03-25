> Використання `FileChannel` разом із `ByteBuffer` дозволяє реалізувати ефективний обмін даними між пам'яттю програми та файловою системою. Основний принцип полягає у використанні буфера як посередника: ми заповнюємо його даними, "перевертаємо" (`flip`) для зміни режиму, і передаємо каналу для запису або читання.

---

> [!abstract] Core Framework
> 
> Для маніпуляцій з файлами через NIO використовуються такі ключові методи:
> 
> - **`ByteBuffer.allocate(int capacity)`**: Виділення пам'яті під буфер фіксованого розміру.
>     
> - **`fos.getChannel()` / `fis.getChannel()`**: Отримання каналу зі стандартних потоків введення-виведення.
>     
> - **`fileChannel.write(buffer)`**: Запис вмісту буфера у файл.
>     
> - **`fileChannel.read(buffer)`**: Зчитування даних із файлу в буфер. Повертає кількість зчитаних байтів або `-1`, якщо досягнуто кінця файлу.
>     

---

> [!info] Key Insights
> 
> - **Життєвий цикл буфера при записі:** `allocate()` (створення) $\rightarrow$ `put()` (заповнення даними) $\rightarrow$ `flip()` (підготовка до читання каналом) $\rightarrow$ `channel.write()`.
>     
> - **Життєвий цикл буфера при читанні:** `channel.read()` (заповнення буфера з файлу) $\rightarrow$ `flip()` (підготовка до вичитування програмою) $\rightarrow$ `clear()` (скидання індексів для наступної порції даних).
>     
> - **Кодування:** При читанні важливо використовувати `Charset.defaultCharset().decode(buffer)`, щоб коректно перетворити байти назад у текст (рядки).
>     

---

> [!example] Практичне застосування
> 
> **1. Запис у файл за допомогою FileChannel**
> 
> Java
> 
> ```java
> import java.io.FileNotFoundException;
> import java.io.FileOutputStream;
> import java.io.IOException;
> import java.nio.ByteBuffer;
> import java.nio.channels.FileChannel;
> 
> /* Створення та запис у файл */
> public class Main {
> 
>     private static final String BASE_PATH = "/Username/files/";
> 
>     public static void main(String[] args) throws IOException {
>         String content = "Some information.";
>         String filePath = BASE_PATH + "myfile.txt";
>         getOutput(writeToFile(content, filePath));
>     }
> 
>     public static String writeToFile(String content, String filePath)
>             throws IOException {
> 
>         FileChannel fileChannel;
> 
>         try (FileOutputStream fos = new FileOutputStream(filePath)) {
>             /* Кодуємо рядковий контент у послідовність байтів */
>             byte[] contentBytes = content.getBytes();
>             
>             /* Виділяємо новий байтовий буфер через метод allocate() */
>             ByteBuffer buffer = ByteBuffer.allocate(contentBytes.length);
>             
>             /* Передаємо вміст масиву байтів у цей буфер через метод put() */
>             buffer.put(contentBytes);
>             
>             /* Отримання FileChannel через FileOutputStream */
>             fileChannel = fos.getChannel();
>             
>             /* Готуємо буфер до читання каналом (flip) */
>             buffer.flip();
>             
>             /* Метод write() записує дані з буфера у файл */
>             fileChannel.write(buffer);
>         } catch (FileNotFoundException e) {
>             return e.getMessage();
>         }
>         /* Закриваємо канал */
>         fileChannel.close();
>         return "The content has been written.";
>     }
> 
>     public static void getOutput(String output) {
>         System.out.println(output);
>     }
> }
> ```
> 
> **2. Читання з файлу за допомогою FileChannel**
> 
> Java
> 
> ```java
> import java.io.FileInputStream;
> import java.io.FileNotFoundException;
> import java.io.IOException;
> import java.nio.ByteBuffer;
> import java.nio.channels.FileChannel;
> import java.nio.charset.Charset;
> 
> /* Читання з файлу */
> public class Main {
> 
>     private static final String BASE_PATH = "/Username/files/";
> 
>     public static void main(String[] args) throws IOException {
>         String filePath = BASE_PATH + "myfile.txt";
>         getOutput(readFile(filePath));
>     }
> 
>     public static String readFile(String filePath) throws IOException {
> 
>         FileChannel channel;
>         StringBuilder builder;
> 
>         try(FileInputStream fis = new FileInputStream(filePath)) {
>             /* Отримання FileChannel через FileInputStream */
>             channel = fis.getChannel();
>             
>             /* Виділяємо буфер ємністю 128 байт */
>             ByteBuffer buffer = ByteBuffer.allocate(128);
>             builder = new StringBuilder();
>             
>             /* Метод read() повертає кількість зчитаних байтів. -1 означає EOF */
>             while (channel.read(buffer) != -1) {
>                 /* Підготовка буфера для зчитування даних (flip) */
>                 buffer.flip();
>                 
>                 /* Декодування байтів у символи за допомогою Charset */
>                 builder.append(Charset.defaultCharset().decode(buffer));
>                 
>                 /* Очищення показників для наступного циклу запису в буфер */
>                 buffer.clear();
>             }
>         } catch (FileNotFoundException e) {
>             return e.getMessage();
>         }
>         /* Закриваємо канал */
>         channel.close();
>         return builder.toString();
>     }
> 
>     public static void getOutput(String output) {
>         System.out.println(output);
>     }
> }
> ```

---

> [!warning] Важливі нюанси
> 
> - **Ресурсний менеджмент**: Використання блоку `try-with-resources` для `FileOutputStream`/`FileInputStream` автоматично закриває потоки, але канал `FileChannel` рекомендується закривати явно, якщо він не є частиною ресурсу, що закривається.
>     
> - **Циклічне читання**: Якщо файл великий, а буфер малий, цикл `while (channel.read(buffer) != -1)` є обов'язковим для повного зчитування даних.
>     
> - **Метод flip()**: Без виклику `flip()` перед записом/читанням з буфера, операція не побачить записаних даних, оскільки `position` буде вказувати на кінець буфера, а не на його початок.
>     

---

> [!tip] Переваги підходу
> 
> - **Контроль пам'яті**: Ви самі визначаєте розмір буфера (`allocate`), що дозволяє обробляти гігабайтні файли, використовуючи лише кілька кілобайт RAM.
>     
> - **Гнучкість**: Канали дозволяють переходити до будь-якої позиції у файлі (позиціонування), що неможливо зі звичайними потоками `InputStream`.
>     

---

# Connections: