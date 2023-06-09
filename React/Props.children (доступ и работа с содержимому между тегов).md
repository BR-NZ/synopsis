# Props.children (доступ и работа с содержимому между тегов)

Это все что мы помещаем между тегов компонента при его вызове в родителе. 

Эти данные называют __`«непрозрачная структура данных»`__ - потому что мы не знаем заранее какие данные получим: компоненты, массивы, функции и пр..

`props.children` – изначально __имеет структуру массива__, то есть все дочерние элементы компонента помещаются в массив.

## Получение children

Допустим где-то наш компонент вызвали и поместили внутри него данные:  
```javascript
const App = () => {
  return (
    <SomeComp> ...Какие-то данные </SomeComp>
  )
}
```

Внутри компонента с этими данными можно работать:  
```javascript
// работать как с обычным пропсом

const SomeComp = ({ children }) => {
  return (
    <div>{ ...children }</div>
  )
}
```

<br>

## Обработка children

Когда этих данных множество, для их преобразования можно использовать несколько статичных методов, доступных в `React.Children`:

* `React.Children.count` – просто возвращает количество детей у компонента.
* `React.Children.map(props.children, func(item, i))` – создаёт новый массив с результатом вызова указанной функции для каждого элемента массива.
* `React.Children.forEach(props.children, func(item, i))` – выполняет указанную функцию один раз для каждого элемента в массиве, но не возвращает массив.
* `React.cloneElement(elem, {…props, key, ref}, childrens)` – возвращает новый React-элемент, клонируя переданный. Объект-пропс должен содержать все новые пропсы, key, а также ref. Полученный элемент будет иметь пропсы исходного элемента и  дополнены (заменены) новыми пропсами. Новые дочерние элементы заменят прошлые. Key и ref из исходного элемента будут сохранены, если в config не было передано другие.
* `React.Children.toArray(props.children)` – ковертирует `children` в массив.
