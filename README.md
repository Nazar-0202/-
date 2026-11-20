# Вариант 31. Туристическое агентство

Консольное приложение на C# (.NET 8), демонстрирующее три паттерна проектирования: **Builder**, **Decorator** и **Observer**.

## 🎯 Задача
1. Сборка тура из частей с проверкой полноты (нельзя без страны и цены).
2. Добавление опций (страховка, трансфер, экскурсии) в любом сочетании с пересчётом цены.
3. Подписка клиентов на изменение цен и отписка.

## 🏗️ Паттерны

### 1. Builder (Строитель) — `TourBuilder`
**Проблема:** тур — сложный объект с множеством необязательных частей. Телескопический конструктор с 8+ параметрами нечитаем.  
**Решение:** пошаговая сборка через fluent-интерфейс (`SetName().SetCountry()...Build()`). Метод `Build()` проверяет обязательные поля и бросает `InvalidOperationException`, если тур неполный.  
**Результат:** `Tour` — неизменяемый (immutable) объект.

### 2. Decorator (Декоратор) — `TourOptionDecorator` и опции
**Проблема:** 3 опции дают 2³ = 8 возможных комбинаций. При наследовании потребовалось бы 8 классов.  
**Решение:** единый интерфейс `ITourPackage`, базовый `BaseTourPackage` и обёртки `InsuranceOption`, `TransferOption`, `ExcursionOption`. Каждая обёртка делегирует `Inner` и добавляет свою стоимость/описание.  
**Результат:** любое сочетание опций во время выполнения, новая опция — новый класс без правки существующего кода (OCP).

### 3. Observer (Наблюдатель) — `TourCatalog` + `ITourObserver`
**Проблема:** клиенты следят за ценами. Без паттерна каталог должен был бы знать конкретные классы клиентов.  
**Решение:** каталог хранит список `ITourObserver`. При `ChangePrice()` рассылает уведомления всем подписчикам. Обход по копии (`ToArray()`) защищает от падения при отписке во время рассылки.  
**Результат:** слабая связанность — каталог не знает о `Client`.

## 📊 Диаграмма классов
![Диаграмма классов](https://www.plantuml.com/plantuml/png/hLNTIXj15BxVfnZqPajTqKgXX3Pe4ai3RGrehyjaznW7DpDXPjRMMa7jHU-BJz0d28fG-kCpJ5wXJzAPSPEUpIQhe1F8nPpl_7pdEtDsNMagTTPF0xNF-81AsYTnIfNQq5goRgQX7Gk-pJu1MK6GBettUr9aF6cAL4Yo-7BJ_QQ3D4emVqem8HEGEMJDdo3GJAT0p3Sp79sO7sPe_epEoDhgCZ7VHr_SrUXqT6R_lvghqQdvQQxD5R6eItFbaDvuOQx9tvDpOivzW4lhDJI_xMz8hCClCxIeK-ju4GG3Modj0Lbu8n98rG8v2eWzlZoo8pBfBlBpcBobVQWHfINZFNJTj4orF9nXQT53LIECQtJNe0ewaiKsKW8nwzCK6Ly93UcCG9iVuqmg9hWDzvef_Vm6KKUG7R7jxy971V_Zu1ZHx52jGNBb-zF8M6e5A9FE3OXxHDvpJ1xTnqNss9IKwDlBGP4xiknXyjW0jxFV1ktK2LsLjW68HH6KQnNcLLT1dNYXer05yeA5ll8gc1SyzDJdmZQIP29pw9x1VAY_2NFpRJAt81QIQZ4Hcd5htAKnaBRpx-HUUD9Re6B91jgMCcCQo-DhywCPSYDUZ8p4rDQ02hzVFjgrO1fhDBnRFig4D4g-IDgSWqsEQQCqH-DenzNrOE1jhMXpbKdAOxWfgz2A
