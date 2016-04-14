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

if((reviewMark.value < 3 && reviewText.value) && 
  reviewName.value) {
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

#### Как-то так получилось

Что про избавление от лишних `if` я уже рассказал. Поэтому отдельно говорить о нём не буду. И что-то мне подсказывает, что я про это уже говорил.

### Частоповторяемые действия

Например, скрытие-отображение какой-нибудь метки и т.п. Чтобы каждый раз не писать `if`, рекомендую делать функцию `toggleVisibility`.

#### Убираем такое

``` javascript
if(reviewText.validity.valid) {
  reviewLabelText.classList.remove("hidden");
} else {
  reviewLabelText.classList.add("hidden");
}
```

#### Пишем так

``` javascript
toggleVisibility(reviewLabelText, reviewText.validity.valid);
```

Реализацию `toggleVisibility` оставляю для разминки извилин.

### Разница между функцией и вызовом

Предположим, есть функция `handleInputs`. И в коде написано:

``` javascript
reviewName.oninput = handleInputs();
```

Что при этом происходит?

Происходит очень простая вещь, `handleInputs` вызывается в момент присваивания значения `reviewName.oninput` и больше не вызывается никогда.

Как же сделать так, чтобы функция вызывалась?

Можно, конечно, сделать так:

``` javascript
reviewName.oninput = function() { handleInputs(); }
```

здесь мы присваиваем в `oninput` новую функцию, которая вызовет функцию `handleInputs`. Способ работающий, но избыточный.

Избыточный потому, что *новая функция нам не нужна*.

Гораздо проще использовать имя функции, без круглых скобок:

``` javascript
reviewName.oninput = handleInputs;
```

## Печеньки

### RadioButtonGroup

Есть такой замечательный класс. Объект именно этого типа мы получаем, когда запрашиваем группу радиокнопок из формы. И сейчас я покажу, как **не надо** выставлять значение в такой группе:

``` javascript
var elems = filterForm['upload-filter'];
for (var i = 0; i < elems.length; i++) {
  if (elems[i].value === browserCookies.get('lastFilter')) {
    elems[i].checked = true;
    // ...
  }
}
```

Гораздо проще будет сделать так:

``` javascript
var elems = filterForm['upload-filter'];
elems.value = browserCookies.get('lastFilter') || defaultFilter;
```

И, кстати, последняя строка примера напоминает мне про...

### Значения по умолчанию

`Cookies` выставляются уже после того, как мы один раз открывали страницу и выполнили на ней какие-то действия, которые привели к их заполнению. Не нужно исходить из того, что если придёт `undefined` всё всё-равно будет хорошо. Как правило, я это проверяю и прошу исправить.
