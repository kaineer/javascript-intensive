# Знакомство с DOM

## Патч и генеральная уборка

### Замыкания больше не нужны

Особенности работы `webpack` приводят к тому, что замыкания, объявленные на верхнем уровне вашего кода становятся не нужны.

#### Как было до использования webpack

``` javascript
// build/js/resizer.js
(function() {
  var resizer = ...; // Прячем переменную от других модулей
  // ...
})();
```

#### Как должно быть теперь

``` javascript
// src/resizer.js
var resizer = ...; // Всё-равно никто ничего не увидит

// чтобы кто-нибудь увидел нашу переменную 
//   надо сделать например так:
module.exports = {
  resizer: resizer;
};
```

#### Ссылки

* [Тут можно посмотреть, что делает webpack c модулями](http://learn.javascript.ru/screencast/webpack)

## Валидация

### VALIDITY.VALID

Первое, о чём хотелось бы сказать, это выставление свойств инпутов, т.е. не стесняемся принимать помощь от броузера, она там специально для этого.

#### Что я имею в виду

``` javascript
var reviewForm = document.querySelector(".review-form");
var reviewMark = form.elements["review-mark"];
var reviewName = form.elements["review-name"];
var reviewText = form.elements["review-text"];
var submit = form.querySelector(".review-submit");

if((reviewMark.value < 3 && reviewText.value) && reviewName.value) {
  submit.disabled = false;
} else {
  submit.disabled = true;
}
```

Что здесь не так? А не так здесь то, что совершенно не используется свойство инпутов `validity`.

#### Добавим использование validity.valid

``` javascript
// Инициализация переменных не меняется

reviewText.required = reviewMark.value < 3;
if(reviewText.validity.valid && reviewName.validity.valid) {
  submit.disabled = false;
} else {
  submit.disabled = true;
}
```

#### IF не нужен

... во всяком случае, здесь.

``` javascript
// Инициализация переменных не меняется

reviewText.required = reviewMark.value < 3;
submit.disabled = 
  !(reviewText.validity.valid && reviewName.validity.valid);
```
