# Обработка ошибок

В языке Rust и в стандартной библиотеке есть средства обработки ошибок. В большинстве
случаев, Rust делает множество проверок, чтобы избежать ошибок, но их всё же недостаточно,
чтобы исключить их вовсе.

В Rust ошибки делятся на две большие группы: обрабатываемые и необрабатываемые.
Обрабатываемые ошибки случаются, когда, например, файл не найден. К необрабатываемым
ошибками, т.н. багам кода относятся обращения к неверному индексу массива.

Во многих языках эти виды ошибок не разделяют каким-либо образом. Rust не имеет
механизма обработки ошибок. Вместо этого есть значения типа `Result<T, E>` для
обрабатываемых ошибок и макрос `panic!`, который останавливает выполнение программы,
когда происходит необрабатываемая ошибка. В конце главы мы поделится методами оценки
проблем: как следует поступить, если произошла ошибка - обработать исключительную
ситуацию или следует остановить выполнение программы.