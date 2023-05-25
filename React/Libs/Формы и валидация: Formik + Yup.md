# Формы и валидация: Formik + Yup

> __`Formik`__ — самая популярная библиотека для удобной работы с формами в `React` и `React Native`. Предоставляет валидацию, работу с событиями и собственные компоненты для форм с зашитой дополнительной функциональностью, таким образом, сокращает и избавляет от дублирования код.

По сути __библиотека реализована на кастомных хуках__, подобное можно реализовать и самому: данные формы в таком решении хранятся в рамках отдельного вызова функции, то есть в "замыкании".

Другим решением из сторонней библиотеки является хранение данных из формы в отдельном хранилище, например `Redux`. Но оно не слишком удачное, вызывает лишнии обновления и не подходит для средних и более проектов.

Более оптимизированная альтернатива: [`React-hook-form`](https://legacy.react-hook-form.com/ru)

<br>

## Установка
Установка прод-зависимостей:  
```
npm i formik
npm i yup
```

Импортируем компоненты библиотек в модуль, где их будем использовать:  
```javascript
// вытаскиваем готовые компоненты
import { Formik, Form, Field, ErrorMessage } from 'formik'

// если хотим повозиться с хуком
import { useFormik } from 'formik'

// вытаскиваем все типы из Yup
import { object, string, number, date, InferType } from 'yup'

// или кладем все в общий объект
import * as Yup from 'yup'
```

### Действующие лица: 
* __`{ initialValues }`__ — содержит объект с начальными значениями элементов формы. Свойства объекта это `name`-атрибуты элементов формы, а значения свойств это их __значения по умолчанию__;
* __`{ values }`__ — объект по структуре схожий с `initialValues`, его свойства это `name`-атрибуты элементов формы. А в значениях он __содержит текущее значение каждого элемента__;
* __`{ errors }`__ — объект по структуре схожий с `initialValues`, его свойства это `name`-атрибуты элементов формы. А в значениях он __содержит ошибки для каждого элемента__;
* __`{ touched }`__ — объект по структуре схожий с `initialValues`, его свойства это `name`-атрибуты элементов формы. А в значениях он __содержит boolean, говорящий о срабатывании onBlur события__ на элементе. То есть в touched объекте поля помечаются как использованные, это нужно чтобы не валидировать поле до его использования пользователем.
* __`validate( values )`__ — функция валидации значений элементов формы, она будет вызвана для каждого элемента формы на событии `onChange` (при каждом изменении его значения). На вход она получает объект `values` с текущими значениями элементов, где его можно анализировать и предпринимать действия, например, записать в объект `errors` ошибку для того или иного элемента формы. 

_Функцию стоит реализовать если мы не собираемся использовать сторонние инструменты валидации типа `Yup` или хотим ее дополнить._

<br>

## Варианты применения
Поскольку __библиотека реализована на кастомных хуках__, использовать ее можно двумя способами:  
1. __Используя ее через компонент `<Formik>`__ — предпочтительный вариант, так как использует контекст, а значит пробрасывать события элементам будет куда удобнее;
2. __Используя ее хук `useFormik()`__ — более незкоуровневый вариант если хочется "посмотреть под капот" библиотеки, кода и лишних действий будет больше;

<br>

## Готовые компоненты форм
Библиотека предоставляет собственные компоненты, реализующие привычные нам `HTML-элементы` форм: `Form`, `Field`, `ErrorMessage` и пр. Внутри них зашит весь функционал библиотеки, их применение существенно уменьшает дублирование и объем боилер-кода. __Они используют контекст `<Formik>` компонента__, чтобы незаметно получать и использовать внутри себя все события и объекты библиотеки, таким образом, передавать их явно в пропсы не нужно.

### `<Field name="currency" type="text/number/checkbox" as="select/textarea" />`
Представляет наиболее общий компонент библиотеки, который заменяет собой стандартные элементы `input`, `textarea`, `select`, `checkbox`.

### `<ErrorMessage name="email" component="div" />`
Компонент будет отслеживать ошибки элемента по его `name-атрибуту` и выводить их внутри себя, рендерясь в виде тега указанного в `component`.

Можно использовать их стандартные прородители `HTML-элементы`: `form`, `input`, `textarea`, `select` и пр. Но в этом случае, стандартные элементы будет необходимо конфигурировать: задавать им обработчики для каждого нужного события и так далее (так как они не могут использовать контекст).

<br>

## 1. Используя компонент `<Formik>`
> __`<Formik>`__ — главный контролирующий компонент, является прямым аналогом хука `useFormik()`, выполненным в компонентной версии. Все что хук получал из объект-конфигуратора, компонент получает из пропсов при вызове. В своей реализации он и использует этот хук, конфигурируя его из полученных пропсов.

Внутри тэгов `<Formik>` может содержать `<form>`/`<Form>` и ее дочерние компоненты (если не нужно выдергивать значений из контекста) или возвращать их в рендер-функции (если значения из контекста нужны, их можно получить в аргументе функции).

### Пример `<Formik>` и "готовых" компонентов форм
Лучше всего понять принцип можно на общем примере:  
```javascript
const App = () => (
   <Formik
      // объект с начальными значениями элементов формы (связь по атрибуту name)
      initialValues={{ email: '', password: '' }}

      // функция-валидатор вызывается при каждом срабатывании события "onChange"
      // получает текущее значение элемента
      validate={values => {
                  const errors = {};
                  if (!values.email) {
                       errors.email = 'Поле обязательно к заполнению';
                  } else if (
                       !/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i.test(values.email)
                  ) {
                       errors.email = 'Некорректный адрес почты';
                  }
                  return errors;
      }}

      // вешаем колбек на события "submit" формы
      onSubmit={(values, { setSubmitting }) => {
                   setTimeout(() => {
                       alert(JSON.stringify(values, null, 2));
                       setSubmitting(false);
                   }, 400);
      }}
  >
      // рендер-функция конфигурирует и отображает обычную форму
      // получает много полезных методов и значений
      {({ isSubmitting }) => (
                   <Form>
                       // используем кастомные элементы библиотеки
                       <Field type="email" name="email" />
                       <ErrorMessage name="email" component="div" />
                       <Field type="password" name="password" />
                       <ErrorMessage name="password" component="div" />
                       <button type="submit" disabled={isSubmitting}>
                           Отправить
                       </button>
                   </Form>
      )} 
  </Formik>
);
```

### Пример `<Formik>` и "стандартных" элементов
Легко понять их преимущества сравнив прошлый участок кода с данным:  
```javascript
const App = () => (
<Formik
   initialValues={{ email: '', password: '' }}
   validate={values => {
               const errors = {};
               if (!values.email) {
                    errors.email = 'Поле обязательно к заполнению';
               } else if (
                    !/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i.test(values.email)
               ) {
                    errors.email = 'Некорректный адрес почты';
               }
               return errors;
   }}
   onSubmit={(values, { setSubmitting }) => {
                setTimeout(() => {
                    alert(JSON.stringify(values, null, 2));
                    setSubmitting(false);
                }, 400);
   }}
>
   // чтобы прокинуть все свойства и события стандартным элементам
   // мы вынуждены брать из объекта-аргумента больше функционала
   {({ values, errors, touched, handleChange, handleBlur, handleSubmit, isSubmitting }) => (
                <Form>
                    // так выглядит дублирование атрибутов каждому элементу
                    <input type="name" name="name" onChange={handleChange} onBlur={handleBlur} value={values.name} />
                    // а так вывод ошибок
                    { errors.name && touched.name %% errors.name }
                    <input type="email" name="email" onChange={handleChange} onBlur={handleBlur} value={values.email} />
                    { errors.email && touched.email && errors.email }
                    <button type="submit" disabled={isSubmitting}>
                        Отправить
                    </button>
                </Form>
   )}
   
  </Formik>
);
```

<br>

## 2. Используя хук `useFormik()`
> __`useFormik()`__ — главный хук библиотеки, который и использует под капотом сам компонент `<Formik>`, получает на вход объект-конфигуратор с спец-объектами и обработчиками событий библиотеки. Вызов хука возвращает объект с другими спец-объектами и обработчиками событий, которые уже можно применять в рендере самой формы.

### Пример `useFormik()` и "стандартных" элементов
Лучше всего понять принцип можно на общем примере:  
```javascript
const validate = values => { /* реализация уже приводилась выше */ }

const Form = () => {
    // хук получает на вход объект-конфигуратор
    const formik = useFormik({
        initialValues: {
            name: '',
            email: '',
            amount: 0,
            currency: 'USD',
            text: '',
            terms: false
        },
        validate,
        onSubmit: values => console.log(JSON.stringify(values, null, 2))
    });
    // а вернет объект со своими событиями и текущими значениями для элементов

    return (
        <form className="form" onSubmit={formik.handleSubmit}>
            <label htmlFor="name">Ваше имя</label>
            <input
                id="name"
                name="name"
                type="text"
                // значение каждого элемента отслеживается по объекту values
                // так реализуется двустороннее связывание у контролируемых элементов
                value={formik.values.name}
                // при изменении value элемента будет вызвана функция-обработчик handleChange
                // она будет смотреть с какими name изменился элемент и менять его значение в объекте values
                onChange={formik.handleChange}
                // onBlur сработает при потере элементом фокуса - и специальный объект touched по name ключу присвоит true
                onBlur={formik.handleBlur}
            />
            { formik.errors.name && formik.touched.name ? <div>{ formik.errors.name }</div> : null }
            
            <label htmlFor="currency">Валюта</label>
            <select
                // селект будет получать value из такого же атрибута у выбранного option
                id="currency"
                name="currency"
                value={formik.values.currency}
                onChange={formik.handleChange}
                onBlur={formik.handleBlur}>
                    <option value="">Выберите валюту</option>
                    <option value="USD">USD</option>
                    <option value="UAH">UAH</option>
                    <option value="RUB">RUB</option>
            </select>
            
            <label htmlFor="text">Ваше сообщение</label>
            <textarea 
                id="text"
                name="text" 
                value={formik.values.text}
                onChange={formik.handleChange}
                onBlur={formik.handleBlur}
            />
            
            <label className="checkbox">
                <input name="terms" type="checkbox" />
                Соглашаетесь с политикой конфиденциальности?
            </label>
            
            <button type="submit">Отправить</button>
        </form>
    )
}
```
Как видно, такой вариант применения библиотеки создает дублирование кода у усложняет понимание.

<br>

## Валидация с помощью `Yup`
> __`Yup`__ — библиотека для удобной валидации данных, может применяться отдельно от `Formik` или `React`. Проще говоря, это построитель `схемы` для анализа и проверки значений во время выполнения. Схема это тот шаблон, по которому данные будут валидированы, она поддерживает многоуровневую проверку и преобразование значений.

`Formik` также поддерживает получение и валидацию данных на основе пользовательских схем, для этого в ее компонент передается пропс `validationSchema` со специальным `yup-объектом` (для хука в есть одноименное свойство объекта конфигурации).

Валидацию через самописную функцию `validate` можно совместить с валидацией схемой, если нужна какая-либо ее кастомная логика — все они будут изменять объект `{errors}`. 

`Yup-схема` представляет собой объект, где свойства это `name-атрибуты` элементов формы, а значения состоят из цепочки методов-проверок: 
1. На объекте `Yup` вызываем метод, который определяет __тип данных элемента формы__: `string()`, `number()`, `boolean()` и тд;
2. Далее по `chaining-принципу` на выбранном типе данных можем вешать различные методы проверок и преобразований;

Методы могут быть следующих типов: 
*  `string`, `number`, `date` — специализированные методы под каждый тип данных;
*  `mixed` — подходят для всех типов данных;
*  То есть вызывая `string()`, на него можно навешать методы из списка `string` или `mixed` (но не `number`);

### Пример создания схемы:
```javascript
import * as Yup from 'yup';

// создаем схему проверки условного объекта
// объект {values} - и будет в Formik этим объектом, где будут данные полей формы, которые стоит проверить
let userSchema = Yup.object({
   id: Yup.string().default('1').required(),
   names: yup.object({
    first: yup.string().default('Alex').min(2, 'Минимум 2 символа!').required(),
   }),
   email: Yup.string().email('Некорректный е-маил!').required('Обязательное поле!'),
});

// теперь на любом объекте-схеме можно вызвать методы:
// default() вернет объект со значениями указанными по умолчанию
userSchema.default() // -> {id: '1', names: {first: 'Alex'}}

// isValid( ..передав ему проверяемые данные )
// он будет проверен на соответствии правилам из схемы
userSchema.isValid( {id: 1} ) // -> false! names.first is required
```

### Пример `useFormik()` с "стандартных" элементов + `Yup-валидацией`:
```javascript
const Form = () => {
    const formik = useFormik({
        initialValues: {
            email: '',
            currency: 'USD',
            text: '',
            terms: false
        },
        // для валидации схемой "Formik" ожидает свойство "validationSchema" (по аналогии с validate)
        // как раз тут за дело берется Yup с проверками и преобразованиями по цепочкам
        validationSchema: Yup.object({
            email: Yup.string().email('Некорректный е-маил!').required('Обязательное поле!'),
            currency: Yup.string().required('Выберите валюту!'),
            text: Yup.string().min(10, 'Не менее 10 символов'),
            terms: Yup.boolean().required('Необходимо согласие!').oneOf([true], 'Необходимо согласие!'),
        }),
        onSubmit: values => console.log(JSON.stringify(values, null, 2))
    });

    return (
        // рендер при валидации схемой никак не отличается от обычного
        <form className="form" onSubmit={formik.handleSubmit}>
            <h2>Отправить пожертвование</h2>
            <label htmlFor="email">Ваша почта</label>
            <input
                id="email"
                name="email"
                type="email"
                value={formik.values.email}
                onChange={formik.handleChange}
                onBlur={formik.handleBlur}
            />
            { formik.errors.email && formik.touched.email ? <div className="error">{ formik.errors.email }</div> : null }
            <label htmlFor="currency">Валюта</label>
            <select // селект просто получает value из value выбранного option
                id="currency"
                name="currency"
                value={formik.values.currency}
                onChange={formik.handleChange}
                onBlur={formik.handleBlur}>
                    <option value="">Выберите валюту</option>
                    <option value="USD">USD</option>
                    <option value="UAH">UAH</option>
                    <option value="RUB">RUB</option>
            </select>
            { formik.errors.currency && formik.touched.currency ? <div className="error">{ formik.errors.currency }</div> : null }
            <label htmlFor="text">Ваше сообщение</label>
            <textarea 
                id="text"
                name="text" 
                // позволяет сократить повторяющийся код установки событий и значений из библиотеки
                { ...formik.getFieldProps('text') }
            />
            { formik.errors.text && formik.touched.text ? <div className="error">{ formik.errors.text }</div> : null }
            <label className="checkbox">
                <input 
                    name="terms" 
                    type="checkbox" 
                    value={formik.values.terms}
                    onChange={formik.handleChange}
                    onBlur={formik.handleBlur}
                />
                Соглашаетесь с политикой конфиденциальности?
            </label>
            { formik.errors.terms && formik.touched.terms ? <div className="error">{ formik.errors.terms }</div> : null }
            <button type="submit">Отправить</button>
        </form>
    )
}
```
### Пример `<Formik>` с готовыми компонентами + `Yup-валидацией`:
```javascript
const CustomForm = () => {
    return (
        <Formik 
            initialValues={{
                email: '',
                amount: 0,
                currency: 'USD',
                text: '',
                terms: false
            }}
            validationSchema={Yup.object({
                email: Yup.string().email('Некорректный е-маил!').required('Обязательное поле!'),
                amount: Yup.number().min(5, 'Не менее 5').required('Обязательное поле!'),
                currency: Yup.string().required('Выберите валюту!'),
                text: Yup.string().min(10, 'Не менее 10 символов'),
                terms: Yup.boolean().required('Необходимо согласие!').oneOf([true], 'Необходимо согласие!'),
            })}
            onSubmit={ values => console.log(JSON.stringify(values, null, 2)) }
            >
            <Form className="form">
                <h2>Отправить пожертвование</h2>
                <label htmlFor="email">Ваша почта</label>
                <Field
                    id="email"
                    name="email"
                    type="email"
                />
                <ErrorMessage name="email" className="error" component="div" />
                <label htmlFor="amount">Количество</label>
                <Field
                    id="amount"
                    name="amount"
                    type="number"
                />
                <ErrorMessage name="amount" className="error" component="div" />
                <label htmlFor="currency">Валюта</label>
                <Field
                    // селект будет получать value из такого же атрибута у выбранного option
                    id="currency"
                    name="currency"
                    as="select">
                        <option value="">Выберите валюту</option>
                        <option value="USD">USD</option>
                        <option value="UAH">UAH</option>
                        <option value="RUB">RUB</option>
                </Field>
                <ErrorMessage name="currency" className="error" component="div" />
                <label htmlFor="text">Ваше сообщение</label>
                <Field 
                    id="text"
                    name="text"
                    as="textarea"
                />
                <ErrorMessage name="text" className="error" component="div" />
                <label className="checkbox">
                    <Field 
                        name="terms" 
                        type="checkbox" 
                    />
                    Соглашаетесь с политикой конфиденциальности?
                </label>
                <ErrorMessage name="terms" className="error" component="div" />
                <button type="submit">Отправить</button>
            </Form>
        </Formik>
    )
}
```
