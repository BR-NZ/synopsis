# Анимации: React Transition Group + React Router

Если нам надо анимировать компоненты из путей `React Router`, удобнее всего поместить их в `<CSSTransition>` для управления `просом-in` каждого маршрута. 

## Сложно анимировать "выхождение" – поскольку маршруты меняются незамедлительно
Нам нужен способ сохранения старого маршрута в течение времени перехода. 

К счастью, внутри компонента пути `<Route>` можно помещать функцию, которая выполнится только при совпадении маршрута (в отличие от `пропса-render`). 

`React Router` передает в эту функцию объект, содержащий `объект-match` – он будет иметь значение только при совпадении маршрута, а иначе равен `null`. 

А эта особенность позволяет нам управлять `пропсом-in` компонента `<CSSTransition>` внутри пути – ведь если он имеет "правдивое" значение, значит и компонент перехода должен получить `in={true}` и начать переход (и наоборот).

## Обратите внимание: 
При использовании `React Transition Group` с `React Router` старайтесь избегать использования компонента `<Switch>`, поскольку он рендерит только совпавший `<Route>`. Это сделает невозможным реализацию перехода выхода, поскольку существующий маршрут не будет совпадать с текущим URL и колбек-функция внутри пути `<Route>` не запустится.

## Наглядный пример: 
```javascript
const Home = () => ( <h1>Home</h1> )
const About = () => ( <h1>About</h1> )
const Contact = () => ( <h1>Contact</h1> )

const routes = [
  { path: '/', name: 'Home', Component: Home },
  { path: '/about', name: 'About', Component: About },
  { path: '/contact', name: 'Contact', Component: Contact },
]

const App = () => (
  <Router>
    <Navbar>
      <Nav>
        {routes.map(({ path, name }) => (
          <Nav.Link
            key={path}
            as={NavLink}
            to={path}
            activeClassName='active'
            exact
          >
            { name }
          </Nav.Link>
        ))}
      </Nav>
    </Navbar>
    
    <Container className='container'>
      {routes.map(({ path, Component }) => (
        <Route key={path} path={path} exact>
        
          {({ match }) => (
            <CSSTransition
              in={match !== null}
              timeout={300}
              classNames='page'
              unmountOnExit
            >
              <div className='page'>
                <Component />
              </div>
            </CSSTransition>
          )}
          
        </Route>
      ))}
    </Container>
  </Router>
)
```

[В документации пример - дичь необъясненная](https://reactcommunity.org/react-transition-group/with-react-router)
