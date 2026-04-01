> **NIO2** (запроваджений у Java 1.7) — це сучасний, високорівневий API для роботи з файловою системою, що входить до пакету `java.nio.file`. Він значно спрощує маніпуляції з файлами порівняно з класичним IO та першою версією NIO, оскільки автоматизує роботу з буферами та кодуванням.

---

> [!abstract] Core Framework NIO2 базується на трьох основних компонентах:
> 
> - **Interface Path:** Програмне представлення шляху до файлу або директорії (заміна застарілому `java.io.File`).
>     
> - **Class Files:** Утилітарний клас, що містить статичні методи для копіювання, створення, видалення та читання/запису файлів.
>     
> - **Class Paths / Path.of():** Фабричні методи для перетворення рядкових шляхів (`String`) в об'єкти `Path`.
>     

---

> [!info] Key Insights
> 
> - **Files API та Path API:** Методи класу `Files` завжди приймають об'єкти `Path` як параметри.
>     
> - **Автоматизація:** Методи на кшталт `Files.readString()` або `Files.writeString()` самостійно керують відкриттям каналів, створенням буферів та декодуванням символів.
>     
> - **Безпека:** Більшість операцій викидають `IOException`, а специфічні випадки (наприклад, спроба створити вже існуючий файл) обробляються через `FileAlreadyExistsException`.
>     

---

> [!example] Практичне застосування
> 
> **1. Створення нової директорії**
> 
> Java
> 
> ```java
> import java.io.IOException;
> import java.nio.file.FileAlreadyExistsException;
> import java.nio.file.Files;
> import java.nio.file.Path;
> import java.nio.file.Paths;
> 
> public class Main {
>     private static final String BASE_PATH = "/Username/files/";
> 
>     public static void main(String[] args) {
>         String newDirName = "docs";
>         String newPath = BASE_PATH + newDirName;
>         getOutput(makeDir(newPath));
>     }
> 
>     private static String makeDir(String newPath) {
>         /* Перетворюємо String значення у Path значення */
>         Path path = Paths.get(newPath);
>         Path newDir;
>         try {
>             /* Метод Files.createDirectory() створює нову директорію */
>             newDir = Files.createDirectory(path);
>         } catch (FileAlreadyExistsException e) {
>             return "Directory already exists!";
>         } catch (IOException e) {
>             return "Something wrong " + e.getMessage();
>         }
>         return newDir + " created!";
>     }
> 
>     private static void getOutput(String output) {
>         System.out.println(output);
>     }
> }
> ```
> 
> ---
> 
> **2. Створення файлу та запис даних**
> 
> Java
> 
> ```java
> import java.io.IOException;
> import java.nio.file.FileAlreadyExistsException;
> import java.nio.file.Files;
> import java.nio.file.Path;
> import java.nio.file.Paths;
> 
> public class Main {
>     private static final String BASE_PATH = "/Username/files/";
> 
>     public static void main(String[] args) {
>         String newFileName = "myfile.txt";
>         String content = "Some information.";
>         String strPath = BASE_PATH + newFileName;
>         
>         getOutput(createFile(strPath));
>         getOutput(writeToFile(Paths.get(strPath), content));
>     }
> 
>     private static String createFile(String strPath) {
>         Path newFile;
>         try {
>             /* Створення нового порожнього файлу */
>             newFile = Files.createFile(Path.of(strPath));
>         } catch (FileAlreadyExistsException e) {
>             return "File already exists!";
>         } catch (IOException e) {
>             return "Something wrong " + e.getMessage();
>         }
>         return newFile + " created!";
>     }
> 
>     private static String writeToFile(Path path, String content) {
>         try {
>             /* Запис рядка безпосередньо у файл */
>             Files.writeString(path, content);
>         } catch (IOException e) {
>             return e.getMessage();
>         }
>         return "Recorded in: " + path;
>     }
> 
>     private static void getOutput(String output) {
>         System.out.println(output);
>     }
> }
> ```
> 
> ---
> 
> **3. Читання та видалення**
> 
> Java
> 
> ```java
> /* Метод читання: зчитує весь вміст у рядок */
> private static String readFromFile(String strPath) {
>     try {
>         return Files.readString(Path.of(strPath));
>     } catch (IOException e) {
>         return "Something wrong " + e.getMessage();
>     }
> }
> 
> /* Метод видалення файлу */
> private static String deleteFile(String strPath) {
>     try {
>         Files.delete(Path.of(strPath));
>         return "File has deleted.";
>     } catch (IOException e) {
>         return "Something wrong " + e.getMessage();
>     }
> }
> ```

---

> [!warning] Обмеження та ризики
> 
> - **Пам'ять:** Методи `Files.readString()` та `Files.writeString()` завантажують весь вміст файлу в оперативну пам'ять. Це зручно для малих файлів, але може призвести до `OutOfMemoryError` при роботі з величезними даними (кілька ГБ).
>     
> - **Синхронність:** Ці методи є блокуючими. Для асинхронних операцій у NIO2 використовуються інші класи (наприклад, `AsynchronousFileChannel`).
>     
> - **Видалення директорій:** Метод `Files.delete()` видалить директорію лише якщо вона **порожня**. Для рекурсивного видалення потрібні додаткові алгоритми обходу дерева файлів.
>     

---

> [!tip] Переваги NIO2
> 
> - **Лаконічність:** Запис файлу займає один рядок коду замість десятка в IO/NIO.
>     
> - **Типізація:** Об'єкт `Path` дозволяє легко маніпулювати частинами шляху (отримати ім'я файлу, батьківську папку тощо).
>     
> - **Кросплатформеність:** NIO2 коректно обробляє різницю в роздільниках шляхів (`/` vs `\`) між OS.
>     

---

# Connections:
- [[Java NIO API]]
- [[Робота з файлами через Java NIO (Буфери та Канали)]]
- [[Практика роботи з FileChannel та ByteBuffer у Java NIO]]
- [[Відмінності між Java IO, NIO та NIO.2]]