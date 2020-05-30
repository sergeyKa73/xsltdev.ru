# Оператор delete

Если говорить коротко, то JavaScript _невозможно_ удалить глобальную переменную, функцию или любой другой объект, которому задан атрибут `DontDelete` .

## Глобальный код и код функции

Если переменная или функция определена в глобальной области видимости (scope) или в [области видимости функции](../function/scopes.md), это значит что она является свойством (`property`) глобального или же `Activation` объекта.

Подобные свойства имеют набор атрибутов, одним из которых и является упомянутый ранее `DontDelete`. Объявление переменных и функций в коде функции или глобально всегда создает свойство с атрибутом `DontDelete`, и поэтому не может быть удалено.

```js
// глобальная переменная:
var a = 1 // задается DontDelete
delete a // false
a // 1

// обычная функция:
function f() {} // задается DontDelete
delete f // false
typeof f // "function"

// переназначение не поможет:
f = 1
delete f // false
f // 1
```

## Явные свойства

Явно заданные свойство могут быть удалены обычным способом.

```js
// явно заданные свойства:
var obj = { x: 1 }
obj.y = 2
delete obj.x // true
delete obj.y // true
obj.x // undefined
obj.y // undefined
```

В примере выше, `obj.x` и `obj.y` могут быть удалены потому что у них не задан атрибут `DontDelete`. Именно поэтому следующий пример тоже сработает.

```js
// работает хорошо везде, кроме IE:
var GLOBAL_OBJECT = this
GLOBAL_OBJECT.a = 1
a === GLOBAL_OBJECT.a // true - просто глобальная переменная
delete GLOBAL_OBJECT.a // true
GLOBAL_OBJECT.a // undefined
```

Здесь мы используем небольшой трюк, чтобы удалить `a`. [`this`](../function/this.md) здесь относится к глобальному объекту и мы явно указали удаление переменной `a` как свойства глобального объекта, что и позволи нам ее удалить.

Из-за нескольких багов в IE (как минимум 6-8) предыдущий код работать в нем не будет.

## Аргументы функций и встроенные модули (built-ins)

Обычным аргументам функций [`arguments` objects](../function/arguments.md) и встроенным свойствам также задан атрибут `DontDelete`.

```js
// аргументы функции и свойства:
;(function (x) {
  delete arguments // false
  typeof arguments // "object"

  delete x // false
  x // 1

  function f() {}
  delete f.length // false
  typeof f.length // "number"
})(1)
```

## Host объекты

Host объект - это объект, предоставляемый окружением. К таким объектам относятся `window`, `document`, `location` и так далее.

Для host объектов поведение оператора `delete` может быть непредсказуемым. Согласно спецификации, таким объектам позволено реализовывать любой вид поведения.

## Заключение

Оператор `delete` часто обладает непредсказуемым поведением и безопасно использовать его можно лишь для удаления явно заданных свойств обычных объектов.