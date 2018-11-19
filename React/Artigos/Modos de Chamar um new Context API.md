# Modos de Chamar um new Context API

Context API já [existia há muito tempo](https://reactjs.org/docs/legacy-context.html#how-to-use-context) nativamente do React, mas após a [release 16.3](https://github.com/facebook/react/blob/master/CHANGELOG.md#1630-march-29-2018) do React ele se tornou mais presente nos projetos. Com uma nova forma de compartilhar os estados, ele deu uma descentralizada no [Redux](https://redux.js.org/), que agora irá ficar com os estados mais complexos para se organizar.

O [Context API](https://reactjs.org/docs/context.html), além de tirar um pouco a centralidade do Redux, se é utilizado para reduzir a passagem de valores via props. Com isso a uma otimização de código e uma melhorar de desempenho no App. Pois havia muitos componentes que recebiam valores de um componente, por exemplo, a cinco níveis acima, modificado uma "árvore" inteira de componentes até chegar no que queriamos.

Com essa nova proposta de new Context API trazida pela a equipe do React, se há algumas formas de chamar ele. Nesse artigo será demonstrado algumas formas de "chamamento" dele. Pode haver outras formas, mas no momento só tenho o conhecimento das que vão seguir o artigo.

## 1. Modo Children (Padrão)

Esse modo é o mais normal de se encontrar nos projetos, é a forma na qual o [próprio site do React](https://reactjs.org/docs/context.html#updating-context-from-a-nested-component) ensina a chamar.

```js
import React from "react";

export const ThemeContext = React.createContext();

export class ThemeProvider extends React.Component {
  state = {
    theme: "dark"
  };

  onChangeTheme = theme => {
    this.setState({
      theme
    });
  };

  render() {
    return (
      <ThemeContext.Provider
        value={{
          ...this.state,
          actions: {
            onChangeThemeDark: () => this.onChangeTheme("dark"),
            onChangeThemeLight: () => this.onChangeTheme("light")
          }
        }}
      >
        {this.props.children}
      </ThemeContext.Provider>
    );
  }
}
```

_context.jsx_

No script acima, se é criado o Context e após a criação é criado uma class, que onde se é definido o Provider e o value do Context.

Logo após, é necessário chamar essa class antes de quando iremos utilizar o Context. Para a certeza que está sendo chamado, chamo a class no _index.js_ do projeto.

```js
import React from "react";
import ReactDOM from "react-dom";

import { ThemeProvider } from "./context";
import App from "./App";
import "./style.css";

ReactDOM.render(
  <ThemeProvider>
    <App />
  </ThemeProvider>,
  document.getElementById("root")
);
```

_index.js_

Após isso, podemos utilizar no nosso projeto.
Nesse exemplo, uso a página inicial do [CRA(create-react-app)](https://github.com/facebook/create-react-app) para mudar o fundo de branco para preto. Por isso, tenho o arquivo de style(feito com [styled-component](https://github.com/styled-components/styled-components)):

```js
import styled from "styled-components";

const AppWrapper = styled.div`
  .App {
    text-align: center;
  }

  .App-logo {
    height: 40vmin;
  }

  .animation-left {
    animation: App-logo-spin-left infinite 20s linear;
  }

  .animation-right {
    animation: App-logo-spin-right infinite 20s linear;
  }

  .App-header-dark,
  .App-header-light {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    min-height: 100vh;
    font-size: calc(10px + 2vmin);
  }

  .App-header-dark {
    background-color: #222;
    color: white;
  }

  .App-header-light {
    background-color: #fff;
    color: #222;
  }

  .App-link {
    color: #61dafb;
  }

  .button-change-light,
  .button-change-dark {
    font-size: larger;
    padding: 3px 12px;
    cursor: pointer;
    -webkit-touch-callout: none;
    -webkit-user-select: none;
    -khtml-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
  }

  .button-change-light {
    background: #fff;
    color: #222;
  }

  .button-change-dark {
    background: #222;
    color: #fff;
  }

  .input-light,
  .input-dark {
    font-size: larger;
    border: 0;
    border-bottom: 1px solid #61dafb;
    outline: 0;
  }

  .input-light {
    background: #fff;
    color: #222;
  }

  .input-dark {
    background: #222;
    color: #fff;
  }

  @keyframes App-logo-spin-left {
    from {
      transform: rotate(0deg);
    }
    to {
      transform: rotate(360deg);
    }
  }

  @keyframes App-logo-spin-right {
    from {
      transform: rotate(0deg);
    }
    to {
      transform: rotate(-360deg);
    }
  }
`;

export default AppWrapper;
```

_AppStyle.js_

No arquivo _App.jsx_ se é utilizado o Modo Children, para chamar o Consumer do Context para utilizar o estado e os actions que definimos anteriormente.

```js
import React from "react";
import { Link } from "react-router-dom";

import AppWrapper from "./AppStyle";
import { ThemeContext } from "./context";
import logo from "./logo.svg";

const App = () => (
  <AppWrapper>
    <ThemeContext.Consumer>
      {context => {
        const {
          theme,
          actions: { onChangeThemeDark, onChangeThemeLight }
        } = context;

        return (
          <div className="App">
            <header
              className={
                theme === "light" ? "App-header-light" : "App-header-dark"
              }
            >
              <img src={logo} className="App-logo" alt="logo" />
              <div style={{ display: "flex", paddingTop: "10vh" }}>
                <div
                  onClick={() => onChangeThemeDark()}
                  className="button-change-dark"
                >
                  dark
                </div>
                <div
                  onClick={() => onChangeThemeLight()}
                  className="button-change-light"
                >
                  light
                </div>
              </div>
            </header>
          </div>
        );
      }}
    </ThemeContext.Consumer>
  </AppWrapper>
);

export default App;
```

_App.jsx_

Nesse modo, se é aberto uma tag e após a sua abertura há uma declaração de um atributo, que se é utlizado para pegar tudo o que foi declarado no value do Provider. Após isso, podemos distribuir como queremos no escopo do arquivo.

É bom esse metódo, mas com a declaração de tags todas as vezes, se há uma criação de código repititivo para utilização do Context.

## 2. Modo Class

Nesse modo chamamos o Context que criamos e utilizamos uma nova alteração liberada na [versão 16.6.0](https://github.com/facebook/react/blob/master/CHANGELOG.md#1660-october-23-2018), na qual se é utilizada o _context.type_.

No mesmo molde do exemplo que estamos utilizando:

```js
import React from "react";
import { Link } from "react-router-dom";

import AppWrapper from "./AppStyle";
import { ThemeContext } from "./context";
import logo from "./logo.svg";

class App extends React.Component {
  static contextType = ThemeContext;

  render() {
    const {
      theme,
      actions: { onChangeThemeDark, onChangeThemeLight }
    } = this.context;

    return (
      <AppWrapper>
        <div className="App">
          <header
            className={
              theme === "light" ? "App-header-light" : "App-header-dark"
            }
          >
            <img src={logo} className="App-logo" alt="logo" />
            <div style={{ display: "flex", paddingTop: "10vh" }}>
              <div
                onClick={() => onChangeThemeDark()}
                className="button-change-dark"
              >
                dark
              </div>
              <div
                onClick={() => onChangeThemeLight()}
                className="button-change-light"
              >
                light
              </div>
            </div>
          </header>
        </div>
      </AppWrapper>
    );
  }
}

export default App;
```

_App.jsx_

Que também pode ser chamado dessa maneira:

```js
import React from "react";
import { Link } from "react-router-dom";

import AppWrapper from "./AppStyle";
import { ThemeContext } from "./context";
import logo from "./logo.svg";

class App extends React.Component {
  render() {
    const {
      theme,
      actions: { onChangeThemeDark, onChangeThemeLight }
    } = this.context;

    return (
      <AppWrapper>
        <div className="App">
          <header
            className={
              theme === "light" ? "App-header-light" : "App-header-dark"
            }
          >
            <img src={logo} className="App-logo" alt="logo" />
            <div style={{ display: "flex", paddingTop: "10vh" }}>
              <div
                onClick={() => onChangeThemeDark()}
                className="button-change-dark"
              >
                dark
              </div>
              <div
                onClick={() => onChangeThemeLight()}
                className="button-change-light"
              >
                light
              </div>
            </div>
          </header>
        </div>
      </AppWrapper>
    );
  }
}

App.contextType = ThemeContext;

export default App;
```

_App.jsx_

Nesse modo podemos utilizar o Context sem chamar uma tag para alimentar nosso arquivo, pegamos o Context que criamos no _context.jsx_ e jogamos no _contextType_. O _contextType_ funciona como um abstrador de context, que pega o value do Provider que criamos.

As duas maneiras que criamos são iguais, o que muda é que em uma nos definimos no começo da escrita da class e na outra difinimos após a escrita da class.

# 3. Modo Context como Connect

Nesse modo iremos fazer uma chamada de Context como se fosse [um connect do Redux](https://blog.logrocket.com/react-redux-connect-when-and-how-to-use-it-f2a1edab2013). Para isso vamos definir uma function que irá receber nossa const/class.

Como está estruturado nosso projeto, iremos aterá-lo:

```js
import React from "react";

const ThemeContext = React.createContext();

export class ThemeProvider extends React.Component {
  state = {
    theme: "dark"
  };

  onChangeTheme = theme => {
    this.setState({
      theme
    });
  };

  render() {
    return (
      <ThemeContext.Provider
        value={{
          ...this.state,
          actions: {
            onChangeThemeDark: () => this.onChangeTheme("dark"),
            onChangeThemeLight: () => this.onChangeTheme("light")
          }
        }}
      >
        {this.props.children}
      </ThemeContext.Provider>
    );
  }
}

export function ThemeConsumer(Component) {
  return function Wrapper(props) {
    return (
      <ThemeContext.Consumer>
        {context => <Component {...props} {...context} />}
      </ThemeContext.Consumer>
    );
  };
}
```

_context.jsx_

_App.jsx_ com class:

```js
import React from "react";
import { Link } from "react-router-dom";

import AppWrapper from "./AppStyle";
import { ThemeConsumer } from "./context";
import logo from "./logo.svg";

class App extends React.Component {
  render() {
    const {
      theme,
      actions: { onChangeThemeDark, onChangeThemeLight }
    } = this.props;

    return (
      <AppWrapper>
        <div className="App">
          <header
            className={
              theme === "light" ? "App-header-light" : "App-header-dark"
            }
          >
            <img src={logo} className="App-logo" alt="logo" />
            <div style={{ display: "flex", paddingTop: "10vh" }}>
              <div
                onClick={() => onChangeThemeDark()}
                className="button-change-dark"
              >
                dark
              </div>
              <div
                onClick={() => onChangeThemeLight()}
                className="button-change-light"
              >
                light
              </div>
            </div>
          </header>
        </div>
      </AppWrapper>
    );
  }
}

export default ThemeConsumer(App);
```

_App.jsx_ com const:

```js
import React from "react";
import { Link } from "react-router-dom";

import AppWrapper from "./AppStyle";
import { ThemeConsumer } from "./context";
import logo from "./logo.svg";

const App = ({ theme, actions: { onChangeThemeDark, onChangeThemeLight } }) => (
  <AppWrapper>
    <div className="App">
      <header
        className={theme === "light" ? "App-header-light" : "App-header-dark"}
      >
        <img src={logo} className="App-logo" alt="logo" />
        <div style={{ display: "flex", paddingTop: "10vh" }}>
          <div
            onClick={() => onChangeThemeDark()}
            className="button-change-dark"
          >
            dark
          </div>
          <div
            onClick={() => onChangeThemeLight()}
            className="button-change-light"
          >
            light
          </div>
        </div>
      </header>
    </div>
  </AppWrapper>
);

export default ThemeConsumer(App);
```

_App.jsx_

Nesse modo, fazemos uma função que pega o componente inteiro, salva as props que ele tem e, além disso, jogamos como se fossem props, todos os elementos do Context. Assim podemos pegar e utilizar-lo no arquivo que estamos querendo. Esse uso é similar ao connect do Redux, quando fazemos a chamada das actions e do estado no mesmo cara.

# 4. Modo Hooks

Nesse modo vamos utilizar [o novo alpha do React](https://github.com/reactjs/rfcs/pull/68) para fazer a chamada do Context, mais informações sobre esse alpha.

Para esse modo se é necessário a instalação do alpha:

```cmd
npm install react@16.7.0-alpha.2 react-dom@16.7.0-alpha.2
```

Depois da instalação, vamos ao nosso projeto:

```js
import React from "react";

export const ThemeContext = React.createContext();

export class ThemeProvider extends React.Component {
  state = {
    theme: "dark"
  };

  onChangeTheme = theme => {
    this.setState({
      theme
    });
  };

  render() {
    return (
      <ThemeContext.Provider
        value={{
          ...this.state,
          actions: {
            onChangeThemeDark: () => this.onChangeTheme("dark"),
            onChangeThemeLight: () => this.onChangeTheme("light")
          }
        }}
      >
        {this.props.children}
      </ThemeContext.Provider>
    );
  }
}
```

_context.jsx_

No _App.jsx_ iremos chamar o [hook](https://reactjs.org/docs/hooks-intro.html) para usar o Context:

```js
import React, { useContext } from "react";
import { Link } from "react-router-dom";

import AppWrapper from "./AppStyle";
import { ThemeContext } from "./context";
import logo from "./logo.svg";

const App = () => {
  const {
    theme,
    actions: { onChangeThemeDark, onChangeThemeLight }
  } = useContext(ThemeContext);

  return (
    <AppWrapper>
      <div className="App">
        <header
          className={theme === "light" ? "App-header-light" : "App-header-dark"}
        >
          <img src={logo} className="App-logo" alt="logo" />
          <div style={{ display: "flex", paddingTop: "10vh" }}>
            <div
              onClick={() => onChangeThemeDark()}
              className="button-change-dark"
            >
              dark
            </div>
            <div
              onClick={() => onChangeThemeLight()}
              className="button-change-light"
            >
              light
            </div>
          </div>
        </header>
      </div>
    </AppWrapper>
  );
};

export default App;
```

Que também se é utlizado como function:

```js
import React, { useContext } from "react";
import { Link } from "react-router-dom";

import AppWrapper from "./AppStyle";
import { ThemeContext } from "./context";
import logo from "./logo.svg";

function App() {
  const {
    theme,
    actions: { onChangeThemeDark, onChangeThemeLight }
  } = useContext(ThemeContext);

  return (
    <AppWrapper>
      <div className="App">
        <header
          className={theme === "light" ? "App-header-light" : "App-header-dark"}
        >
          <img src={logo} className="App-logo" alt="logo" />
          <div style={{ display: "flex", paddingTop: "10vh" }}>
            <div
              onClick={() => onChangeThemeDark()}
              className="button-change-dark"
            >
              dark
            </div>
            <div
              onClick={() => onChangeThemeLight()}
              className="button-change-light"
            >
              light
            </div>
          </div>
        </header>
      </div>
    </AppWrapper>
  );
}

export default App;
```

_App.js com function_

Nesse modo utilizamos as novas alterações propostas pela a equipe do React em relação a funções e consts, para mais informações. O _useContext_ puxa todos os values que definimos no Provider e extrai conforme o requerimos no const.

Ainda se há outra maneira que se é utilizado esse modo que é dessa maneira:

```js
import React, { useContext } from "react";

const ThemeContext = React.createContext();

export class ThemeProvider extends React.Component {
  state = {
    theme: "dark"
  };

  onChangeTheme = theme => {
    this.setState({
      theme
    });
  };

  render() {
    return (
      <ThemeContext.Provider
        value={{
          ...this.state,
          actions: {
            onChangeThemeDark: () => this.onChangeTheme("dark"),
            onChangeThemeLight: () => this.onChangeTheme("light")
          }
        }}
      >
        {this.props.children}
      </ThemeContext.Provider>
    );
  }
}

export function contextUse() {
  return useContext(ThemeContext);
}
```

_context.jsx com uma nova função_

```js
import React from "react";
import { Link } from "react-router-dom";

import AppWrapper from "./AppStyle";
import { contextUse } from "./context";
import logo from "./logo.svg";

function App() {
  const {
    theme,
    actions: { onChangeThemeDark, onChangeThemeLight }
  } = contextUse();

  return (
    <AppWrapper>
      <div className="App">
        <header
          className={theme === "light" ? "App-header-light" : "App-header-dark"}
        >
          <img src={logo} className="App-logo" alt="logo" />
          <div style={{ display: "flex", paddingTop: "10vh" }}>
            <div
              onClick={() => onChangeThemeDark()}
              className="button-change-dark"
            >
              dark
            </div>
            <div
              onClick={() => onChangeThemeLight()}
              className="button-change-light"
            >
              light
            </div>
          </div>
        </header>
      </div>
    </AppWrapper>
  );
}

export default App;
```

_App.jsx chamando e utilizando a nova função criada_

Quando fazemos essa função feita acima, economizamos as chamadas de Hooks e confirmamos que puxamos os dados da onde queremos realmente.

# Referências

Abaixo há algumas refências que tive para a escrita desse artigo e também há alguns links com algum tutoriais de new Context API:

- https://reactjs.org/docs/context.html Site do React com dois dos modos(1 e 2) e uma introdução sobre o new Context API

- https://github.com/wesbos/React-Context Repositorio com o modo 1.

- https://codesandbox.io/s/rr6rmwj6y4 CodeSandBox com o modo 3.

- https://codesandbox.io/s/oj598j7mp9 CodeSandBox com um Context um pouco complexo e o modo 4.

- https://codesandbox.io/s/vq1nz87x00 CodeSandBox com os dois modos(3 e 4).

- https://github.com/xicovarisco/context-api-with-hocs/blob/master/src/components/HOCS/withAppContext.js Repositorio com o modo 3.

- https://github.com/diegohaz/awesome-react-context Listagem de onde e o que aprender em new Context API.

- https://reactjs.org/docs/hooks-intro.html Introdução ao Hooks, que irão vir na versão 16.7
