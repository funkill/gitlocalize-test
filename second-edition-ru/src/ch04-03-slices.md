﻿## Срезы

*Срезы (slices)*  - это ссылочный тип, не использующий владение.
Это непрерывная коллекция упорядоченных элементов.

Рассмотрим учебную задачу. Необходимо написать функцию, входным параметром которой
является строка. Выходным значением функции является первое слово, которое будет
найдено в этой строке. Если функция не находит разделителя слов (пробела), она
возвращает эту строку.

Прежде всего рассмотрим описание этой функции:

```rust,ignore
fn first_word(s: &String) -> ?
```

Функция `first_word` имеет входной параметр типа `&String`. Нам не нужно владение
переменной для её работы, так что это то что нам нужно. Для решения задачи мы можем
найти индекс конца слова в строке. Вот как это можно сделать с помощью функции 4-10:

<span class="filename">Filename: src/main.rs</span>

```rust
fn first_word(s: &String) -> usize {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return i;
        }
    }

    s.len()
}

fn main() {
    let index = first_word(&String::from("hello, Nik!"));
    println!("{}", index);
}

```

<span class="caption">Listing 4-10: Пример функции `first_word`, которая возвращает
index пробела в строке типа `String`</span>

Теперь давайте изучим код этой функции. Для того, чтобы найти пробел в строке,
мы превратим строку в массив байтов, используя метод `as_bytes`:

```rust,ignore
let bytes = s.as_bytes();
```

Далее, используя метод массива `iter()` мы создаём объект для последовательного
перебора содержания массива - итератор. Далее, используя цикл `for`, мы перебираем
байты и анализируем каждый из них. Обратите внимание, что при каждой итерации мы
получаем индекс элемента и ссылку на него:

```rust,ignore
for (i, &item) in bytes.iter().enumerate() {
```

Мы будет изучать итераторы более детально в главе 13. Сейчас, достаточно понять,
что метод `iter` возвращает каждый элемент коллекции. Метод `enumerate`
передаёт результаты работы метода `iter` в кортеж. Первый элемент этого кортежа
возвращает индекс, второй элемент - ссылку на элемент. Такой способ перебора элементов
массива наиболее удобный.

Так как метод `enumerate` возвращает кортеж, мы можем использовать шаблон создающий
переменные, которые в дальнейшем можно использовать внутри тела цикла.

Нам надо найти байт, который представляет собой значение пробела. Для этого мы
приводим символьную константу ' ' к типу байт *b' '*. В выражении `if` мы сравниваем
полученное таким образом константное значение с текущим байтом из массива.

Если мы находим пробел, мы возвращаем позицию пробела. Иначе мы возвращаем длину
массива `s.len()`:

```rust,ignore
    if item == b' ' {
        return i;
    }
}
s.len()
```

Таким образом, мы получаем искомое значение. Но оно может устареть в будущем  4-11:

<span class="filename">Filename: src/main.rs</span>

```rust
# fn first_word(s: &String) -> usize {
#     let bytes = s.as_bytes();
#
#     for (i, &item) in bytes.iter().enumerate() {
#         if item == b' ' {
#             return i;
#         }
#     }
#
#     s.len()
# }
#
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s); // word will get the value 5.

    s.clear(); // This empties the String, making it equal to "".

    // word still has the value 5 here, but there's no more string that
    // we could meaningfully use the value 5 with. word is now totally invalid!
}
```

<span class="caption">Listing 4-11: Сохранение результата вызова функции `first_word`,
потом изменяем содержимое переменной `s`</span>

Эта программа скомпилируется без всяких проблем.

Создадим ещё одну функцию, которая возвращает индексы начала и конца первого слова.
Вот как будет выглядеть её описание:

```rust,ignore
fn second_word(s: &String) -> (usize, usize) {
```

Обратите внимание, что весьма сложно удерживать в синхронном состоянии вcе эти переменные
(входящие и исходящие). Для этих целей существуют срезы.

### Строковые срезы

Строковые срезы - это ссылка на часть строки `String`, и её инициализация
выглядит следующим образом:

```rust
let s = String::from("hello world");

let hello = &s[0..5];
let world = &s[6..11];
```

Эта инициализация похожа на создание ссылки на переменную `String`, но с дополнительным
условием - указанием отрезка `[0..5]`. Вместо целой переменной мы получаем ссылку
на её часть. Начало и конец отрезка включено в срез, а вот окончание - 
нет.

Мы можем создавать срезы, используя определение отрезка `[starting_index..ending_index]`,
где `starting_index` означает первую позицию в срезе, а `ending_index` на единицу больше,
чем последняя позиция. Таким образом, в примере `let world = &s[6..11];` переменная `world`
будет срезом, которая содержит ссылку на 7-ой байт в `s` и длину равной `5`.

Рисунок 4-12 объясняет это схематично.

<img alt="world containing a pointer to the 6th byte of String s and a length 5" src="img/trpl04-06.svg" class="center" style="width: 50%;" />

<span class="caption">Figure 4-12: Срез ссылается на часть
`String`</span>

Синтаксис Rust позволяет упростить описание среза, если он начинается
с 0-го индекса:

```rust
fn main(){
    let s = String::from("hello");

    let slice = &s[0..2];
    println!("{}",slice);
    let slice = &s[..2];
    println!("{}",slice);
}
```

Таким же образом можно поступить с последним элементом, если это последний байт в
`String`:

```rust
fn main() {
    let s = String::from("hello");

    let len = s.len();
    println!("sting length = {}", len);
    let slice = &s[3..len];
    println!("{}", slice);
    let slice = &s[3..];
    println!("{}", slice);
}

```

Таким образом, срез целой строки можно описать так:

```rust
fn main() {
    let s = String::from("hello");
    println!("{}", s);
    let len = s.len();
    println!("a length of the string = {}", s);
    let slice = &s[0..len];
    println!("{}", slice);
    let slice = &s[..];
    println!("{}", slice);
}

```

> Внимание: Индексы среза строк должны соответствовать границам UTF-8 симоволов.
> Если вы попытаетесь получить срез нарушая границы мультибайтового символа,
> то вы получите ошибку времени исполнения. В рамках этой главы мы будем 
> предполагать только ASCII кодировку. Более тщательно работу со строками и UTF-8 
> мы рассмотрим в главе 8.

Применим полученные знания и перепишем метод `first_word`. Для обозначения типа
"срез строки" существует запись `&str`:

<span class="filename">Filename: src/main.rs</span>

```rust
fn first_word(s: &String) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}
```

Теперь, вызвав метод `first_word`, мы получим один объект, который включает в себя
всю необходимую информацию.

Аналогичным образом можно переписать и второй метод `second_word`:

```rust,ignore
fn second_word(s: &String) -> &str {
```

Благодаря использованию срезов нельзя изменить данные строки, если
на неё ссылается срез (т.к. это может привести к ошибке):

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s);

    s.clear(); // Error!
}
```

Ошибка компиляции:

```text
17:6 error: cannot borrow `s` as mutable because it is also borrowed as
            immutable [E0502]
    s.clear(); // Error!
    ^
15:29 note: previous borrow of `s` occurs here; the immutable borrow prevents
            subsequent moves or mutable borrows of `s` until the borrow ends
    let word = first_word(&s);
                           ^
18:2 note: previous borrow ends here
fn main() {

}
^
```

Благодаря соблюдению правил, Rust просто исключает класс подобных ошибок.

#### Строковые константы - срезы

Вооружившись знаниями о срезах можно посмотреть по-новому на
инициализацию переменной строкового типа:

```rust
let s = "Hello, world!";
```

Тип `s` является `&str` - это срез, указывающий на конкретное значение в коде программы.
Поэтому строковый литерал неизменяемый, а тип `&str` это неизменяемая ссылка.

#### Строковые срезы как параметры

Используя строковые срезы, как параметры вы можете улучшить
код ваших методов:

```rust,ignore
fn first_word(s: &String) -> &str {
```

Также можно записать этот код следующим образом:

```rust,ignore
fn first_word(s: &str) -> &str {
```

Если мы используем срез, мы можем передавать его в методы.
Использование срезов вместо переменных делает код более удобным:

<span class="filename">Filename: src/main.rs</span>

```rust
fn first_word(s: &str) -> &str {
   let bytes = s.as_bytes();
   for (i, &item) in bytes.iter().enumerate() {
       if item == b' ' {
           return &s[0..i];
       }
     }
   &s[..]
}
fn main() {
    let my_string = String::from("hello world");

    // first_word works on slices of `String`s
    let word = first_word(&my_string[..]);
    println!("{}",word);
    let my_string_literal = "hello world";

    // first_word works on slices of string literals
    let word = first_word(&my_string_literal[..]);
    println!("{}",word);
    // since string literals *are* string slices already,
    // this works too, without the slice syntax!
    let word = first_word(my_string_literal);
    println!("{}",word);
}
```

### Другие срезы

Существуют также срезы других типов. Рассмотрим массив:
```rust
let a = [1, 2, 3, 4, 5];
```
Создадим срез:

```rust
let a = [1, 2, 3, 4, 5];

let slice = &a[1..3];
```

Этот срез имеет тип данных `&[i32]`. Мы поговорим о таком типе
коллекций в главе 8.

## Итоги

Такие концепции как владение, заимствование и срезы - это способы
защиты использования памяти.  Rust даёт вам возможность контролировать использование
памяти.

Владение влияет на множество других концепций языка Rust.
В следующей главе мы рассмотрим способ группировки данных в `struct`.

