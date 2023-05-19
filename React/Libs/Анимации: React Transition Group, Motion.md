# Анимации: React Transition Group, Motion

__`React-transition-group`__ - эта библиотека не содержит сами анимации, она лишь помогает удобно реагировать на переходы в React-приложениях, то есть "появления", "исчезновения" частей интерфейса. 

__Функционал библиотеки не дает возможностей__ производить действия над компонентами (монтировать, размонтировать), этот действия мы реализуем сами, он лишь позволяет описать как стиль компонентов должен реагировать на эти события.

## Установка в проект:
```
// добавляем прод-зависимость
npm i react-transition-group

// импортируем в модуль
import { Transition, CSSTransition, SwitchTransition, TransitionGroup } from 'react-transition-group'
```

<br>

## Существует 4 основных состояния `state`:
* entering (процесс вхождения `duration`s)
* entered (вошел)
* exiting (процесс выхода `duration`s)
* exited (вышел)

<img title="Изменение in и состояния" alt="Изменение in и состояния" width="650" src="https://github.com/BR-NZ/synopsis/assets/24506129/11c5e825-10f7-45b0-b871-4015145c4b47">


## Они вызывают 6 событий:
<img title="Изменение in и события" alt="Изменение in и события" width="650" src="https://github.com/BR-NZ/synopsis/assets/24506129/de52d31b-623c-4d53-ab23-1fe316a3e30d">

Когда пропс-`in` переходит:  
1.  Из `true` в `false`, он выдает по порядку 3 события: `onEnter`, `onEntering`, `onEntered`.
2.  Из `false` в `true`, он выдает по порядку 3 события: `onExit`, `onExiting`, `onExited`.
<br>

## Составляющие
Для этих целей у библиотеки есть 4 компонента:

<br>

### `<Transition>` (инлайн-стили перехода)
Позволяет декларативно описать переход от одного состояния компонента к другому с течением времени.

Это базовый компонент, который не использует никаких CSS-классов проекта, вместо этого он задает inline-стили.

Чаще всего он используется для анимации монтажа и демонтажа компонента, но также может использоваться для описания переходных состояний.

Наглядный пример применения:  
```javascript
const duration = 300;

// объект-стилей базовых (не привязанных к состоянию)
const defaultStyle = {
  transition: `opacity ${duration}ms ease-in-out`,
  opacity: 0,
}

// объект-стилей под разные состояния (state)
const transitionStyles = {
  entering: { opacity: 1 },
  entered: { opacity: 1 },
  exiting: { opacity: 0 },
  exited: { opacity: 0 },
};

someComp = (props) {
  const nodeRef = useRef(null);
  return (
    <Transition in={props.in} timeout={duration} nodeRef={nodeRef}>
      {
      state => (
        <div ref={nodeRef} style={{
          ...defaultStyle, // базовые стили будут всегда (на любом state)
          ...transitionStyles[state] // под каждый state добавятся свои стили
        }}>
          I'm a fade Transition!
        </div>
      )
      }
    </Transition>
  )
}
```

<br>

### `<CSSTransition>` (класс перехода)


### `<SwitchTransition>` (вариант перехода)


### `<TransitionGroup>` (обертка)
  

<br>

