% Дженерики (обобщённые типы)

Иногда, при написании функции или типа данных, мы можем захотеть, чтобы они
работали для нескольких типов аргументов. К счастью, у Rust есть возможность,
которая дает нам лучший способ реализовать это: дженерики (обобщённые типы).
Дженериками называется «параметрический полиморфизм» в теории типов. Это
означает, что они являются типами или функциями, которые имеют несколько форм
(poly — кратно, morph — форма) по данному параметру («параметрический»).

В любом случае, хватит о теории типов, давайте рассмотрим какой-нибудь дженерик
код. Стандартная библиотека Rust предоставляет тип `Option<T>`, который является
дженерик типом:

```rust
enum Option<T> {
    Some(T),
    None,
}
```

Часть `<T>`, которую вы уже видели несколько раз прежде, указывает, что это
обобщённый тип данных. Внутри перечисления, везде, где мы видим `T`, мы
подставляем вместо этого абстрактного типа тот, который используется в дженерике.
Вот пример использования `Option<T>` с некоторыми дополнительными аннотациями
типов:

```rust
let x: Option<i32> = Some(5);
```

В определении типа мы используем `Option<i32>`. Обратите внимание, что это очень
похоже на `Option<T>`. С той лишь разницей, что, в данном конкретном `Option`,
`T` имеет значение `i32`. В правой стороне выражения, мы используем `Some(T)`,
где `T` равно `5`. Так как `5` является представителем типа `i32`, то типы по
обе стороны совпадают, отчего Rust счастлив. Если же они не совпадают, то мы
получим ошибку:

```rust,ignore
let x: Option<f64> = Some(5);
// error: mismatched types: expected `core::option::Option<f64>`,
// found `core::option::Option<_>` (expected f64 but found integral variable)
```

Но это не значит, что мы не можем сделать `Option<T>`, который содержит `f64`!
Просто с левой и с правой сторон выражения объявления переменной типы должны
совпадать:

```rust
let x: Option<i32> = Some(5);
let y: Option<f64> = Some(5.0f64);
```

Это просто прекрасно. Одно определение — многостороннее использование.

Дженерики могут быть обобщёнными не только для одного единственного типа.
Рассмотрим другой подобный тип из стандартной библиотеки Rust — `Result<T, E>`:

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

Этот тип является обобщённым сразу для _двух_ типов: `T` и `E`. Кстати,
заглавные буквы может быть любыми, какими хотите. Мы могли бы определить
`Result<T, E>` как:

```rust
enum Result<A, Z> {
    Ok(A),
    Err(Z),
}
```

если бы захотели. Соглашение гласит, что первый обобщённый параметр для 'типа'
должен быть `T`, и что для 'ошибки' используется `E`. Но Rust не проверяет
этого.

Тип `Result<T, E>` предназначен для того, чтобы возвращать результат вычисления,
и имеет возможность вернуть ошибку, если произойдет какой-либо сбой.

## Дженерик функции

Мы можем задавать функции, которые принимают дженерик типы с помощью
аналогичного синтаксиса:

```rust
fn takes_anything<T>(x: T) {
    // do something with x
}
```

Синтаксис состоит из двух частей: `<T>` говорит о том, что “эта функция является
обобщённой по одному типу, `T`”, а `x: T` говорит о том, что “х имеет тип `T`”.

Несколько аргументов могут иметь один и тот же дженерик тип:

```rust
fn takes_two_of_the_same_things<T>(x: T, y: T) {
    // ...
}
```

Мы можем написать версию, которая принимает несколько типов:

```rust
fn takes_two_things<T, U>(x: T, y: U) {
    // ...
}
```

Обобщённые функции наиболее полезны в связке с «ограничениями по типажам», о
которых мы расскажем в главе [Типажи][traits].

[traits]: traits.html

## Дженерик структуры

Вы также можете задать дженерик тип для `struct`:

```rust
struct Point<T> {
    x: T,
    y: T,
}

let int_origin = Point { x: 0, y: 0 };
let float_origin = Point { x: 0.0, y: 0.0 };
```

Аналогично функциям, мы также объявляем дженерик параметры в `<T>`, а затем
используем их в объявлении типа `x: T`.