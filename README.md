# Prática DevOps — Hello World com Pipeline Segura

Projeto simples que demonstra uma classe `HelloWorld` escrita em JavaScript e
exibida em uma página HTML, com uma **esteira de CI/CD no GitHub Actions** que
executa testes de segurança **SAST** e **DAST** a cada alteração.

## Estrutura

```
pratica-devops/
├── .github/
│   └── workflows/
│       └── secure-pipeline.yml   # Pipeline de segurança (SAST + DAST)
├── index.html                    # Página com a classe HelloWorld
└── README.md                     # Esta documentação
```

## Sobre o projeto

O arquivo `index.html` contém:

- Uma classe JavaScript `HelloWorld` com dois métodos:
  - `greet()` — retorna a mensagem de saudação (`Hello, <nome>!`).
  - `render()` — escreve a saudação em um elemento da página.
- Estilos básicos para exibir a mensagem em um cartão centralizado.

## Como executar

Abra o arquivo `index.html` em um navegador:

- Dê duplo clique no arquivo, ou
- Use a extensão **Live Server** no VS Code (botão direito → *Open with Live Server*), ou
- Suba um servidor local na pasta do projeto e acesse <http://localhost:8080/index.html>:

  ```bash
  python -m http.server 8080
  ```

Você verá a mensagem **Hello, &lt;nome&gt;!** centralizada na tela.

## Como personalizar

Edite a chamada no final do script:

```js
const app = new HelloWorld("Seu Nome", document.getElementById("greeting"));
app.render();
```

Basta trocar `"Seu Nome"` pela saudação desejada.

## Pipeline de segurança (CI/CD)

A esteira fica em `.github/workflows/secure-pipeline.yml` e possui **dois nós**,
executados em sequência:

```
push / PR na master ──► [ SAST - Semgrep ] ──(passou)──► [ DAST - OWASP ZAP ]
```

### Quando roda

| Evento                              | Executa? |
|-------------------------------------|----------|
| `push` na branch `master`           | ✅       |
| Pull Request para a `master`        | ✅       |
| Manual (botão **Run workflow**)     | ✅       |
| Push em outras branches             | ❌       |

### Nó 1 — SAST (Static Application Security Testing)

Análise estática do código-fonte com o **[Semgrep](https://semgrep.dev/)**,
usando os conjuntos de regras `p/default` e `p/javascript`.

- Gera o relatório `semgrep-report.json`, publicado como artifact
  `relatorio-sast-semgrep`.
- **Validação:** executa o Semgrep com `--error`. Se houver **qualquer achado**,
  o job falha e a pipeline é interrompida (o DAST não roda).

### Nó 2 — DAST (Dynamic Application Security Testing)

Teste dinâmico da aplicação em execução com o
**[OWASP ZAP](https://www.zaproxy.org/)** (Baseline Scan).

1. Sobe a aplicação com `python3 -m http.server 8080`.
2. **Validação:** confirma que `http://localhost:8080/index.html` responde
   **HTTP 200**. Caso contrário, o job falha.
3. Executa o `zap-baseline.py` (via Docker) contra a página.
   **Validação:** alertas de nível FAIL quebram a pipeline; alertas WARN
   (`-I`) apenas aparecem no relatório.
4. Publica os relatórios `zap-report.html` e `zap-report.json` como artifact
   `relatorio-dast-zap`.

O DAST depende do SAST (`needs: sast`): só é executado se a análise estática
passar.

### Boas práticas aplicadas

As actions usadas no workflow estão **fixadas por SHA de commit** (em vez de
tags como `@v4`), evitando ataques de cadeia de suprimentos caso uma tag seja
reapontada. Essa correção foi exigida pelo próprio SAST — a regra
`github-actions-mutable-action-tag` do Semgrep bloqueou a pipeline até o ajuste.

### Como acompanhar

1. Acesse a aba **Actions** do repositório.
2. Abra a execução do workflow **Secure Pipeline CI/CD**.
3. Veja os logs de cada job (**SAST - Semgrep** e **DAST - OWASP ZAP**).
4. Baixe os relatórios na seção **Artifacts**, no final da página da execução.

## Requisitos

- Para a página: nenhuma dependência externa, apenas um navegador moderno
  (Chrome, Edge, Firefox, Safari).
- Para a pipeline: nenhuma configuração extra — roda nos runners `ubuntu-latest`
  do GitHub Actions.
