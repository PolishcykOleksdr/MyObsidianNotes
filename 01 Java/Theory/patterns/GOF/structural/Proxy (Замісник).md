> **Замісник (Proxy)** — це **структурний патерн**, який надає **об'єкт-замісник**, що контролює доступ до реального об'єкта, дозволяючи виконувати додаткові дії до або після звернення до нього.

---

> [!abstract] Core Framework
>  **Proxy стоїть між клієнтом і реальним об'єктом, реалізуючи той самий інтерфейс:**
> 
> - **Subject** — спільний інтерфейс для реального об'єкта і заміщувача (`Downloader`);
> - **RealSubject** — реальний об'єкт із основною логікою (`RealDownloader`);
> - **Proxy** — замісник, що тримає посилання на `RealSubject` і контролює доступ до нього (`ProxyDownloader`).

---

> [!info] Key Insights
> 
> - **Ледаче створення (Lazy Initialization).** Реальний об'єкт (`RealDownloader`) не створюється до першого реального запиту — Proxy відкладає дороге створення до моменту необхідності.
>     
> - **Кешування.** Після першого завантаження Proxy зберігає посилання на `RealDownloader` і повторно не виконує завантаження при наступних викликах.
>     
> - **Прозорість для клієнта.** Клієнт взаємодіє з Proxy через той самий інтерфейс `Downloader` — без будь-яких змін у клієнтському коді.
>     

---

> [!example] Практичне застосування
> 
> **Структура патерну:**
> 
> - `Downloader` — інтерфейс із методом `download()`;
> - `RealDownloader` — реальна реалізація, що одразу завантажує файл у конструкторі;
> - `ProxyDownloader` — відкладає створення `RealDownloader` до першого виклику `download()` і кешує результат.

```java
// Інтерфейс для завантаження файлу
interface Downloader {
    void download();
}

// Реальний об'єкт, що виконує завантаження
class RealDownloader implements Downloader {
    private String url;

    public RealDownloader(String url) {
        this.url = url;
        download();
    }

    public void download() {
        System.out.println("Завантаження файлу з URL: " + url);
    }
}

// Проксі для об'єкта завантаження
class ProxyDownloader implements Downloader {
    private String url;
    private RealDownloader downloader;

    public ProxyDownloader(String url) {
        this.url = url;
    }

    public void download() {
        if (downloader == null) {
            downloader = new RealDownloader(url);
        }
        System.out.println("Кешоване завантаження файлу з URL: " + url);
    }
}

public class Main {
    public static void main(String[] args) {
        // Завантажуємо файл за допомогою реального об'єкта
        Downloader realDownloader = new RealDownloader("https://example.com/file.txt");
        realDownloader.download();

        // Завантажуємо файл за допомогою проксі
        Downloader proxyDownloader = new ProxyDownloader("https://example.com/file.txt");
        proxyDownloader.download();
    }
}
```

---

> [!warning] Обмеження та ризики
> 
> - **Затримка відповіді.** Додатковий рівень непрямості (перевірки кешу, контролю доступу) вносить накладні витрати при кожному виклику.
>     
> - **Ускладнення коду.** Наявність двох класів з однаковим інтерфейсом може збентежити — важливо чітко документувати, де використовується Proxy, а де RealSubject.
>     

---

> [!tip] Коли використовувати
> 
> **Так** ✅:
> 
> - Потрібне ледаче створення дорогого об'єкта (Virtual Proxy)
> - Потрібен контроль доступу до об'єкта (Protection Proxy)
> - Потрібне кешування результатів або логування викликів (Caching / Logging Proxy)
> - Реальний об'єкт знаходиться на іншому сервері або в іншому адресному просторі (Remote Proxy)
> 
> **Ні** ❌:
> 
> - Реальний об'єкт легкий і не потребує контролю доступу
> - Додатковий рівень непрямості ускладнює систему без реальної користі

---
#structual #GOF #pattern
# Connections:
- [[Шаблони GOF (Gang of Four)]]