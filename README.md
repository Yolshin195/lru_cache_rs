# 🚀 LRU Cache + Memoization in Rust

LRU cache implementation in Rust with O(1) get/put operations, plus a memoization layer for caching function results. Includes optional attribute macro for Python-like usage. Focuses on ownership, borrow checker, and efficient data structures.

## 📌 Overview

Реализовать **LRU (Least Recently Used) cache** с возможностью использования его для **кеширования результатов функций (memoization)**.

Цель проекта — продемонстрировать:

* понимание структур данных
* работу с ownership / borrowing
* проектирование API в Rust
* (опционально) навыки metaprogramming через proc macros

---

## 🧠 Part 1 — LRU Cache

### 🎯 Требуется реализовать структуру:

```rust
pub struct LruCache<K, V> {
    // implementation details
}
```

---

### 📦 API

```rust
impl<K, V> LruCache<K, V>
where
    K: Eq + std::hash::Hash,
{
    pub fn new(capacity: usize) -> Self;

    pub fn get(&mut self, key: &K) -> Option<&V>;

    pub fn put(&mut self, key: K, value: V);

    pub fn len(&self) -> usize;
}
```

---

### ⚙️ Requirements

* Все операции (`get`, `put`) должны работать за **O(1)**
* При превышении capacity:

  * удаляется **наименее недавно использованный элемент (LRU)**
* Вызов `get`:

  * возвращает значение
  * обновляет порядок использования (элемент становится “самым свежим”)

---

### ⚠️ Constraints

* ❌ Не использовать готовые реализации LRU cache
* ❌ Избегать `unsafe`
* ❌ Минимизировать лишние `clone`
* ✅ Разрешено использовать стандартную библиотеку

---

### 💡 Hints

* Используйте комбинацию:

  * `HashMap`
  * структура для хранения порядка (например, двусвязный список или аналог)
* В Rust вместо ссылок можно использовать:

  * индексы
  * `Rc<RefCell<T>>` (если необходимо)

---

## 🧪 Part 2 — Memoization

### 🎯 Реализовать обёртку:

```rust
pub fn memoize<F, K, V>(f: F, capacity: usize) -> impl FnMut(K) -> V
where
    F: Fn(K) -> V,
    K: Eq + std::hash::Hash + Clone,
    V: Clone;
```

---

### 📌 Поведение

```rust
let mut cached_fn = memoize(|x: u64| x * 2, 100);

cached_fn(10); // вычисляет
cached_fn(10); // возвращает из кеша
```

---

### ⚙️ Requirements

* Использует `LruCache` внутри
* Кеширует результат по входным аргументам
* При переполнении:

  * удаляет LRU элементы

---

## 🔥 Part 3 — Attribute Macro (Advanced)

### 🎯 Реализовать proc macro:

```rust
#[lru_cache(capacity = 100)]
fn fib(n: u64) -> u64 {
    match n {
        0 | 1 => n,
        _ => fib(n - 1) + fib(n - 2),
    }
}
```

---

### 📌 Поведение

* Автоматически добавляет кеширование
* Кеш хранится внутри функции (static / lazy init)

---

### ⚠️ Constraints (для упрощения)

* Поддерживать только:

  * функции без generics
  * аргументы, реализующие `Hash + Eq + Clone`
  * возвращаемое значение: `Clone`

---

### 💡 Hints

* Использовать:

  * `proc_macro`
  * `syn`
  * `quote`
* Генерировать обёртку функции с кешем

---

## 🧪 Testing

Обязательно покрыть тестами:

* добавление элементов
* переполнение кеша
* корректное удаление LRU
* повторные вызовы (memoization)

---

## 🚀 Bonus (Optional)

### ⭐ Level 2

* Добавить TTL (время жизни элементов)

### ⭐ Level 3

* Сделать потокобезопасную версию (`Mutex` / `RwLock`)

### ⭐ Level 4

* Поддержка async функций

---

## 🎯 Expected Outcome

После выполнения задания:

* понимание LRU cache
* уверенная работа с borrow checker
* опыт проектирования API
* (опционально) понимание proc macros

---

## 📚 Suggested Steps

1. Реализовать базовый `LruCache`
2. Добавить тесты
3. Реализовать `memoize`
4. (опционально) добавить macro

---

## 💬 Notes

Это задание намеренно содержит нетривиальные аспекты:

* конфликт mutable / immutable borrow
* сложность self-referential структур
* необходимость правильного владения данными

Основная цель — научиться **думать в модели Rust**, а не только писать код.
