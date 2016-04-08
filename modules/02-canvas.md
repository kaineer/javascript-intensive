# Графика

## Canvas

### Shotgun surgery

Подробнее об этих граблях написано здесь:
https://sourcemaking.com/refactoring/smells/shotgun-surgery

Пример растёт из задания по Kekstagram-му, но иметь совет в виду следует всегда.

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