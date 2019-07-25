## Data Types Типы данных

Любая переменная в языке Rust обязательно имеет какой-либо тип. Это даёт возможность
корректной её обработки, а также проведение возможной оптимизации. В этой части
книги вы познакомитесь с встроенными в стандартную библиотеку типами данных,
которые не требуют перед своим использованием каких-либо дополнительных описаний.
Из предыдущей секции вы узнали, что типы данных присваиваются перемененным при их
инициализации. В этой секции вы узнаете о типах данных подробнее.

Важной особенностью языка Rust является *статическая типизация*. Благодаря этому
все типы переменных известны при компиляции кода. Конкретный тип,
если это не указано заранее, компилятор выбирает сам на основании доступных данных
(на основе конкретного значения и способа его использования). Для устранения
неточности, используется явное указание типа данных:

Также, особенностью объявления литерала является возможность аннотирования. Это
позволяет в краткой форме описать тип данных Например: `5i32`, `1u32`.

```rust
fn main() {
  let guess: u32 = "42".parse().expect("Not a number!");
  println!("guess = {}",guess);
}
```

Пожалуйста, удалите или закомментируйте информацию о типе переменной и, попробовав
скомпилировать код, получите ошибку:

```rust
fn main() {
  let guess
  //: u32
  = "42".parse().expect("Not a number!");
  println!("guess = {}",guess);
}
```


```text
error[E0282]: unable to infer enough type information about `_`
 --> src/main.rs:2:9
  |
2 |     let guess = "42".parse().expect("Not a number!");
  |         ^^^^^ cannot infer type for `_`
  |
  = note: type annotations or generic parameter binding required
```
Изучая материалы этой секции, вы познакомитесь с различными типами данных Rust.

### Скалярные типы данных

Скалярный тип данных (scalar data type) содержит одно значение и не имеет внутренних
компонентов. Скалярные типы данных делятся на четыре категории:
- Числовые.
- Символьные.
- Даты.
- Логические данные.

В состав скалярных типов *Rust* входят:
- целые числа,
- числа с плавающей запятой,
- логические,
- символьные.

Рассмотрим особенности каждого из них по порядку.

#### Целые числа

С одним из целых типов мы уже знакомы. Мы использовали его для уточнения при
конвертации строки в число. Обратите внимание на первый символ в типе `u32`!
Он указывает на то, что данный тип не может быть отрицательной величиной (unsigned).
Число, стоящее после буквы указывает на битовую разрядность этого числа.
Если заменить символ `u` на `i` в `u32`, то переменной данного типа можно будет
присваивать отрицательные целые числа. Проверим на нашем примере:


```rust
fn main() {
  let guess:
  //u32
  i32
  = "-42".parse().expect("Not a number!");
  println!("guess = {}",guess);
}
```
Обратите внимание, на знак конвертируемого числа `-42`! Попробуйте присвоить
это значение беззнаковому типу данных `u32`!

<span class="caption">Таблица 3-1: Rust. Целочисленные типы данных</span>

| Размер | Знаковый | Беззнаковый |
|--------|--------|----------|
| 8-bit  | i8     | u8       |
| 16-bit | i16    | u16      |
| 32-bit | i32    | u32      |
| 64-bit | i64    | u64      |
| 128-bit | i128    | u128      |
| arch   | isize  | usize    |

Пожалуйста, используйте их в нашей программе! Посмотрите на особенности их работы в
коде!

```rust
fn main() {
  let guess:
  //u8
  //u16
  //u32
  //u64
  i8
  //i16
  //i32
  //i64
  //isize
  //usize

  = "-42".parse().expect("Not a number!");
  println!("guess = {}",guess);
}
```

Давайте пристальнее посмотрим на эту сводную таблицу: по горизонтали длины, по
вертикали два варианта числового типа - знакового и беззнакового. Надеюсь, что
теперь ясно какие Rust имеет типы данных. А теперь очень интересный вопрос. Какие
ограничения у этих типов данных. Давайте напишем программу, которая будет выводить
доступные для использования числа в определённом типе данных. Для этого исследования
подойдут уже усвоенные нами знания об особенностях переменных, а также бесконечный цикл.

```rust
fn main() {
  let mut value:
  u8
  //u16
  //u32
  //u64
  //i8
  //i16
  //i32
  //i64
  //isize
  //usize

  = 0;
  println!("value = {}", value);
}
```
Запустите программу, измените код так, чтобы программа вывела бы вам все доступные
в указанном типе данных величины. Изучите работу со всеми целочисленными типами!
Благодаря созданным учебным программам Вы узнаете максимально допустимые величины
каждого целочисленного типа данных и, конечно, получите бесценный опыт и уверенность.

| Тип  | Знаковый минимум   | Знаковый максимум  |
|------|--------------------|--------------------|
| i8   | -128               | 127                |
| i16  | -32768             | 32767              |
| i32  |-2147483648         | 2147483647         |
| i64  |-9223372036854775808| 9223372036854775807|
| isize|-9223372036854775808| 9223372036854775807|

| Тип  | Беззнаковый минимум | Беззнаковый максимум |
|------|--------------|---------------------|
| u8   | 0            | 255                 |
| u16  | 0            | 65535               |
| u32  | 0            | 4294967295          |
| u64  | 0            | 18446744073709551615|
| usize| 0            | 18446744073709551615|


Один из возможных вариантов решения. Остановить длинный или бесконечный цикл, зависшее
консольное приложение можно комбинацией клавиш `Ctrl-C`:

```rust
fn main() {
    let max = <i8>::max_value();
    let mut value = <i8>::min_value();
    loop {
        value = value + 1;

        println!("value = {}", value);

        if value == max {
            break;
        }
    }
}

```

Каждый знаковый числовой тип хранит данные от -(2<sup>n - 1</sup>) до 2<sup>n -
1</sup> - 1 включительно, где n - это количество использованных битов данных.
Переменная типа данных `i8` может хранить значения от -(2<sup>7</sup>) до
2<sup>7</sup> - 1. Что эквивалентно следующему отрезку [-128, 127]. А беззнаковая
переменная такого же битового размера может хранить величины от 0 до 255.

Обратите внимание на типы данных `isize` и `usize`. Их битовая ёмкость зависит от
архитектуры операционной системы. Если система 32-битная - переменные могут хранить
32-битные величины, если 64-битная, то 64-битные соответственно.

Кроме ёмкости целочисленные переменные могут иметь различные обозначения.
Так одно и тоже число может быть записано в разных системах счисления. Её выбор
зависит от замысла программиста. Есть также возможность указать тип литерала непосредственно
при его написании (в виде суффикса числа). Правда есть ограничения (битовая система
счисления не имеет суффикса). Также для удобства представления есть возможность
использовать визуальный разделитель разрядов `_`.

<span class="caption">Таблица 3-2: Целые литералы в Rust</span>

| Числовые литералы | Пример       |
|------------------|---------------|
| Decimal          | `98_222`      |
| Hex              | `0xff`        |
| Octal            | `0o77`        |
| Binary           | `0b1111_0000` |
| Byte (`u8` only) | `b'A'`        |

```rust
fn main() {

  let value = 98_222_000;
  println!("value = {}", value);

  let value = 0xff;
  println!("value = {}", value);

  let value = 0o77;
  println!("value = {}", value);

  let value = 0b1111_0000;
  println!("value = {}", value);

  let value = b'A';
  println!("value = {}", value);

}
```

Если вы не уверены какой тип данных выбрать - используйте тип по умолчанию. Это
`i32`. Типы данных `isize` или `usize` используются при сортировке наборов данных.

Примеры использования различных видов написания целочисленных числовых данных:

```rust
fn main() {
    let value = 98_222_000;
    println!("value = {}", value);

    let mut value = 0xff;
    println!("value = {}", value);
    value = 0x_ff;
    println!("value = {}", value);

    let mut value = 0o77;
    println!("value = {}", value);
    value = 0o_77;
    println!("value = {}", value);

    let mut value = 0b1111_0000;
    println!("value = {}", value);
    value = 0b1_111_0000;
    println!("value = {}", value);

    let value = b'A';
    println!("value = {}", value);
}

```
Примеры использование разделителя `_`:

```rust
fn main() {
    let value = 98_222_000;
    println!("value = {}", value);

    let mut value = 0xff;
    println!("value = {}", value);
    value = 0x_ff;
    println!("value = {}", value);

    let mut value = 0o77;
    println!("value = {}", value);
    value = 0o_77;
    println!("value = {}", value);

    let mut value = 0b1111_0000;
    println!("value = {}", value);
    value = 0b1_111_0000;
    println!("value = {}", value);

    let value = b'A';
    println!("value = {}", value);
}

```

Обратите внимание на результаты работы программы, при использовании инициализации
по умолчанию данными значениями:

```rust
fn main() {
    //let value = 9223372036854775807;
    let value:i64 = 9223372036854775807;
    println!("value = {}", value);

    //let value = -9223372036854775808;
    let value:i64 = -9223372036854775808;
    println!("value = {}", value);

    //let value = 18446744073709551615;
    let value:u64 = 18446744073709551615;
    println!("value = {}", value);
}

```

Пожалуйста, проверьте вышеописанную информацию, используя теоретическую
информацию в ваших программных кодах.
Уверен, что вы столкнётесь с различными сюрпризами, которые помогут усвоению
пройденного материала и ускорят ваше становление в качестве знатока Rust. Пишите
код, пишите!

#### Числа с плавающей запятой

Кроме типов данных обозначающие целые числа Rust имеет два типа данных обозначающих
числа с плавающей запятой. Это 32- и 64-битные величины - `f32` и `f64`.
По умолчанию, т.е. при автоматическом определении типа используется `f64`.
Хотя скорость обработки данных не зависит от разрядности конкретного типа, всё же
на 32-разрядных системах для увеличения производительности рекомендуется использовать
`f32`.

Пример инициализации чисел с плавающей запятой:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    let x = 2.0; // f64

    let y: f32 = 3.0; // f32
}

```

Очень важно знать предельные значения типов данных. Стандартная библиотека Rust
предлагает подробную информацию по каждому типу данных.
Узнать максимальное и минимальное значения этих типов данных нам поможет "изящное"
решение:

```rust
fn main() {
    let value32_min = std::f32::MIN;
    println!("value f32 min = {}", value32_min);
    let value32_max = std::f32::MAX;
    println!("value f32 max = {}", value32_max);

    let value64_min = std::f64::MIN;
    println!("value f64 min = {}", value64_min);
    let value64_max = std::f64::MAX;
    println!("value f64 max = {}", value64_max);
}
```

Формат чисел с плавающей запятой соответствует стандарту IEEE-754. При этом `f32` -
это числа с единичной точностью, а `f64` с двойной точностью.

Пожалуйста, самостоятельно изучите состав констант модуля `std::f32`:

```rust

fn main() {
    println!("std::f32");
    // Approximate number of significant digits in base 10.
    println!("DIGITS = {}", std::f32::DIGITS);

    // Difference between 1.0 and the next largest representable number.
    println!("EPSILON = {}", std::f32::EPSILON);

    // Infinity (∞).
    println!("INFINITY = {}", std::f32::INFINITY);

    // Number of significant digits in base 2.
    println!("MANTISSA_DIGITS = {}", std::f32::MANTISSA_DIGITS);

    // Largest finite f32 value.
    println!("MAX = {}", std::f32::MAX);

    // Maximum possible power of 10 exponent.
    println!("MAX_10_EXP = {}", std::f32::MAX_10_EXP);

    // Maximum possible power of 2 exponent.
    println!("MAX_EXP = {}", std::f32::MAX_EXP);

    // Smallest finite f32 value.
    println!("MIN = {}", std::f32::MIN);

    // Minimum possible normal power of 10 exponent.
    println!("MIN_10_EXP = {}", std::f32::MIN_10_EXP);

    // One greater than the minimum possible normal power of 2 exponent.
    println!("MIN_EXP = {}", std::f32::MIN_EXP);

    // Smallest positive normal f32 value.
    println!("MIN_POSITIVE = {}", std::f32::MIN_POSITIVE);

    // Not a Number (NaN).
    println!("NAN = {}", std::f32::NAN);

    // Negative infinity (-∞).
    println!("NEG_INFINITY = {}", std::f32::NEG_INFINITY);

    // The radix or base of the internal representation of f32.
    println!("RADIX = {}", std::f32::RADIX);
}

```

Пожалуйста, самостоятельно изучите состав констант модуля `std::f64`:

```rust

fn main() {
    println!("std::f64");
    // Approximate number of significant digits in base 10.
    println!("DIGITS = {}", std::f64::DIGITS);

    // Difference between 1.0 and the next largest representable number.
    println!("EPSILON = {}", std::f64::EPSILON);

    // Infinity (∞).
    println!("INFINITY = {}", std::f64::INFINITY);

    // Number of significant digits in base 2.
    println!("MANTISSA_DIGITS = {}", std::f64::MANTISSA_DIGITS);

    // Largest finite f64 value.
    println!("MAX = {}", std::f64::MAX);

    // Maximum possible power of 10 exponent.
    println!("MAX_10_EXP = {}", std::f64::MAX_10_EXP);

    // Maximum possible power of 2 exponent.
    println!("MAX_EXP = {}", std::f64::MAX_EXP);

    // Smallest finite f64 value.
    println!("MIN = {}", std::f64::MIN);

    // Minimum possible normal power of 10 exponent.
    println!("MIN_10_EXP = {}", std::f64::MIN_10_EXP);

    // One greater than the minimum possible normal power of 2 exponent.
    println!("MIN_EXP = {}", std::f64::MIN_EXP);

    // Smallest positive normal f64 value.
    println!("MIN_POSITIVE = {}", std::f64::MIN_POSITIVE);

    // Not a Number (NaN).
    println!("NAN = {}", std::f64::NAN);

    // Negative infinity (-∞).
    println!("NEG_INFINITY = {}", std::f64::NEG_INFINITY);

    // The radix or base of the internal representation of f64.
    println!("RADIX = {}", std::f64::RADIX);
}

```

#### Чиcловые операции

Rust предоставляет основные математические операции с числовыми типами данных:
- сумма,
- разность,
- умножение,
- деление,
- остаток от деления.

Пример их использования:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    // addition
    let sum = 5 + 10;

    // subtraction
    let difference = 95.5 - 4.3;

    // multiplication
    let product = 4 * 30;

    // division
    let quotient = 56.7 / 32.2;

    // remainder
    let remainder = 43 % 5;

    println!("sum = {}", sum);
    println!("difference = {}", difference);
    println!("product = {}", product);
    println!("quotient = {}", quotient);
    println!("remainder = {}", remainder);
}

```
Каждое из этих выражений использует математические операции и вычисляет значение,
которое присваивается переменной. Приложение 2 содержит список всех математических
операций языка Rust.

#### Логический тип данных

В языке Rust логический тип данных `bool` может принимать два значения - `true`
и `false`. Обратите внимание, что Rust чувствительный к регистру. Так что любые
вариации с регистром в константных величинах будут считаться ошибкой.

Пример использования:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {

    let t = true;

    println!("t = {}", t);
    println!("!t = {}", !t);
    println!("t && t= {}", t && t);
    println!("t || t= {}", t || t);

    let f: bool = false; // with explicit type annotation

    println!("f = {}", f);
    println!("!t = {}", !t);
    println!("t && t= {}", t && t);
    println!("t || t= {}", t || t);
}

```

Логические значения применяются в операторах сравнения `if`, `for`.

#### Символьный тип данных

В Rust поддерживается работа с символьным типом данных `char`.
Пример:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
  let c:char = 'z';
  let z = 'ℤ';
  let heart_eyed_cat = '😻';
  println!("c = {}", c);
  println!("z = {}", z);
  println!("heart_eyed_cat = {}", heart_eyed_cat);
}
```
Размер для хранения одного символа составляет 4 байта.
Символьный тип поддерживает Юникод. Поддерживаются сложные символьные-юникод структуры,
такие как символы с ударением, китайские/японские/корейские иероглифы, смайлики
и другие возможные символы. Внутреннее представление символов не соответствует концепции
юникода. Подробнее об этом можно ознакомиться в главе 8.

### Сложные типы данных

*Сложные типы данных* - это группа множества значений объединённых в один тип данных.
В Rust существует два способа описания такого рода объединения типов. Это кортежи
(упорядоченный набор фиксированной длины) и массивы.

#### Группировка значений в кортежи (Tuples)

Кортеж - это способ группировки множества различных типов значений в один сложный
тип.

Пример:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
    println!("tup = {:?}", tup);
}
```

Присвоение множеству переменных содержания кортежа:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    let tup = (500, 6.4, 1);

    let (x, y, z) = tup;

    println!("The value of (x,y,z) is: ({},{},{})", x, y, z);
}

```

Есть ещё один способ доступа к содержанию кортежа - по индексу - с помощью `.`:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
  let x: (i32, f64, u8) = (500, 6.4, 1);
  let v1 = x.0;
  let v2 = x.1;
  let v3= x.2;

  println!("The value of (x,y,z) is: ({},{},{})", v1,v2,v3);
}
```

Обратите внимание, каким образом были определены типы данных кортежа.

#### Массивы

Массивы отличаются от кортежей большими ограничениями. В массиве все значения
имеют одинаковый тип данных. Также, важной особенностью массивов является их размер.
Он фиксируется при создании и не может быть изменён.

Пример:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];
    let b: [u8; 5] = [1, 2, 3, 4, 5];
    println!("a is: {:?}", a);
    println!("b is: {:?}", b);
}
```

Обратите внимание на тип скобок в выражении инициализации массива - они квадратные.
В стандартной библиотеке есть тип данных, аналогичный массиву, но имеющий возможность
изменения содержимого - это вектор.

Массивы подходят для хранения группы данных, состав и значения которых заранее
известны:

```rust
let months = ["January", "February", "March", "April", "May", "June", "July",
           "August", "September", "October", "November", "December"];
println!("a is: {:?}", months);
```

##### Организация доступа к элементам массива

Массив - это непрерывная область памяти, содержащаяся в стеке. Вы можете получить
доступ к какому-либо элементу по его индексу:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];
    let first = a[0];
    let second = a[1];

    println!("first = {}, second = {}", first, second);
}

```

##### Ошибка доступа к элементу массива

При попытке доступа к несуществующему индексу массива - программа аварийно завершится.
Важной особенностью языка Rust является предотвращение доступа к памяти, если
произошла ошибка какого-либо рода. Для системного языка программирования это большой
плюс, т.к. существует возможность на уровне языка предотвратить несанкционированный
доступ к памяти.

<span class="filename">Файл: src/main.rs</span>

```rust,ignore
fn main() {
    let a = [1, 2, 3, 4, 5];
    let index = 10;

    let element = a[index];

    println!("The value of element is: {}", element);
}
```
Описание ошибки при запуске этой программы:

```text
$ cargo run
   Compiling arrays v0.1.0 (file:///projects/arrays)
     Running `target/debug/arrays`
thread '<main>' panicked at 'index out of bounds: the len is 5 but the index is
 10', src/main.rs:6
note: Run with `RUST_BACKTRACE=1` for a backtrace.
```

В главе 9 будет подробно рассказано об возможных реакциях на ошибку.
