# Графика

## Canvas

### К чему приводит копирование кода

Пример растёт из задания по Kekstagram-му, но иметь совет в виду следует всегда.

Представим себе ситуацию: у нас в коде полно математики: деления, вычитания, умножения и, как только мы получаем какое-то значение в одном месте, мы уже не задумываясь, копируем получившееся выражение из одного места в другое.

#### Как это может выглядеть в коде:

``` javascript
ctx.beginPath();
this._ctx.moveTo(-this.container.width / 2 + this._resizer.width / 2,
                 -this.container.width / 2 + this._resizer.width / 2);
this._ctx.lineTo(this.container.width / 2 - this._resizer.width / 2,
                 -this.container.width / 2 + this._resizer.width / 2);
this._ctx.lineTo(+ this.container.width / 2 - this._resizer.width / 2,
                 + this.container.width / 2 - this._resizer.width / 2);
this._ctx.lineTo(-this.container.width / 2 + this._resizer.width / 2,
                 +this.container.width / 2 - this._resizer.width / 2);
this._ctx.closePath();
```

А теперь представим себе, что нам понадобилось добавить в этот повторяющийся кусочек `this.container.width / 2 - this._resizer.width / 2` ещё какую-нибудь величину. Будете редактировать все восемь строчек? И ни разу не ошибётесь? Если да, запишите, пожалуйста, скринкаст, я посмотрю.

#### А если не стрелять себе в ногу

``` javascript
var halfSide = (this.container.width + this._resizer.width) / 2;
var ctx = this._ctx;

ctx.beginPath();
ctx.moveTo(-halfSide, -halfSide);
ctx.lineTo(halfSide, -halfSide);
ctx.lineTo(halfSide, halfSide);
ctx.lineTo(-halfSide, halfSide);
ctx.closePath();
```

Код становится короче, понятнее и его легче модифицировать.

#### Ссылки

* [Shotgun surgery](https://sourcemaking.com/refactoring/smells/shotgun-surgery)
* [Стрельба дробью](https://refactoring.guru/ru/smells/shotgun-surgery)
* [Расходящиеся модификации](https://refactoring.guru/ru/smells/divergent-change)

По ссылкам – объяснение примерно того же явления, но уже не на уровне одной функции, а на уровне одного или нескольких классов.

### Вебинары полны информации

И, зачастую, в рассмотренных примерах полно полезных кусков кода. Которые **не стоит** копировать целиком, без понимания.

А пока рассмотрим обязательную часть задания `Canvas` из проекта "Кекстаграм". В задании написано:

> Доработайте функцию `redraw` в объекте `Resizer` в файле `js/resizer.js` таким образом, чтобы вокруг жёлтой рамки, рисующей ограничение, рисовался чёрный слой с прозрачностью 80% используя методы холста, который хранится в переменной `this._ctx`.

Давайте нарисуем затемнение, пусть даже без учета ширины линии.

#### Попытка 1

Рисуем четыре прямоугольника: слева от рамки на весь контейнер по высоте, справа от рамки, тоже на весь контейнер и ещё два прямоугольника в оставшиеся места сверху и снизу от рамки.

``` javascript
// Инициализация переменных
var ctx = this._ctx;
var width = this.container.width;
var height = this.container.height;
var resizerSide = this.resizer.side;
// .. и стиля
ctx.fillStyle = "rgb(0, 0, 0, 0.8)";

// Прямоугольник слева
ctx.fillRect(-width / 2, -height / 2, (width - resizerSide) / 2, heigt);
// Прямоугольник справа
ctx.fillRect(resizerSide / 2, -height / 2, (width - resizerSide) / 2, heigt);
// Сверху
ctx.fillRect(-resizerSide / 2, -height / 2, resizerSide, (height - resizerSide) / 2);
// Снизу
ctx.fillRect(resizerSide / 2, resizerSide / 2, resizerSide, (height - resizerSide) / 2);
```

Всё сравнительно просто, понятно, но есть одно неприятное **но**. На границах стыковки прямоугольников видны либо "просветы", либо "наползания".

#### Попытка 2

А теперь давайте сделаем так, как предлагается в вебинаре. (Ну или почти предлагается. Во всяком случае, намёк в демках теперь точно есть).

Нарисуем два контура, один по периметру контейнера, по часовой стрелке, другой по периметру рамки, против часовой стрелки. И выполним `fill("evenodd")`:

``` javascript
// Инициализация переменных и стиля
// .. приведена в предыдущем куске кода

ctx.beginPath();

// Внешний контур
ctx.rect(-width / 2, -height / 2, width, height);

// Внутренний контур
ctx.moveTo(-resizerSide / 2, -resizerSide / 2);
ctx.lineTo(-resizerSide / 2,  resizerSide / 2);
ctx.lineTo( resizerSide / 2,  resizerSide / 2);
ctx.lineTo( resizerSide / 2, -resizerSide / 2);

ctx.closePath();

ctx.fill("evenodd");
```

"Ни единого разрыва!" &copy;

[Живой пример](http://codepen.io/kaineer/pen/jqwzJr?editors=1010) на codepen.io.

### Волшебная кнопка

В этом совете практически не будет кода. Но, возможно, будут ссылки :)

Суть в том, что иногда внутрь функции `_drawMessage` в `Codemagick` попадает слишком большое количество кода, т.е. одна функция выполняет слишком много действий. И текст на строки разбивает и высоту окна подсчитывает и рисует тень с сообщением. Это грозит возрастанием сложности функции (критерий Д4) и разрастанием высоты функции с выползанием за высоту экрана.

Всячески советую разбивать такие функции на несколько.

В книге, описывающей самодокументирующий код прямо так и было написано:

> Хочешь написать комментарий, что делает этот код? Выдели его в метод, чтобы по имени метода было понятно, что этот код делает.

#### Ссылки

* [Настоящая волшебная кнопка](http://button.dekel.ru/)
* [Антипаттерн "Волшебная кнопка"](https://ru.wikipedia.org/wiki/%D0%9C%D0%B0%D0%B3%D0%B8%D1%87%D0%B5%D1%81%D0%BA%D0%B0%D1%8F_%D0%BA%D0%BD%D0%BE%D0%BF%D0%BA%D0%B0)