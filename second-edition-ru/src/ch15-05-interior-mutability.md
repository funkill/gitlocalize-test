## `RefCell<T>` и шаблон внутренней изменяемости (Interior Mutability Pattern)

*Внутренняя изменяемость* - это шаблон проектирования в Rust, позволяющий изменять
данные даже если ссылки на эти данные неизменяемые (что обычно нельзя сделать,
из-за правил владения). Это шаблон предлагает использовать небезопасный код внутри
структур данных для связи правил Rust заимствования и изменяемости. Мы подробнее
поговорим о небезопасном коде в Главе 19. Этот шаблон полезен, когда вы уверены,
что правила заимствования будут действительный во время работы программы, даже если
компилятор не будет в этом уверен. Небезопасный код будет использован внутри
безопасной API.

Давайте рассмотрим тип данных `RefCell<T>`, который реализует этот шаблон.

### `RefCell<T>` имеет внутреннюю изменяемость

В отличие от `Rc<T>` тип `RefCell<T>` представляет собой единственного владельца
данных . Что же отличает `RefCell<T>` от `Box<T>`? Давайте вспомним правила
заимствования из Главы 4:
1. В любой момент времени вы можете иметь *одно из*, но не оба:
   * Одна изменяемая ссылка.
   * Любое количество неизменяемых ссылок.
2. Ссылки всегда должны быть действительными.

С помощью ссылок и  `Box<T>` правила заимствования применяются на этапе компиляции.
С помощью `RefCell<T>` они применяются во время работы программы. Если вы нарушите
эти правила, работая с ссылками - будет ошибка компиляции. Если вы работаете с
`RefCell<T>` и вы нарушите эти правила - вы получите `panic!`.

Статический анализ, который проводит компилятор Rust, по своей сути консервативен.
Существуют свойства кода, которые невозможно обнаружить, анализируя
код: самая известная проблема с остановкой, которая выходит за рамки этого
но интересная тема для исследования, если вы заинтересованы.

Поскольку некоторый анализ невозможен, компилятор Rust не пытается даже
что-либо предпринять. Если он не может быть уверен, поэтому он консервативен и
иногда отвергает правильные которые фактически не нарушали бы гарантии Rust.
Иными словами, если Rust пропускает неверную программу, люди не смогут доверять
гарантиям Rust. Если Rust отклонит правильную программу, программист будет
быть неудобным, но ничего катастрофического не может произойти. `RefCell <T>` полезен
когда вы знаете, что правила заимствования соблюдаются, но компилятор не может
понять, что это правильно.

Подобно `Rc <T>`, `RefCell <T>` используется только для однопоточных
сценариев. Мы поговорим о том, как получить функциональность `RefCell <T>` в
многопоточную программу в следующей главе о параллелизме. Пока, все, что вы
нужно знать, что если вы попытаетесь использовать `RefCell <T>` в многопоточном
контекст, вы получите ошибку времени компиляции.

С помощью ссылок мы используем синтаксис `&` и `& mut` для создания простых ссылок
и изменяемых, соответственно. Но с `RefCell <T>` мы используем методы `borrow`
и `borrow_mut`, которые являются частью безопасного API, который имеет` RefCell <T> `.
`borrow` возвращает тип умного указателя` Ref`, а `borrow_mut` возвращает
умный указатель типа `RefMut`. Эти два типа реализуют `Deref`, чтобы мы могли
рассматривайте их так, как если бы они были регулярными ссылками. `Ref` и` RefMut`
отслеживают заимствование динамически, и их реализация `Drop` отпускает заимствования
динамически.

В листинге 15-14 показано, как выглядит `RefCell <T>` с функциями, которые
заимствовать их параметры неизменно и изменчиво. Обратите внимание, что переменная
`data` объявляется неизменной с `let data`, а не` let mut data`, но
`a_fn_that_mutably_borrows` разрешено заимствовать данные динамически и производить
изменения данных!

<span class="filename">Filename: src/main.rs</span>

```rust
use std::cell::RefCell;

fn a_fn_that_immutably_borrows(a: &i32) {
    println!("a is {}", a);
}

fn a_fn_that_mutably_borrows(b: &mut i32) {
    *b += 1;
}

fn demo(r: &RefCell<i32>) {
    a_fn_that_immutably_borrows(&r.borrow());
    a_fn_that_mutably_borrows(&mut r.borrow_mut());
    a_fn_that_immutably_borrows(&r.borrow());
}

fn main() {
    let data = RefCell::new(5);
    demo(&data);
}
```

<span class="caption">код 15-14: использование `RefCell<T>`, `borrow` и
`borrow_mut`</span>

This example prints:

```text
a is 5
a is 6
```

В методе `main` мы создали экземпляр `RefCell<i32>` содержащий 5 и сохранили в него
значение 5. Обратите внимание, что этот экземпляр мы сохранили в неизменяемую переменную.
Далее, мы передали её функции `demo`. Обратите внимание, что аргумент функции тоже
неизменяемая ссылка.

В функции `demo` мы передаём неизменяемую ссылку на значение внутри `RefCell<i32>`
посредством вызова метода `borrow` в функцию `a_fn_that_immutably_borrows`. Далее,
что более интересно, мы передаём изменяемую ссылку на значение внутри `RefCell<i32>`
посредством вызова метода `borrow_mut` в функцию `a_fn_that_mutably_borrows`.

### Правила заимствования проверяются `RefCell<T>` в момент работы программы

Рассмотрим пример из Главы 4. Этот код использует ссылки, который пытается
создать изменяемые ссылки в одной и той же области видимости (ошибка):

```rust,ignore
let mut s = String::from("hello");

let r1 = &mut s;
let r2 = &mut s;
```

Описание ошибки компиляции:

```text
error[E0499]: cannot borrow `s` as mutable more than once at a time
 -->
  |
5 |     let r1 = &mut s;
  |                   - first mutable borrow occurs here
6 |     let r2 = &mut s;
  |                   ^ second mutable borrow occurs here
7 | }
  | - first borrow ends here
```

Если же использовать `RefCell<T>` и вызвать `borrow_mut` дважды код скомпилируется,
но в момент работы произойдет ошибка:

```rust,should_panic
use std::cell::RefCell;

fn main() {
    let s = RefCell::new(String::from("hello"));

    let r1 = s.borrow_mut();
    let r2 = s.borrow_mut();
}
```

код компилируется, но при его работе (`cargo run`) происходит ошибка:

```text
    Finished dev [unoptimized + debuginfo] target(s) in 0.83 secs
     Running `target/debug/refcell`
thread 'main' panicked at 'already borrowed: BorrowMutError',
/stable-dist-rustc/build/src/libcore/result.rs:868
note: Run with `RUST_BACKTRACE=1` for a backtrace.
```

Ошибка времени выполнения `BorrowMutError` похожа на ошибку компиляции - сообщается,
что мы уже заимствовали `s` один раз и нам нельзя заимствовать её снова. Мы не
можем обойти правила заимствования во время работы. Поэтому использовать обертку
`RefCell<T>` надо весьма аккуратно. У вас может не быть ошибок при компиляции, но
при работе кода - ошибка случится - это весьма негативно скажется на отладке такого
кода.

### Множественное владение изменяемыми данными при совместном использовании `Rc<T>`
и `RefCell<T>`

Итак, почему мы решили компромиссное решение использовать `RefCell <T>`?
Ну, помните, когда мы говорили, что `Rc <T>` позволяет вам иметь неизменяемый
ссылка на `T`? Учитывая, что `RefCell <T>` неизменен, но имеет возможность внутреннего
изменения мы можем комбинировать `Rc<T>` и `RefCell<T>`, чтобы получить тип, который
ссылка подсчитана и изменена. В листинге 15-15 показан пример того, как это сделать
что, снова возвращаясь к списку наших минусов из Листинга 15-5. В этом примере,
вместо сохранения значений `i32` в списке совпадений, мы будем хранить
`Rc <RefCell <i32>>` значения. Мы хотим сохранить этот тип, чтобы мы могли
иметь владельца данных, который не входит в список (несколько владельцев
функциональность, которую предоставляет `Rc <T>`), и поэтому мы можем изменять
внутреннее значение  `i32`(функция внутренней изменчивости, предоставляемая `RefCell <T>`):

<span class="filename">Filename: src/main.rs</span>

```rust
#[derive(Debug)]
enum List {
    Cons(Rc<RefCell<i32>>, Rc<List>),
    Nil,
}

use List::{Cons, Nil};
use std::rc::Rc;
use std::cell::RefCell;

fn main() {
    let value = Rc::new(RefCell::new(5));

    let a = Cons(value.clone(), Rc::new(Nil));
    let shared_list = Rc::new(a);

    let b = Cons(Rc::new(RefCell::new(6)), shared_list.clone());
    let c = Cons(Rc::new(RefCell::new(10)), shared_list.clone());

    *value.borrow_mut() += 10;

    println!("shared_list after = {:?}", shared_list);
    println!("b after = {:?}", b);
    println!("c after = {:?}", c);
}
```

<span class="caption">код 15-15: использование `Rc<RefCell<i32>>` для создания
`List`, который мы можем изменять</span>

Мы создали экземпляр `Rc<RefCell<i32>>`. Мы сохранили его в `value`.
Далее мы создали `List` в `a`, который содержит `Cons` . Далее, мы обернули это
значение в `Rc<T>`, благодаря чему мы смогли создать списки `b` и `c`.

Далее, мы добавили число 10 к имеющимися значению с помощью разыменования и
использования функции `borrow_mut`.

Далее мы вывели на печать `shared_list`, `b` и `c`:

```text
shared_list after = Cons(RefCell { value: 15 }, Nil)
b after = Cons(RefCell { value: 6 }, Cons(RefCell { value: 15 }, Nil))
c after = Cons(RefCell { value: 10 }, Cons(RefCell { value: 15 }, Nil))
```

Помимо `RefCell<T>` существует ещё `Cell<T>`, которое копирует в и из.
`Mutex<T>` предлагает изменяемость между потоками. Об этом мы поговорим в соответствующей
главе.