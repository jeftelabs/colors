# @jeftelabs/colors

Wrapper sobre [`kleur`](https://github.com/lukeed/kleur) com três modos de saída: ANSI para terminais, texto sem estilos e uma representação textual das transformações, útil em testes.

[![CI](https://github.com/jeftelabs/colors/actions/workflows/checks.yml/badge.svg)](https://github.com/jeftelabs/colors/actions/workflows/checks.yml)
[![npm version](https://img.shields.io/npm/v/@jeftelabs/colors)](https://www.npmjs.com/package/@jeftelabs/colors)
[![license](https://img.shields.io/npm/l/@jeftelabs/colors)](./LICENSE)

## Por que usar

A API mantém os mesmos métodos de cor e estilo em todos os modos. Assim, a aplicação pode escolher como produzir o texto sem mudar as chamadas de transformação.

- `ansi()` aplica cores e estilos usando `kleur`.
- `silent()` retorna o texto sem decoração.
- `raw()` descreve as transformações como texto simples, facilitando asserções previsíveis.

## Instalação

```sh
npm install @jeftelabs/colors
```

O pacote é ESM e oferece um default export.

## Uso

```ts
import useColors from '@jeftelabs/colors'

const colors = useColors.ansi()

console.log(colors.red('Isso é um erro'))
console.log(colors.cyan('Olá, mundo'))
console.log(colors.red().bgBlack('Erro com fundo'))
```

Os métodos podem receber o texto diretamente ou ser encadeados. Em uma cadeia, chame os estilos sem argumento e passe o texto ao último método:

```ts
const message = useColors.ansi().red().bold('Falha ao salvar')
```

Cada chamada a `ansi()`, `silent()` ou `raw()` cria uma instância. Consulte a [referência da API](./docs/references/api.md) para a lista completa de métodos e detalhes de tipagem.

## Modo raw para testes

`raw()` não emite códigos ANSI. Em vez disso, inclui os nomes das transformações no resultado:

```ts
const colors = useColors.raw()

colors.red('hello world')
// "red(hello world)"

colors.bgBlack().red('hello world')
// "bgBlack(red(hello world))"
```

Isso permite testar a transformação solicitada sem depender do terminal:

```ts
assert.equal(useColors.raw().red('hello world'), 'red(hello world)')
```

## Modo silencioso

`silent()` ignora as transformações e retorna o conteúdo como texto:

```ts
const colors = useColors.silent()

colors.red('hello world')
// "hello world"

colors.bgBlack().red('hello world')
// "hello world"
```

## Escolher o modo conforme o ambiente

O pacote não detecta automaticamente suporte a cores. A aplicação pode escolher o modo usando as informações do ambiente. Por exemplo, com o pacote opcional [`supports-color`](https://www.npmjs.com/package/supports-color):

```sh
npm install supports-color
```

```ts
import supportsColor from 'supports-color'
import useColors from '@jeftelabs/colors'

const colors = process.env.NODE_ENV === 'test'
  ? useColors.raw()
  : supportsColor.stdout
    ? useColors.ansi()
    : useColors.silent()

export default colors
```

O modo ANSI habilita `kleur` ao criar a instância. Se o processo exigir controle explícito da saída colorida, selecione `silent()` quando ANSI não for desejado.

## Desenvolvimento

O fluxo de testes usa `@jeftelabs/exec` e requer Node.js 24 ou superior. Clone o repositório, instale as dependências e rode os testes rápidos:

```sh
npm ci
npm run quick:test
```

Comandos adicionais:

- `npm test`: lint e testes com cobertura.
- `npm run typecheck`: verificação de tipos.
- `npm run build`: compilação dos artefatos de distribuição.

Veja [Desenvolvimento, testes e publicação](./docs/guides/development.md) para o fluxo completo e [a documentação](./docs/README.md) para arquitetura e API.

## Licença

[MIT](./LICENSE)
