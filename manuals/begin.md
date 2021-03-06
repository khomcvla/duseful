# С чего начать

Для самого старта нам потребуется совсем немного ПО.

* [Компилятор](#Компилятор)
    + [DMD](#dmd)
    + [LDC](#ldc)
    + [GDC](#gdc)
* [Система сборки](#Система-сборки)
* [Среда разработки](#Среда-разработки)
* [Первые шаги](#Первые-шаги)

## Компилятор

Любой компилируемый язык программирования для компиляции требует компилятор (как ни странно).
Для D существует 3 полноценных компилятора и несколько экспериментальных, которые
не будем разбирать. Какой из них выбрать решать Вам, каждый имеет некоторые преимущества
и недостатки.

### DMD

Компилятор от разработчиков языка, самые свежие фичи, самые последние исправления.
Поддерживается только intel архитектура (x86, x86_64). Рекомендуется для новичков.
Для каждой поддерживаемой системы существует установочный файл/пакет.

[Скачать](https://dlang.org/download.html)

[Инструкция](./install/)

### LDC

Для frontend (разбор исходного кода) используется оригинальная реализация от DMD,
для backend (кодогенерация, оптимизация) используется
[LLVM](https://ru.wikipedia.org/wiki/Low_Level_Virtual_Machine). Использование LLVM
позволяет получать более оптимизированный код (самые быстрые D-программы компилируются
ldc), а так же осуществлять сборку под различные архитектуры (x86, x86_64, ARM,
PowerPC, MIPS и т.д.). *Обычно* последняя версия отстаёт от DMD на 1-2 месяца.
Распостраняется в виде архива, нужно будет добавить его в `$PATH`.

[Скачать](https://github.com/ldc-developers/ldc/releases)

[Инструкция](./install/)

### GDC

Backend компилятора использует код gnu compiler collection (gcc), frontend транслируется
с D на С++ командой разработчиков gdc. Отсюда следует, что возможны различия в поведении
с DMD и LDC на одинаковом коде (не проверенная информация). Поддерживается только linux.
Архитектуры x86_64 и ARM. Официальный релиз основан на достаточно старом DMD, имеет смысл
собирать из репозитария на github.

[Скачать](https://gdcproject.org/downloads)

[Инструкция](./install/)

## Система сборки

По сути единственным развитым и распостранённым вариантом является dub.

[Скачать](http://code.dlang.org/download)

[Инструкция](./install/)

Так же dub является менеджером пакетов.

## Среда разработки

Разработчики языка, по всей видимости, не очень любят IDE как явление, поэтому
некоторые особенности языка облегчают работу с ним без IDE. Подстветка синтаксиса
есть практически для всех разспостранённых редакторов текста. Но если хочется
чего-то больше блокнота с подсветкой синтаксиса можно попробовать что-то из списка ниже.

### Visual Studio Code

Кроссплатформенная IDE, базирующаяся на [Atom](https://atom.io/). Позволяет
отлаживать, работать с git и много другое.

[Скачать](https://code.visualstudio.com/)

[Code-D (плагин для работы с D)](https://marketplace.visualstudio.com/items?itemName=webfreak.code-d)

Code-d использует внешние программы для разбора исходного кода, автодополнения и
подсветки ошибок, при установке он должен самостоятельно их скачать и собрать
(компилятор уже должен быть доступен в путях).

[Плагин для отладки](https://marketplace.visualstudio.com/items?itemName=webfreak.debug)

### IntelliJ IDEA

### Visual Studio

### DLangIDE

IDE написана на D для D. Придётся собирать самому, но там достаточно просто всё.
(TODO инструкция)

[Страница на github](https://github.com/buggins/dlangide)

### Coedit

IDE написана на Pascal (!).

[Страница на github](https://github.com/BBasile/Coedit)

## Первые шаги

После того, как Вы поставили компилятор уже можно экспериментировать, но лучше будет ещё доставить систему сборки dub.

Создайте файл с расширением `*.d` и запишите туда следующее:

```d
import std.stdio;

void main()
{
    writeln("hello world");
}
```
Сохраните, затем запустите следующие команды из командной строки, находясь рядом с файлом (команда `cd` -- change dir -- позволит Вам перейти в нужную директорию):
    
    dmd <filename>.d
    ./<filename>

Первая строчка компилирует исходный код из Вашего файла в исполняемый файл (вместо `dmd` может быть любой другой из перечисленных компиляторов). Вторая строчка запускает исполняемый файл.

Для того чтобы сделать это за один шаг можно воспользоваться утилитой `rdmd`. Она компилирует файл в временную директорию (Temp, tmp и тд), а уже оттуда сразу запускает его.

    rdmd <filename>.d
    
Если часть кода лежит в дополнительном файле (аля библиотека) рядом с вашим целевым, то необходимо его включить в исходном коде целевого файла и добавить при сборке:

```d
// foo.d
module foo;

int myFunc(int a, int b) { return a * b; }
```
```d
// app.d
import std.stdio;
import foo;

void main()
{
    writeln("myFunc(3,4) = ", myFunc(3,4));
}
```

    dmd app.d foo.d
    ./app
    
либо

    rdmd app.d foo.d
    
Последовательность файлов важна: первым должен быть целевой файл (содержащий функцию `main`).
    
### Первый проект

Если Вы хотите писать что-то больше однофайловых программ Вам потребуется система сборки и менеджер пакетов -- dub.

Создайте папку, перейдите в неё из командной строки и вызовите dub:

    dub init
    
dub будет в интерактивном режиме создавать проект. В самом начале советую изменить только одно значение по умолчанию -- формат файла сборки. Это первый параметр -- введите sdl и нажмите несколько раз enter. Будет создана базовая структура проекта:

```
% tree -a
.
├── dub.sdl
├── .gitignore
└── source
    └── app.d

1 directory, 3 files
```

Для компиляции и запуска программы просто введите `dub`, а только для сборки `dub build`.

Если вы хотите использовать библиотеку из [репозитария dub](code.dlang.org) просто добавьте её в список
зависимостей своего проекта:

0. перейдите на страницу библиотеки, там будет удобное поле для копирования, например `dependency "dlib" version="~>0.12.2"`
0. вставляете эту строчку в конец `dub.sdl`
0. вызываете сборку командой `dub` или `dub build` и dub сам загружает её и подключает к проекту (PROFIT)

Подробная информация по настройке проекта через `dub.sdl` [тут](http://code.dlang.org/package-format?lang=sdl).
