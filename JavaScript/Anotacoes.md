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
import * as helpers from 'App';

helpers.soma(1, 3); // 4
console.log(helpers.user.nome); // Diego
````
