# Написание binding'ов

Binding это код, позволяющий использовать существующий код на другом языке (библиотеку).

Для статической линковки необходимо объявить используемые типы данных и функции:

```d
struct Foo { float x, y; }
extern(C) int somefunc(Foo*, int);
```

И добавить флаг линковки в сборочный скрипт (`dub.sdl`, `dub.json` или `meson.build`).

Для динамической линковки процесс чуть более объёмный и требует:

* объявления типов
* объявление указателей на функции
* кода загрузки указателей из динамической библиотеки (платформозависимый)
* реализации функций-обёрток, которые будут вызывать функции по указателям

Библиотека будет загружаться во время работы программы, флагов линковки и наличия
самой библиотеки в скомпилированном виде на момент компиляции приложения не требуется.

Для больших библиотек это достаточно объёмная работа, для её упрощения существует несколько библиотек:

* [bindbc](https://code.dlang.org/packages/bindbc-loader) -- самая распостранённая,
  ранее автор работал над `Derelict` (библиотека со схожей функциональностью), `bindbc`
  является переосмыслением, поддерживает `-betterC`;
* [dynamic](https://code.dlang.org/packages/dynamic) и [ssll](https://code.dlang.org/packages/ssll) -- 
  библиотеки со схожей с `bindbc` функциональностью, но имею возможность генерации кода на основе
  объявлений, по объёму ручной работы биндинг динамической библиотеки становится схож с биндингом статической.

Использование этих библиотек описано в их `README.md`.

Так же существуют проекты, позволяющие использовать C/С++ библиотеки прямо из D кода [dpp](https://code.dlang.org/packages/dpp) (препроцессинг исходников и кодогенерация) и [calypso](https://github.com/Syniurge/Calypso) (совмещение на уровне ast компилятора).