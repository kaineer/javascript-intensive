# Графика

## Canvas

### К чему приводит копирование кода

Пример растёт из задания по Kekstagram-му, но иметь совет в виду следует всегда.

Представим себе ситуацию: у нас в коде полно математики: деления, вычитания, умножения и, как только мы получаем какое-то значение в одном месте, мы уже не задумываясь, копируем получившееся выражение из одного места в другое.

#### Как это может выглядеть в коде:

``` javascript
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

А теперь представим себе, что нам понадобилось добавить в этот повторяющийся кусочек `(this.container.width - this._resizer.width) / 2` ещё какую-нибудь величину. Будете редактировать все восемь строчек? И ни разу не ошибётесь? Если да, запишите, пожалуйста, скринкаст, я посмотрю.

#### А если не стрелять себе в ногу

``` javascript
var halfSide = (this.container.width + this._resizer.width) / 2;
var ctx = this._ctx;

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

Давайте нарисуем затемнение.

#### Попытка 1

``` javascript



```