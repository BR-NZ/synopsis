# Основные принципы работы Redux

<img title="Redux" alt="Redux" width="300" src="https://github.com/BR-NZ/synopsis/assets/24506129/e4970a1a-2c43-4f4b-9244-2349a9fd9488">
<br>
<br>

__`Redux`__ — менеджер для управления состоянием данных и пользовательским интерфейсом в приложениях с большим количеством сущностей.

* `Redux` делает состояние глобальным для всего приложения;
* Состояние изолировано от прямых изменений в специальной сущности "хранилище";
* Оно доступно только для чтения, а все изменения возможны только по "заявке" к функции `reducer`.

<br>

## Reduce + Flux = Redux
Название «Редакс» составлено из двух слов: `reduce` и `flux`:
* `Reduce` – функция, которая приводит большую структуру данных к одному значению;
* `Flux` – архитектура приложения, при которой данные передаются в одну сторону;

Инструмент основан на этих двух понятиях, поэтому они вынесены в название.

<br>

## Установка
Добавляем две библиотеки как прод-зависимости: 
* `redux` – сам редакс со всеми полезными сущностями;
* `react-redux` – библиотека прослойка для удобной работы в react;

```
npm i redux
npm i react-redux
```

После этого нужно создать в директории проекта отдельную папку для хранения файлов, связанных с Redux. Внутри нее можно размещать файлы для хранения источника состояний, файлы с кодом редукторов и другие. Структура должна быть понятной. 

<br>

## Использование
Наиболее простой пример использования приведем ниже:  
```javascript
import { createStore } from 'redux'

// 1. Главная функция, которая будет изменять состояние на основе полученного "действия"
const reducer = (state, action) => { /* ...реализуем свою логику */ }

// 2. Создаем объект хранилища
const store = createStore( reducer )

// 3. Вытаскиваем из него полезные методы, с которыми будем работать
const { dispatch, subscribe, getState } = store

// 4. dispatch - будет передавать действие в reducer
const onClick = () => dispatch({type: "INCREMENT"})

// 5. getState - получает текущее значение в state
<span>Текущее значение: { getState() }</span>

// 6. subscribe - выполняет полученный колбек при изменении state
subscribe( () => console.log( getState() ) )
```

Но, как правило, в реальных проектах actions и reducer разбиваются на отдельные модули.

* Redux обычно используется в связке с фреймворками: React, Vue, Angular. Реже для проектов на чистом JS;
* Чаще всего в рамках приложения одно глобальное состояние, но технически количество не ограничено;
* Имеет открытый исходный код и доступен бесплатно. Со всеми зависимостями __весит всего около 2 Кб__;

<br>

## Для чего нужен
* Для управления состоянием приложения, работающего с большим количеством данных;
* Более легкого масштабирования приложения, его преобразования под разные задачи;
* Избавления от ошибок, связанных с беспорядком в объекте состояния;
* Предсказуемости и понятности работы приложения.

<br>

## Как устроено Redux-приложение
Управление состоянием с помощью Redux можно разделить на три компонента:

1. Источник состояния;
2. Интерфейс, созданный на основе актуального состояния;
3. Функции-действия.

<br>
<img title="Схема работы Redux" alt="Схема работы Redux" width="600" src="https://github.com/BR-NZ/synopsis/assets/24506129/32d99b53-fd66-4fcb-a62c-d64fadf347de">
<br>
<br>

Источник состояния существует в определенном виде. На его основе строится интерфейс приложения. Когда происходят изменения, в источник состояния поступает действие и редукторы перезаписывают состояние. Источник состояния изменяется.

Затем он отправляет новые данные всем компонентам, которые настроены их получать. Так реализована зависимость интерфейса от источника. Компоненты получают информацию и, если нужно, перерисовывают интерфейс в соответствии с ней.

Таким образом за состоянием изменяется интерфейс, так как он зависит от источника. Приложение становится реактивным, то есть мгновенно реагирующим на изменения. Количество ошибок уменьшается, а поведение программы становится более предсказуемым.

<br>

## Как это работает
Принцип работы с состояниями в `Redux` хорошо отражен на анимированной схеме:

<img title="Анимация работы Redux" alt="Анимация работы Redux" width="600" src="https://redux.js.org/assets/images/ReduxDataFlowDiagram-49fa8c3968371d9ef6f2a1486bd40a26.gif">

1. Допустим в `компоненте` (View, UI) произошло событие, которому нужно как-то изменить наше состояние, оно не сможет сделать это напрямую, только вызвав специальный метод-диспетчер `dispatcher`;  
2. Метод `dispatcher` всегда принимает объект события `action` (действия), внутри него указывается как именно нам нужно изменить состояние;  
  * `action` имеет одно обязательное свойство `type` – туда принято записывать строку в верхнем регистре с названием действия;  
  * `action` также имеет одно необязательное свойство `payload` – обычно, оно содержит аргументы для обработчиков действий внутри `reducer()`;  
  * `action` может иметь и другие свойства, которые могут понадобится в `reducer()`.
3. Вызов `dispatcher()` из компонента на самом деле вызывает метод `reducer()` который получает на вход:  
  * `action` – тот же, что передавался диспетчеру;  
  * `state` – текущее состояние приложения;  
4. `компонент` (View, UI) подписан на изменения состояния, поэтому при его обновлении получает актуальные данные.

<br>

## Базовые принципы

### Единый источник состояния
Все данные о состоянии приложения хранятся в одном месте, без копий. Глобальное состояние организовано как дерево объектов и называется `state tree`. Также используются термины «источник состояния» и «хранилище». Оба означают местонахождение глобального состояния. Единый источник нужен для централизации и отладки приложения. Работа становится проще, если данные находятся в одном месте.

### Доступ к состоянию — только для чтения
Глобальное состояние заблокировано для записи и изменения. Компоненты приложения могут читать из него, но не переписывать по своему желанию. Это предотвращает непредсказуемые изменения. Ситуаций, когда объект изменился, а функции об этом не узнали, не бывает.

### Изменение состояния – только через `reducer()`
__`reducer()`__ выполняется всякий раз, когда поступает новое действие, только он может "изменять" состояние, а точнее заменять его на новое.

Это `чистая функция`, а значит:
* Ее результат зависит только от входных параметров;
* При одинаковых параметрах, ее результат должен быть постоянен;
* Она не может выполнять побочные эффекты внутри;

### Состояние `иммутабельно`
Само состояние изменять напрямую нельзя, это приведет к множеству багов и поломает логику работы `react` и `redux` - ведь для вычисления изменения состояния и его минификации им одновременно нужны "старая" и "текущяя" версия.

### Результат работы редуктора
Это новый объект состояния с актуальными данными, рассчитанными на основе информации из объекта-действия. Он попадает в дерево состояний вместо старого.

<br>

## Подробнее:
* __[Отличная статья](https://blog.skillfactory.ru/glossary/redux/)__
