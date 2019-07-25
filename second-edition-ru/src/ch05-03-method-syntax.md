## Особенности определения методов

*Методы* имею множество схожих черт с функциями.
Сходства:
- Определение начинается с ключевого слова `fn`, далее идёт имя.
- Они имеют параметры и возвращаемое значение.
- Они содержат код, который выполняется, когда метод вызывается.
Различия:
- Они определяются в контексте структур (или перечислений или типажей, которые мы будем обсуждать в главах 6 и 17).
- Первый параметр всегда `self`, предоставляющий ссылку на экземпляр структуры.

### Определение методов

Давайте изменим функцию `area`, которая имеет один входной параметр, ссылку на экземпляр
`Rectangle`. Сделаем это определение частью функционала структуры `Rectangle`.
Пример 5-13:

<span class="filename">Filename: src/main.rs</span>

```rust
#[derive(Debug)]
struct Rectangle {
    length: u32,
    width: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.length * self.width
    }
}

fn main() {
    let rect1 = Rectangle { length: 50, width: 30 };

    println!(
        "The area of the rectangle is {} square pixels.",
        rect1.area()
    );
}
```

<span class="caption">Пример 5-13: Определение метода `area` в структуре `Rectangle`</span>

Для определения функций в контексте структуры мы пишем `impl`, далее имя структуры.
Внутри фигурных скобок располагаются определения функций. У функции `area` первым
и единственным аргументом является ссылка на экземпляр структуры. Метод экземпляра
вызывается через точку. Далее в скобках следуют аргументы.

Компилятор знает, что `&self` в данном контексте это `rectangle: &Rectangle`. Обратите
внимание, что мы используем ссылку `&self`. Метод может взять `self` во владение,
заимствовать, как неизменяемую переменную, а также может заимствовать, как изменяемую
переменную.

В данной функции мы выбрали использование `&self`, так как нам не нужно владение,
нам нужно только чтение данных структуры. Если нам понадобиться изменять значения
экземпляра структуры, мы должны вызвать `&mut self`. Очень редко может понадобиться
получить владение `self`, т.к. это может лишь понадобиться для трансформации экземпляра
во что-то другое.

> ### Где используется оператор `->`?
>
>В языках C++, два различных оператора используются для вызова методов:
> вы используете `.` если вы вызываете метод непосредственно из экземпляра структуры
>  и с помощью `->` если вызываем метод из ссылки на объект. Другими словами, если
> `object` является ссылкой вызовы метода `object->something()` и `(*object).something()`
> аналогичны.
>
> Rust не имет такого эквивалента (оператора `->`), наоборот, в Rust функционал,
> который называется *автоматическая ссылка и разыменование*. Вызов методов является
> одним из немногих мест в Rust, в котором есть такой функционал.
>
> Как это работает: когда вы вызываете метод `object.something()`, Rust автоматически
> добавляет `&`, `&mut`, or `*` так чтобы `object` имел соответствующие опции
> Другими словами, строки *p1.distance(&p2);* *(&p1).distance(&p2);* эквивалентны:
>
```rust
#[derive(Debug,Copy,Clone)]
struct Point {
   x: f64,
   y: f64,
}

impl Point {
   fn distance(&self, other: &Point) -> f64 {
      let x_squared = f64::powi(other.x - self.x, 2);
      let y_squared = f64::powi(other.y - self.y, 2);

      f64::sqrt(x_squared + y_squared)
   }
}

fn main() {
   let p1 = Point { x: 0.0, y: 0.0 };
   let p2 = Point { x: 5.0, y: 6.5 };
   p1.distance(&p2);
   (&p1).distance(&p2);
}

```

Первый вариант *p1.distance(&p2);* выглядит давольно-таки понятно.
Компилятор Rust может определить, что можно делать с переменной
(читать значение (`&self`), изменять содержание (`&mut self`) или сохранять значение (`self`) ).
Тот факт, что описание владения происходит неявно, делает код программы более компактным.


### Методы с несколькими параметрами

Давайте дальше практиковаться в использовании методов. Добавим метод проверки
вхождения одного прямоугольника в другой. Метод возвратит `true` если ответ положительный
и `false` если отрицательный.
Пример 5-14:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
fn main() {
    let rect1 = Rectangle { length: 50, width: 30 };
    let rect2 = Rectangle { length: 40, width: 10 };
    let rect3 = Rectangle { length: 45, width: 60 };

    println!("Can rect1 hold rect2? {}", rect1.can_hold(&rect2));
    println!("Can rect1 hold rect3? {}", rect1.can_hold(&rect3));
}
```

<span class="caption">Listing 5-14: Демонстрация использование ещё несуществующего метода
`can_hold` method</span>

Предполагаемый вывод:

```text
Can rect1 hold rect2? true
Can rect1 hold rect3? false
```

Мы знаем, что где и как добавляются методы внутри конструкции `impl Rectangle`.
Пример 5-15:

<span class="filename">Filename: src/main.rs</span>

```rust
#[derive(Debug)]
struct Rectangle {
    length: u32,
    width: u32,
}

impl Rectangle {
   fn area(&self) -> u32 {
       self.length * self.width
   }

   fn can_hold(&self, other: &Rectangle) -> bool {
       self.length > other.length && self.width > other.width
   }
}

fn main(){
let rect1 = Rectangle { length: 50, width: 30 };
   let rect2 = Rectangle { length: 40, width: 10 };
   let rect3 = Rectangle { length: 45, width: 60 };
   println!("area of rect1 = {}", rect1.area());
   println!("Can rect1 hold rect2? {}", rect1.can_hold(&rect2));
   println!("Can rect1 hold rect3? {}", rect1.can_hold(&rect3));
}

```

<span class="caption">Listing 5-15: Реализация метода `can_hold` `Rectangle`,
который получает другой экземпляр `Rectangle` в качестве параметра</span>

Когда будет выполнен код метода `main` вы увидите ожидаемый вывод в терминальной
строке. Методы могут иметь множество параметров, которые мы добавляем после параметра
`self` и все эти параметра работают также, как и параметры функции.

### Ассоциированные функции

Ещё одна удобная опция блока `impl` - мы можем определять функции, которые не
имеют параметра `self`. Они называются *ассоциированными функциями*, т.к. они
находятся внутри структуры. Они функции, не методы, т.к. они не требуют для их
использования ссылки на экземпляр структуры. Пример (`String::from`).

Такие функция часто используются для инициализации экземпляра структуры.
Пример:

<span class="filename">Filename: src/main.rs</span>

```rust
 #[derive(Debug)]
 struct Rectangle {
     length: u32,
     width: u32,
 }

impl Rectangle {
    fn square(size: u32) -> Rectangle {
        Rectangle { length: size, width: size }
    }
}
```

```rust
#[derive(Debug)]
struct Rectangle {
    length: u32,
    width: u32,
}

impl Rectangle {
   fn area(&self) -> u32 {
       self.length * self.width
   }

   fn can_hold(&self, other: &Rectangle) -> bool {
       self.length > other.length && self.width > other.width
   }
   fn square(size: u32) -> Rectangle {
       Rectangle { length: size, width: size }
   }
}

fn main(){
let rect1 = Rectangle { length: 50, width: 30 };
   let rect2 = Rectangle { length: 40, width: 10 };
   let rect3 = Rectangle { length: 45, width: 60 };
   println!("area of rect1 = {}", rect1.area());
   println!("Can rect1 hold rect2? {}", rect1.can_hold(&rect2));
   println!("Can rect1 hold rect3? {}", rect1.can_hold(&rect3));
   let rect4 = Rectangle::square(50);
   println!("area of rect4 = {}", rect4.area());
}

```

Для вызова ассоциированной функции используется `::`. Пример (`let sq = Rectangle::square(3);`.
Также `::` используется в областях видимости создаваемые модулями. Об этом поговорим
в главе 7.

### Несколько блоков `impl`

Каждая структура может использовать множество блоков `impl`. Пример 5-15:

```rust
#[derive(Debug)]
struct Rectangle {
    length: u32,
    width: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.length * self.width
    }
}

impl Rectangle {
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.length > other.length && self.width > other.width
    }
}
impl Rectangle {
    fn square(size: u32) -> Rectangle {
        Rectangle {
            length: size,
            width: size,
        }
    }
}
fn main() {
    let rect1 = Rectangle {
        length: 50,
        width: 30,
    };
    let rect2 = Rectangle {
        length: 40,
        width: 10,
    };
    let rect3 = Rectangle {
        length: 45,
        width: 60,
    };
    println!("area of rect1 = {}", rect1.area());
    println!("Can rect1 hold rect2? {}", rect1.can_hold(&rect2));
    println!("Can rect1 hold rect3? {}", rect1.can_hold(&rect3));
    let rect4 = Rectangle::square(50);
    println!("area of rect4 = {}", rect4.area());
}

```

<span class="caption">Пример 5-16: неоднократное использование `impl`</span>

В главе 10 вы увидите, как множество таких блоков может быть эффективно использовано.

## Итоги

Структуры помогают создавать типы и добавлять к ним методы. Методы могут быть
статическими и динамическими (требующими ссылки на экземпляр структуры).

Также существуют и другие способы создавать новые типы данных. Один из них - это
перечисления.
