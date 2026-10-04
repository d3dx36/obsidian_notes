# План изучения Kotlin

> **Старт:** 0 знаний · **Цель:** Android-разработка
> **Формат:** 5 дней в неделю по 1–1.5 часа + 1 практическое задание в выходные

## Как пользоваться планом

1. Каждый этап = 3–7 дней. Не переходить дальше, пока не закрыт чек-лист «Умею» в конце этапа.
2. Правило **20/80**: 20% времени чтение, 80% — набор кода. Kotlin не учится чтением.
3. Любая тема проверяется только кодом, который запускается. Прочитал — сразу пиши мини-пример.
4. Ошибки компилятора — не баг, а тренажёр. Читай их целиком, а не последнюю строку.
5. Один большой проект в конце важнее десяти разрозненных задач.

**Где писать код:** [Kotlin Playground](https://kotlinlang.org/play?version=stable&mode=online) — для синтаксиса; IntelliJ IDEA / Android Studio — для остального.

---

## Этап 0. Среда и настройка (1 день)

- [x] Установить IntelliJ IDEA (Community) или Android Studio
- [x] Создать Gradle-проект (Kotlin/JVM), запустить `main`, убедиться что `Hello, world` печатается
- [ ] Настроить автоформат по [Kotlin Coding Conventions](https://kotlinlang.org/docs/coding-conventions.html) (в IDEA уже из коробки)
- [ ] Завести репозиторий, завести папку `exercises/`, каждая тема — отдельный файл `TopicName.kt`

**Ресурс:** [Get Started with Kotlin](https://kotlinlang.org/docs/home.html)

---

## Этап 1. Базовый синтаксис (4–5 дней)

**Темы**
- Переменные: `val` / `var`, `const val`, типизация и вывод типа
- Типы: `Int`, `Long`, `Double`, `Float`, `Boolean`, `Char`, `String`, `Unit`, `Any`
- Примитивы vs обёртки, `Long` vs `Int` (главный источник багов в Android)
- Строки: шаблонные строки `"$name"`, raw-строки `"""`, интерполяция с выражениями
- `if` как выражение, `when` (в т.ч. `when` без условия и `when(val)` для smart cast)
- Циклы: `for`, `while`, `do-while`, `..` (range), `step`, `downTo`, `repeat`
- Функции: параметры по умолчанию, именованные аргументы, единичные выражения
- **Важно:** точка с запятой не нужна. Именованные аргументы — обязательны, а не опциональны

**Умею**
- Написать функцию, которая принимает именованные параметры со значениями по умолчанию
- Преобразовать `if`-цепочку в `when`
- Написать `when`-выражение, возвращающее значение, и использовать его в `return`

**Практика:** калькулятор дохода/расходов по месяцам; валидатор пароля (длина, цифры, спецсимволы)

---

## Этап 2. Null safety (2–3 дня)

**Темы**
- Тип `null`, `Any?`
- `?.` безопасный вызов, `?.let {}`
- Elvis-оператор `?:`
- `!!` — когда допустимо, почему это плохо в проде
- `lateinit` / `lazy`
- `when (val)` для smart cast
- Проверки с `if (x != null)` → smart cast
- Kotlin-типы и Java: platform types, почему `?.` не всегда спасает

**Умею**
- Избавиться от всех `!!` в своём коде
- Объяснить, почему `lateinit var` нельзя для `Int`/`String` без nullable-обёртки

**Практика:** загрузка профиля пользователя, где часть полей может отсутствовать → функция `formatUser(): String` без единого `!!`

---

## Этап 3. Классы и объекты (5–7 дней)

**Темы**
- Класс, конструктор (primary / secondary), `init`-блок, `private constructor`
- Свойства: `val` / `var`, `get()` / `set()`, backing field `_x`
- Видимость: `public` (по умолчанию), `private`, `protected`, `internal`
- Модификаторы: `open`, `final`, `abstract`, `override`, `data`, `sealed`, `inner`, `companion object`, `enum class`, `value class`
- Наследование, абстракция, интерфейсы (в Kotlin всё есть `final` — неожиданно)
- `object` (синглтон) vs `companion object`
- `equals`/`hashCode`/`toString` у `data`-класса, `copy()`, `componentN`
- **Анонимные объекты** vs **объектные выражения**

**Умею**
- Написать `data class` и понять, зачем нужен `copy()`
- Объяснить, чем `sealed class` лучше `enum class`, когда у состояния есть разные наборы полей
- Понимать разницу `object Foo` и `companion object`

**Практика:** модель задачи (todo) со статусами через `sealed class`: `data class Active(...)`, `data class Done(val completedAt: Long)`, `object Archived`

---

## Этап 4. Функции первого класса и лямбды (3–4 дня)

**Темы**
- Функции как значения, передача/возврат функций
- Лямбды, анонимные функции, `fun interface` (SAM)
- Trailing lambda
- Функции высшего порядка (приём функции, возврат функции)
- Non-local `return` и inline-функции (`crossinline`, `noinline`) — на уровне «понимаю зачем»

**Умею**
- Написать свою функцию с параметром-функцией
- Реализовать `withLock`-подобный `with` вручную и понять, чем отличается от `kotlin.io`

**Практика:** свой `repeat(times) { }`, свой `timed(label) { }` для замеров

---

## Этап 5. Коллекции (4–5 дней)

**Темы**
- `List`, `Set`, `Map` — интерфейсы и иммутабебельность по умолчанию
- `mutableListOf` и т.д. — когда можно, а когда нет
- `Iterable` vs `Collection` vs `Sequence` (ленивость!)
- Функциональные операции: `map`, `filter`, `fold`, `reduce`, `sumOf`, `count`, `any`, `all`, `none`, `first`/`firstOrNull`, `last`, `sortedBy`, `groupBy`, `associate`, `distinct`, `take`/`drop`, `chunked`, `zip`, `windowed`
- `joinToString`, `partition`, `flatMap`
- `List<T>` vs `List<out T>`, `Map<K, V>` — базовое понимание
- `Collection<T>` из Java vs Kotlin `MutableList` — конфликт имён

**Умею**
- Сцепить 3+ операции в читаемую цепочку
- В упоре на вложенный `for` найти цепочку из `map`/`filter`/`associate`
- Объяснить, почему для больших данных `Sequence` лучше

**Практика:** анализ списка заказов: средний чек, топ-3 товара, разбивка по месяцам, дедупликация

---

## Этап 6. Extension-функции и scope-функции (4–5 дней)

> Это самый «языковой» блок Kotlin, в нём чаще всего ошибаются на собесах

**Темы**
- Extension-функции, extension-свойства, общие расширения, `inline` для расширений-«скоростей»
- `let`, `run`, `apply`, `also`, `with` — **таблица выбора**, не заучивать наизусть, а понимать 4 вещи: кто receiver, что возвращает, доступен ли `it`/это, лямбда vs receiver
- `takeIf` / `takeUnless`
- `also` vs `apply`
- Destructuring declaration в `componentN`

**Шпаргалка, которую надо заполнить самому:**

| Функция | Receiver | Возвращает | Lambda | Когда |
|---|---|---|---|---|
| `let` | it | результат лямбды | (T) -> R | трансформация, null-обёртка |
| `run` | this | результат лямбды | T.() -> R | трансформация объекта |
| `apply` | this | **сам this** | T.() -> Unit | конструирование/настройка |
| `also` | it | **сам this** | (T) -> Unit | побочное действие |
| `with` | this | результат лямбды | T.() -> R | несколько вызовов на объекте |

**Умею**
- По коду восстановить, какая scope-функция нужна, за 2 секунды
- Написать builder: `buildUser { name = "..." }`
- Пояснить разницу `apply` и `also` на примере логирования

**Практика:** DSL-строитель `html { head { title("...") } }` на 30 строк

---

## Этап 7. Generics и variance (3–4 дня)

**Темы**
- Generic-типы и функции, `fun <T> first(list: List<T>): T`
- `in` (contravariant), `out` (covariant), `*` (star projection)
- `T : Any`, `T : Comparable<T>`, `where`
- Reified + `inline` (нужно, чтобы понять `json`/сериализацию)
- Обобщённые `object` / `companion object`

**Умею**
- Объяснить, почему `List<out T>` нельзя добавить, а `MutableList` — можно
- Написать generic-функцию с ограничением `reified` и `is T`

**Практика:** свой `TypeUtils.kt` с `isType<T>()` на reified

---

## Этап 8. Делегаты, операторы, аннотации (2–3 дня)

**Темы**
- Delegation: `by lazy`, `by Delegates.observable`, `by Delegates.notNull`
- `operator fun` и `componentN`, `getValue`/`setValue`
- Destructuring в `data class` / для Map.Entry
- Аннотации: `@JvmStatic`, `@JvmOverloads`, `@Volatile`, `@Deprecated`
- Инлайн-классы `value class`, `@JvmInline`
- `typealias`

**Умею**
- Реализовать `getValue`/`setValue` и понять, зачем это нужно
- Объяснить, когда `value class` полезен (обёртка примитива не аллоцируется)

---

## Этап 9. Корутины — база (5–7 дней)

> Для Android это второй по важности блок после синтаксиса

**Темы**
- Что такое корутина, `suspend`, точка приостановки
- `CoroutineScope`, `launch`, `async` (+ `Deferred`), `runBlocking`
- `Job`, `isActive`, `cancel()`, `CancellationException` — **отмена как норма**
- `CoroutineContext`, `Dispatchers` (Main / IO / Default / Unconfined)
- Структурированная конкурентность: дети умирают вместе с родителем
- `try/finally` + `coroutineScope {}` / `supervisorScope {}`
- `withContext`, `withTimeout`
- `NonCancellable` — когда нужен `finally` с suspend-вызовом

**Умею**
- Запустить корутину в нужном диспетчере и правильно отменить
- Поймать отмену и не «проглотить» `CancellationException`
- Написать `suspend fun` и вызвать её из UI без `!!` и без ANR

**Практика:** загрузка данных из 3 источников параллельно через `async`, отмена при закрытии экрана

---

## Этап 10. Flow и реактивность (4–5 дней)

**Темы**
- `Flow`, cold vs hot
- `collect`, `map`, `filter`, `transform`, `onEach`, `catch`, `retry`
- `stateIn` / `shareIn` / `SharingStarted`
- `callbackFlow`
- Связка с `collectAsState()` / `collectAsStateWithLifecycle()` в Compose

**Умею**
- Построить поток событий/состояния, который не переподписывается
- Объяснить, зачем нужен `SharingStarted.WhileSubscribed`

**Практика:** поиск с debounce + диспатчеринг результатов в StateFlow

---

## Этап 11. Kotlin для Android (5–7 дней)

**Темы**
- `data class` как UI-модель, `Parcelize` (`@Parcelize` + kotlin-parcelize)
- Extension-функции на `Context`, `View`, `Fragment` (учиться читать, а не писать «на всё»)
- sealed-иерархия для навигации/результатов
- `SavedStateHandle` (Compose `rememberSaveable` — как это работает)
- Интероп с Java: `@JvmStatic`, `@JvmField`, `@JvmOverloads`, `Platform class`
- Производительность: `inline`/`noinline`, `crossinline`, аллокации в `compose lambda`
- Тесты: как тестировать корутины (`kotlinx-coroutines-test`, `runTest`)

**Умею**
- Написать sealed-результат навигации и обработать в UI
- Не ловить `ClassCastException` из-за platform types

**Практика:** маленький экран-поиск: `StateFlow` во ViewModel + Compose + сохранение состояния при повороте

---

## Этап 12. Итоговый проект (3–4 недели)

Один проект, который проходит через всё сразу:

**Вариант A — «Трекер привычек»**
- Compose UI
- Room для хранения, Flow для реактивности
- ViewModel + `viewModelScope` + `StateFlow`
- Sealed-классы для навигации и результатов
- DI: ручной или Hilt
- Тесты: unit (логика) + 1–2 UI-теста

**Вариант B — «Погодное приложение»**
- Retrofit/OkHttp + `suspend`, обработка ошибок через sealed `Result`
- Кэш в Room, `NetworkBoundResource`-паттерн
- WorkManager для фонового обновления

**Критерии готовности**
- [ ] Собирается в release, без warnings
- [ ] Нет `!!` и `GlobalScope`
- [ ] Экраны не блокируют поток
- [ ] Есть unit-тесты на бизнес-логику
- [ ] Можно объяснить каждый файл по архитектуре

---

## Порядок тем — если времени мало

Приоритет, если придётся сокращать:

1. **Обязательно:** этапы 1, 2, 3, 5, 6, 9, 10
2. **Важно:** этапы 4, 7, 11
3. **Может подождать:** этап 8 (кроме `by lazy`), этап 12 — до первого собеседования

---

## Что НЕ учить пока

`[Что не учить](../Что не учить.md)` — не тратить время на:

- Рефлексию, `kotlin-reflect`
- Метапрограммирование, `KSP`/`KAPT` (просто знать, что это)
- `Channels`, `select`, `actors`
- Многопоточность уровня `synchronized` / `ReentrantLock` внутри корутин
- Собственный компилятор, IR, `Compiler plugin`
- `Compose` до этапа 9 (без корутин Compose не понять)

---

## Ресурсы

**Официальное (главное, по порядку):**
- [Kotlin docs — Basic syntax](https://kotlinlang.org/docs/basic-syntax.html)
- [Kotlin docs — Scope functions](https://kotlinlang.org/docs/scope-functions.html)
- [Kotlin docs — Coroutines guide](https://kotlinlang.org/docs/coroutines-guide.html)
- [Kotlin docs — Coroutines Android guide](https://kotlinlang.org/docs/coroutines-guide.html)
- [Coroutines guide от Android](https://developer.android.com/kotlin/coroutines)
- [Android Basics with Compose](https://developer.android.com/courses/android-basics-compose)

**Практика:**
- [Kotlin Playground](https://kotlinlang.org/play) — для бесшовных задач
- [Exercism: Kotlin](https://exercism.org/tracks/kotlin) — 60+ задач, с проверкой
- [JetBrains Kotlin Koans](https://github.com/Kotlin/Kotlin-Koans) — пробелы в знаниях

**Книги (по одной, не все сразу):**
- «Atomic Kotlin» — А. Климов, если новичок
- «Kotlin in Action» — для глубины, после этапа 7

---

## Практика каждый день (шаблон)

```
1. 10 мин — повторить вчерашнее без подсмотра
2. 20 мин — новая теория этапа (1–2 подтемы, не весь этап)
3. 40 мин — код: 3–5 задач на выбранную подтему
4. 10 мин — записать в заметку 3 вещи, которые понял, и 1, где плаваю
```

## Чек-лист прогресса

- [ ] Этап 0 — среда
- [ ] Этап 1 — синтаксис
- [ ] Этап 2 — null safety
- [ ] Этап 3 — классы
- [ ] Этап 4 — лямбды
- [ ] Этап 5 — коллекции
- [ ] Этап 6 — extension + scope
- [ ] Этап 7 — generics
- [ ] Этап 8 — делегаты и операторы
- [ ] Этап 9 — корутины
- [ ] Этап 10 — Flow
- [ ] Этап 11 — Kotlin для Android
- [ ] Этап 12 — итоговый проект
