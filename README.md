# Projeto Inicial — Hello World

Projeto inicial simples que demonstra uma classe `HelloWorld` escrita em
JavaScript e exibida em uma página HTML.

## Estrutura

```
hello-world/
├── HelloWorld.html   # Página com a classe HelloWorld
└── README.md         # Esta documentação
```

## Sobre o projeto

O arquivo `HelloWorld.html` contém:

- Uma classe JavaScript `HelloWorld` com dois métodos:
  - `greet()` — retorna a mensagem de saudação (`Hello, <nome>!`).
  - `render()` — escreve a saudação em um elemento da página.
- Estilos básicos para exibir a mensagem em um cartão centralizado.

## Como executar

1. Abra a pasta `hello-world` como workspace (ou navegue até ela).
2. Abra o arquivo `HelloWorld.html` em um navegador:
   - Dê duplo clique no arquivo, ou
   - Use a extensão **Live Server** no VS Code (botão direito → *Open with
     Live Server*).

Você verá a mensagem **Hello, Mundo!** centralizada na tela.

## Como personalizar

Edite a chamada final no final do script:

```js
const app = new HelloWorld("Seu Nome", document.getElementById("greeting"));
app.render();
```

Basta trocar `"Seu Nome"` pela saudação desejada.

## Requisitos

- Nenhum dependência externa.
- Qualquer navegador moderno (Chrome, Edge, Firefox, Safari).
