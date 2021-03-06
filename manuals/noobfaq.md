# FAQ для тех кто не в теме

## D это Delphi?

Нет, D к Delphi не имеет отношения. D -- следующая буква алфавита после C.
Это некоторый намёк на то, что D это более прогрессивный и новый язык,
созданный после C и C++.

## Виртуальная машина

D компилируется в нативный бинарный код под каждую поддерживаемую платформу,
отсутствует необходимость в виртуальной машине. Но в компиляторе ldc2 есть
возможность JIT-компиляции ([обсуждение](https://forum.dlang.org/thread/bskpxhrqyfkvaqzoospx@forum.dlang.org)).

## А чем D лучше С++?

Тем что создавался с оглядкой на опыт C++ и имеет ряд преимуществ в повседневном
использовании: 
* сборщик мусора (можно писать код без его использования, если хочется, но это не так комфортно как со сборщиком)
* модули вместо инклудов
* хорошая стандартная библиотека
* расширенные возможности в compile-time
* встроенные типы строк (utf-8)

и [многое другое](https://dlang.org/comparison.html)

## Почему D?

Частый вопрос, на который каждый разработчик может дать только субъективный ответ.

Постарался собрать самые популярные ответы в несколько категорий:

### D легче и удобней чем C++

Если Вы знаете стандарт C++ как свои пять пальцев, так же хорошо владеете популярными
библиотеками типа boost и/или Qt, то этот аргумент Вам ничего не даёт.

Другие люди, начиная писать на D чувствуют бОльшую свободу и лёгкость при
написании кода, особенно для простых задач. У D нет такого объёма legacy как у
C++ и многие вещи перереализованы чуть красивее, лаконичней, логичней.

Compile-time интроспекция и выполнение кода во время компиляции на голову лучше
реализованы чем в C++, что делает код шаблонов читабельным и лёгким для написания.

### D значительно быстрее чем python при сопоставимой сложности кода

D компилируется в нативный бинарный код и сопоставим по скорости исполнения с С++.
При использовании ldc разница с С++ сокращается до единиц процентов при эквивалентном коде.

В отличии от Python D поддерживает бинарную совместимость с C и это позволяет использовать
уже собранные C-шные библиотеки посредством написания небольшого binding'а (нужно объявить
типы данных и используемые сигнатуры). [Как писать биндиги](bindings.md).

### D удобней и легковесней чем Java

Java накладывает огромное количество ограничений на разработчика, хотя и даёт
многое взамен. D проектировали с оглядкой на Java и в D есть несколько концепций,
заимствованных оттуда:

* интерфейсы (в D добавили возможность делать `final` методы)
* отсутствие ромбовидного наследования
* иерархия модулей на основе файловой иерархии (модуль `some.foo` должен лежать в `some/foo.d`, при этом
  есть некоторые поблажки для локальных модулей)

D не нуждается в виртуальной машине, поэтому требует меньше памяти при исполнении,
при этом в D есть сборщик мусора, что позволяет так же легко манипулировать данными
как на Java.

Для небольших проектов D требует меньше телодвижений по созданию проекта, например
можно создать программу в один файл, которая будет выглядеть как скрипт:

```d
#!/bin/dub
/+ dub.sdl:
    dependency "serialport" version="~>2.2.2"
 +/

import std.stdio;
import std.exception : enforce;
import serialport;

void main(string[] args)
{
    enforce(args.length == 3, "use: program <PORT> <data>");

    auto sp = new SerialPortBlk(args[1]);
    sp.readTimeout = 5.seconds;

    sp.write(args[2]);

    void[2048] buffer = void;

    const ret = cast(char[])sp.read(buffer[], SerialPort.CanRead.anyNonZero);

    // если по com-порту придёт ответ в течении 5 секунд, он будет выведен на экран
    writeln(ret);
}
```

запуск: `dub sptest.d /dev/pts/4 okda`

`dub` при необходимости скачает и установит зависимости (`serialport`), затем
соберёт программ и потом запустит её с аргументами `/dev/pts/4` и `okda`.

## D завтра не загнётся?

Язык появился достаточно давно (версия 2 появился в 2007 году) и пока не загнулся.
Автор оригинального компилятора dmd (Walter Bright) продолжает развивать язык.

В феврале 2020 года D занял 14 место в рейтинге Tiobe, показав значительный прирост
интереса (в феврале 2019 он был на 23-ем месте).

![](../img/tiobe_feb_2020.png)

За активностью можно следить на:
* [оф. форуме языка](https://forum.dlang.org/group/announce)
* [github странице dlang](https://github.com/dlang)
* [github странице ldc2](https://github.com/ldc-developers/ldc) (популярный компилятор с fe от dmd и be llvm)

## А по библиотекам есть чё?

Стандартом de facto стал инструмент для сборки и работы с зависимостями `dub`,
который имеет [собственный репозитарий пакетов](http://code.dlang.org/).

Существуют проекты поддерживающие систему сборки [meson](https://mesonbuild.com/D.html),
например [libmir](https://github.com/libmir), но их количество значительно меньше библиотек,
поддерживащих dub.

## Можно писать под разные платформы?

Да, можно. `#ifdef` отсутствует из-за отсутствия препроцессора, есть другие способы
условной компиляции, например `version(...) { ... }` или `static if(...) { ... }`.

Компилятор от создателей языка `dmd` работает на x86_64 на осноных ОС:
* Windows
* Linux
* Mac OS

[список](https://dlang.org/download.html)

Компилятор `ldc2` с llvm бэкендом позволяет компилировать код под arm и некоторые другие
платформы, отличные от x86 ([инфа](https://wiki.dlang.org/Cross-compiling_with_LDC)).
Так же код, сгенерированный `ldc2`, несколько производительней чем у `dmd`.

## Кодировки

Исходные коды по умолчанию в utf-8. В языке есть типы строк для utf-8 `string`,
utf-16 `wstring` и utf-32 `dstring`. В стандартной библиотеке 
[есть](https://dlang.org/phobos/std_utf.html) методы работы с разными кодировками.

В повседневном использовании редко необходимы явные преобразования.

## Комьюнити

Ссылки на англоговорящее комьюнити можно найти в разделе `Community` на [оф. сайте](https://dlang.org/).

Рускоговорящее комьюнити есть в telegram [dlangru](https://t.me/dlangru).

## Benchmarks

* https://www.techempower.com/benchmarks/
* https://github.com/frol/completely-unscientific-benchmarks
* https://github.com/kennyalive/DigitalWhip
* https://github.com/kostya/benchmarks
* https://habr.com/post/261201/
* https://github.com/nordlow/compiler-benchmark

## Насколько сложна декомпиляция кода написанного на D?

D не C#, сложность декомпиляции сопоставима со сложностью декомпиляции C++.

## Инструменты

Для vs-code есть:

* [code-d](https://marketplace.visualstudio.com/items?itemName=webfreak.code-d)
* [vscode-dls](https://marketplace.visualstudio.com/items?itemName=LaurentTreguier.vscode-dls)

Для Visual Studio есть [visual-d](https://github.com/dlang/visuald).

Так же gdb работает с dlang.
