# Анимации: React Transition Group + React Router

Если нам надо анимировать компоненты из путей `React Router`, удобнее всего поместить их в `<CSSTransition>` для управления `просом-in` каждого маршрута. 

Самым сложным является анимирование выхода, поскольку маршруты меняются незамедлительно. Нам нужен способ сохранения старого маршрута в течение времени перехода. К счастью, `пропс-children` компонента `<Route>` может принимать функцию. В отличие от пропа render, функция из пропа children запускается только при совпадении маршрута. React Router передает объект, содержащий объект match, который существует при совпадении маршрута, в противном случае, он имеет значение null. Это позволяет нам управлять пропом in компонента CSSTransition.

## Обратите внимание: 
При использовании `React Transition Group` с `React Router` старайтесь избегать использования компонента `<Switch>`, поскольку он рендерит только совпавший `<Route>`. Это сделает невозможным реализацию перехода выхода, поскольку существующий маршрут не будет совпадать с текущим URL и колбек `children` не запустится.

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
            {name}
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

[Пример в песочнице](https://reactcommunity.org/react-transition-group/with-react-router)
