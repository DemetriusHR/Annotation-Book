# Iniciando com new Context API

Na [versão 16.3]() tivemos uma drastica mudança no Context. [Anteriomente o Context API]() era utilizado para como um HOC, para passar props entre os componentes. [Agora o Context API]() é utilizado para o compatilhamento de "caracteriscas" entre o projeto inteiro, sem a utilização de [HOC](). Pois nessa nova versão do Context API, a maior mudança é a redução nos HOCs entre os componentes.

O Context API é uma ferramenta que deixa os estados globais, assim como [Redux](), logo tira a centralidade do Redux e coloca o Redux para fazer outras funções, apesar do Redux não ser contraindicado em alguns casos especificos para essa função.

Antes de começar-mos com a prática há duas palavras que precisam ser entendidas: Provider e Consumer.

Provider: é onde definimos o valor do nosso context e, também, será o responsável por disponibilizar esse valor para os componentes abaixo dele.

Consumer: é onde pegamos todo o valor que está definido no Provider e utilizamos, como e onde queremos no componente.

Nesse artigo estarei mostrando passo a passo como criar um Context e compartilhar em pelo menos um componente. Logo após será deixado alguns links, para o aprofundamento e estudo das demais coisas que se dá pra fazer com o new Context API.

Começamos com um [CRA(create-react-app)]():

```cmd
create-react-app testing-context
```

Verifique que o React está na versão de, pelo menos, 16.3 para a nova versão do Context API estar ativa:

```json
{
  "name": "testing-context",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "babel-eslint": "^10.0.1",
    "prettier": "^1.14.3",
    "react": "^16.6.3",
```

_package.json_

Feita a verificação, temos que ter uma estrutura como essa:

```
- node_modules
- public
- src
-- App.css
-- App.js
-- Context.jsx
-- index.css
-- index.js
-- logo.svg
-- serviceWorker.js
```

Nessa estrutura iremos mexer em poucas coisas dos arquivos que já estavam nela, começamos como o _App.css_:

```css
.App {
  text-align: center;
}

.App-logo {
  height: 40vmin;
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

@keyframes App-logo-spin-right {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(-360deg);
  }
}
```

_App.css_

Agora que definimos o estilo do App, vamos alterar o _App.js_ também:

```js
import React from "react";

import "./App.css";

const App = () => (
  <div className="App">
    <header className="App-header-dark">
      <img
        src="https://upload.wikimedia.org/wikipedia/commons/thumb/4/47/React.svg/512px-React.svg.png"
        className="App-logo animation-right"
        alt="logo"
      />
    </header>
  </div>
);

export default App;
```

_App.js_

Logo após iremos criar dois Contexts, um irá trocar o tema da página e o outro mostrará o nome real da pessoa, quando nós digitamos o apelido dela:

```js
import React from "react";

export const ThemeContext = React.createContext();

export const nicknameConfig = {
  jioke: {
    firstName: "Kingsley",
    lastName: "Silas"
  }
};

export const NicknameContext = React.createContext(nicknameConfig.jioke);

class ThemeProvider extends React.Component {
  state = {
    theme: "dark"
  };

  onChangeTheme = theme =>
    this.setState({
      theme
    });

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

class NicknameProvider extends React.Component {
  state = {
    nickname: "jioke",
    toggleNick: newNick => this.toggleNick(newNick)
  };

  toggleNick(newNick) {
    this.setState({
      nickname: newNick
    });
  }

  render() {
    return (
      <NicknameContext.Provider
        value={{
          ...this.state,
          config: nicknameConfig[this.state.nickname]
        }}
      >
        <ThemeContext.Consumer>
          {context => (
            <input
              type="text"
              value={this.state.nickname}
              className={
                context.theme === "light" ? "input-light" : "input-dark"
              }
              onChange={event => this.toggleNick(event.target.value)}
            />
          )}
        </ThemeContext.Consumer>
        {this.props.children}
      </NicknameContext.Provider>
    );
  }
}

export { ThemeProvider, NicknameProvider };
```

_Context.jsx_

## Explicação do código escrito acima:

- Aqui criamos o context "ThemeContext" sem valor algum:

```js
export const ThemeContext = React.createContext();
```

- Logo após, temos a criação do context "NicknameContext" com o valor default de "jioke":

```js
export const nicknameConfig = {
  jioke: {
    firstName: "Kingsley",
    lastName: "Silas"
  },
  spiderman: {
    firstName: "Peter",
    lastName: "Parker"
  }
};

export const NicknameContext = React.createContext(nicknameConfig.jioke);
```

- Criamos, também, a class que será o Provider do context "ThemeContext". Definimos um state e uma function para mudar o state, depois definimos o valor do Provider com o state e o action que tem duas funções para a mudança definida do state. Após é chamada o children, que será tudo no meio das tags "ThemeProvider":

```js
class ThemeProvider extends React.Component {
  state = {
    theme: "dark"
  };

  onChangeTheme = theme =>
    this.setState({
      theme
    });

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

- Essa é a class um pouco mais complicada, mas fácil de entender.
  Temos a criação do state já refêrenciando a function que crio após a criação do state.
  Depois temos a definição do valor do Provider, chamando o state, assim como a const que criamos acima da criação do Context "NicknameContext".
  Após isso, já chamo o Consumer do context "ThemeContext", para que na troca de tema nós mudar-mos o tema do input que está sendo criado.
  Logo, o input tem o valor do state "nickname" e quando se é mudado já referenciamos a function que está tratando de pegar toda a alteração.
  Fechando o Consumer do context "ThemeContext" temos um children, que irá receber tudo que está entre as tags "NicknameProvider".

```js
class NicknameProvider extends React.Component {
  state = {
    nickname: "jioke",
    toggleNick: newNick => this.toggleNick(newNick)
  };

  toggleNick(newNick) {
    this.setState({
      nickname: newNick
    });
  }

  render() {
    return (
      <NicknameContext.Provider
        value={{
          ...this.state,
          config: nicknameConfig[this.state.nickname]
        }}
      >
        <ThemeContext.Consumer>
          {context => (
            <input
              type="text"
              value={this.state.nickname}
              className={
                context.theme === "light" ? "input-light" : "input-dark"
              }
              onChange={event => this.toggleNick(event.target.value)}
            />
          )}
        </ThemeContext.Consumer>
        {this.props.children}
      </NicknameContext.Provider>
    );
  }
}
```

- Por último, somente exportamos os dois Providers que criamos: "ThemeProvider" e "NicknameProvider".

```js
export { ThemeProvider, NicknameProvider };
```

## Alterando o index.js

Criamos o _Context.jsx_, agora temos que alterar o _index.js_ para utilizar-mos o Context "ThemeContext":

```js
import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter, Switch, Route } from "react-router-dom";

import { ThemeProvider } from "./Context";
import "./index.css";
import App from "./App";
import * as serviceWorker from "./serviceWorker";

ReactDOM.render(
  <ThemeProvider>
    <App />
  </ThemeProvider>,
  document.getElementById("root")
);

serviceWorker.unregister();
```

_index.js_

- Chamamos o Provider antes do "App". Pois se não colocar o componente no meio das tags de Provider, o componente não consegue utilizar o context com o valor que definimos no Provider.

## Alterando o App.js

Alterado o _index.js_, vamos alterar o _App.js_ para usarmos o Context que nós definimos:

```js
import React from "react";

import logo from "./logo.svg";
import "./App.css";
import { ThemeContext, NicknameProvider, NicknameContext } from "./Context";

const App = () => (
  <IndexWrapper>
    <ThemeContext.Consumer>
      {contextTheme => {
        const {
          theme,
          actions: { onChangeThemeDark, onChangeThemeLight }
        } = contextTheme;

        return (
          <div className="App">
            <header
              className={
                theme === "light" ? "App-header-light" : "App-header-dark"
              }
            >
              <img src={logo} className="App-logo animation-right" alt="logo" />
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
              <NicknameProvider>
                <NicknameContext.Consumer>
                  {contextNickname =>
                    context.config !== undefined ? (
                      <div>
                        <p>
                          {context.config.firstName} {context.config.lastName}
                        </p>
                      </div>
                    ) : (
                      ""
                    )
                  }
                </NicknameContext.Consumer>
              </NicknameProvider>
            </header>
          </div>
        );
      }}
    </ThemeContext.Consumer>
  </IndexWrapper>
);

export default App;
```

_App.js_

### Detalhamento do codigo _App.js_

- Aqui pegamos somente o "ThemeContext", pois o Provider já colocamos antes. Já o Nickname chamamos o Provider, pois não definimos ainda, e o Consumer para pegar os valores que declarei no Provider.

```js
import { ThemeContext, NicknameProvider, NicknameContext } from "./Context";
```

- Logo após, chamo o Consumer do context "ThemeContext", pego os valores que irei utilizar do context definido e aloco em consts.

```js
const App = () => (
  <IndexWrapper>
    <ThemeContext.Consumer>
      {contextTheme => {
        const {
          theme,
          actions: { onChangeThemeDark, onChangeThemeLight }
        } = contextTheme;

        return (
          <div className="App">
```

- Nesse momento é quando começaremos a utilizar o context, veja que pegamos o context comparamos com `light` e dependendo da condição ele agrega uma class ao `header`.

```js
 <header
    className={
      theme === "light" ? "App-header-light" : "App-header-dark"
    }
  >
```

- Utilizamos nos onClick as actions que criamos, para que nosso context "ThemeContext" altere seu estado.

```js
<div style={{ display: "flex", paddingTop: "10vh" }}>
  <div onClick={() => onChangeThemeDark()} className="button-change-dark">
    dark
  </div>
  <div onClick={() => onChangeThemeLight()} className="button-change-light">
    light
  </div>
</div>
```

- Abrimos e fechamos o Provider do context "NicknameContext". Além disso, dentro do nosso Consumer fazermos uma verificação para ver se nosso valor `config`, que pegamos baseado na const, há o valor que estamos digitando no input que definimos no Provider desse context, o "NicknameContext". Depois disso, somente vamos mostrar o valor que pegamos do nosso valor, o `firstName` e o `lastName`.

```js
<NicknameProvider>
  <NicknameContext.Consumer>
    {contextNickname =>
      context.config !== undefined ? (
        <div>
          <p>
            {context.config.firstName} {context.config.lastName}
          </p>
        </div>
      ) : (
        ""
      )
```

- Depois, só fechamos as tags abertas e exportamos o _App_.

## Considerações Finais

O new Context API é uma ferramenta incrível do React e utilizando ela da para fazer muitas coisas interessantes. Para saber mais sobre o Context, irei deixar alguns links que irão para pequenas aplicações feitas com context e alguns outros artigos sobre ele.

- https://codesandbox.io/s/q3j8py1wr4

- https://medium.com/dailyjs/reacts-%EF%B8%8F-new-context-api-70c9fe01596b

- https://github.com/Jafsari/Context-API-Practice/blob/master/src/App.js

- https://medium.freecodecamp.org/replacing-redux-with-the-new-react-context-api-8f5d01a00e8c

- https://github.com/xicovarisco/context-api-with-hocs

- https://blog.bitsrc.io/react-context-api-a-replacement-for-redux-6e20790492b3
