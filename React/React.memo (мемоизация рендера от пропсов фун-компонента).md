# React.memo (мемоизация рендера от пропсов)
_Для фукнциональных компонентов_

<br>

__`React.memo`__ – метод, который получает на вход функциональный компонент, и возвращает его "мемоизированную" версию. Новый компонент сможет избегать лишних рендеров, сравнивая прошлый пропсы с новыми.  

В случае установления равенства пропсов будет использоваться результат последнего рендера, избегая повторного рендеринга.

_Это компонент высшего порядка, то есть метод, возвращающий модифицированный компонент._  

<br>

## В чем проблема:
Часто компонент производит лишние рендеры при казалось бы тех же пропсах. Например, виджет курса валют может периодически получать с сервера все те же данные в виде объекта.  

Такое бывает, когда в пропсы приходит ссылочная сущность (объект или массив), даже при неизменном содержимом, запрашивая по API новые данные, ссылка будет пересоздаваться каждый раз – вызывать лишние рендеры.  

<br>

## Синтаксис:
```
const MyComponent = React.memo( const MyComponent = (props) => {
    // …рендер с использованием пропсов
}, [ func (prevProps, nextProps) {} ] )
```

<br>

## Функция сравнения:
Вторым аргументом этого метода принимается `кастомная функция сравнения пропсов`.

__По умолчанию:__ React.memo будет сравнивать прошлые `props` с новыми по принципу `«поверхностного сравнения»` – то есть вложенные в объекты ссылочные данные не сравниваются (всегда будут считаться разными). 

__Изменить правило сравнения можно:_ передав вторым аргументом свою функцию сравнения, которая `должна возвращать true` если __проспсы считаются равными__.

<br>

## Не рекомендуется:
* Применять для компонентов, которые `часто могут получать разные props`, так как каждое обновление `props` будет проверено внутренним алгоритмом сравнения и замедлит производительность;
* React.memo не рекомендован для мемоизирования классовых компонентов, для этих целей используют `React.PureComponent`.
