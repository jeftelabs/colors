# Instalação e primeiros passos

## Instalar

Adicione o pacote ao projeto consumidor:

```sh
npm install @jeftelabs/colors
```

O pacote é ESM. Use-o em um projeto com suporte a imports ESM do Node.js ou com um bundler que consuma ESM. A API de runtime tem um default export.

## Criar uma saída ANSI

```ts
import useColors from '@jeftelabs/colors'

const error = useColors.ansi().red().bold('Falha ao salvar')
console.log(error)
```

Cada chamada a `ansi()` cria uma instância. Métodos sem argumento acumulam transformações; o último método recebe o texto e retorna uma string.

## Escolher outro modo

```ts
import useColors from '@jeftelabs/colors'

const plain = useColors.silent().red().bold('Falha ao salvar')
// "Falha ao salvar"

const described = useColors.raw().red().bold('Falha ao salvar')
// "red(bold(Falha ao salvar))"
```

`silent()` ignora as transformações e devolve o conteúdo. `raw()` mantém a ordem delas como texto, útil para testes e inspeção.

## Usar uma instância em etapas

```ts
const colors = useColors.raw()
colors.red()
const result = colors.bold('mensagem')
// "red(bold(mensagem))"
```

Não intercale cadeias incompletas na mesma instância: chamadas sem texto mantêm estado até que uma transformação receba o texto.

## Escolher o modo conforme o ambiente

O pacote não detecta TTY, `NO_COLOR` ou suporte a cores. A aplicação pode selecionar um factory com base no ambiente. Este exemplo usa a dependência opcional `supports-color`:

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

O construtor do modo ANSI define `kleur.enabled = true`, afetando o módulo `kleur` compartilhado no processo. Escolha `silent()` quando a saída sem sequências ANSI for necessária.

## Testar o projeto localmente

O requisito do loader usado pelo fluxo de testes é Node.js 24 ou superior. O pacote não declara hoje `engines` em seu `package.json`.

```sh
npm ci
npm run quick:test
```

Para mais comandos de manutenção, consulte [Desenvolvimento, testes e publicação](./development.md). Para a lista completa de métodos e tipos, consulte a [Referência da API](../references/api.md).
