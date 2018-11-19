# Anotações

eval(string) = com a string escrita da forma correta se é possivel realizar coding em modo de execução.
ex: 
`````
eval("var b = 5;")
`````
with = tem a mesma função de eval, só que with é já tem uma mudança explicita.
ex:
`````
with(obj){ a = 5; }
`````
não se é recomendado utilizar nenhum dos dois anteriores, pois esses afetam no desempenho do script.

Zero é False

````
!!0  \\false
````

`````
!!1  \\true
`````

`````
!!999 \\true
`````

````
1+2 === 3 \\true

0.1+0.2 === 0.3 \\false
````

## Desestruturação

Pode-se destruturar objetos e vetores

````
const user = {
  nome: 'Diego',
  empresa: {
    nome: 'RocketSeat',
    site: 'www.rocketseat.com.br'
  }
};

let { nome } = user;

console.log(nome); // Diego

let { empresa: { site } } = user;

console.log(site); // www.rocketseat.com.br
````

É possivel fazer isso atraves de uma function:

````
function showName({ nome }) {
  console.log(nome);
}

const user = { nome: 'Diego' };

showName(user); // Diego
````

E em vetores: 

````
const numbers = [1, 2, 3];

let [a, b, c] = numbers;

console.log(a); // 1
console.log(b); // 2
console.log(c); // 3
````

## Todas as exportações de um arquivo

App.js
````
export function soma(a, b) {
  return a + b;
}

export const user = {
  nome: 'Diego',
  empresa: 'RocketSeat'
};

export default class App {
  static log() {
    console.log('Hey');
  }
}
````

Index.js
````
import helpers from 'App';

helpers.soma(1, 3); // 4
console.log(helpers.user.nome); // Diego
````
## Arrays Management

````
const original = { a: 1, b: 2 }; \\ Object { a: 1, b: 2 }

const copy = { ...original, c: 3 }; \\ Object { a: 1, b: 2, c: 3 }

const { a, ...noA } = copied; \\ Object { b: 2, c: 3 }
````

````
// bad
const foo = a ? a : b;
const bar = c ? true : false;
const baz = c ? false : true;

// good
const foo = a || b;
const bar = !!c;
const baz = !c;
````

## Operadores Matemáticos

````
2 ** 2
````
** é uma potencialização, onde o numero antes do ** é o potencializado e o após é o expoente