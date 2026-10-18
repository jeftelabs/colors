# API e exemplos

## Instalação

Instale o pacote no projeto consumidor:

```sh
npm install @jeftelabs/colors
```

O pacote é ESM (`"type": "module"`) e publica JavaScript compilado em `build/`. Consulte a versão publicada do pacote para o requisito de Node.js aplicável; o repositório usa Node.js 24 para seu fluxo de desenvolvimento e testes.

## Export principal

O entry point expõe um objeto como default export:

```ts
import useColors from '@jeftelabs/colors'
```

O objeto possui três factories, cada um retornando uma instância de `Colors`:

```ts
useColors.ansi()
useColors.silent()
useColors.raw()
```

## Métodos de transformação

Todas as instâncias oferecem os métodos abaixo. Cada método pode ser chamado sem argumento para continuar uma cadeia, ou com o texto final para obter uma string.

| Grupo | Métodos |
| --- | --- |
| Cores de texto | `black`, `red`, `green`, `yellow`, `blue`, `magenta`, `cyan`, `white`, `gray`, `grey` |
| Cores de fundo | `bgBlack`, `bgRed`, `bgGreen`, `bgYellow`, `bgBlue`, `bgMagenta`, `bgCyan`, `bgWhite` |
| Estilos | `reset`, `bold`, `dim`, `italic`, `underline`, `inverse`, `hidden`, `strikethrough` |

Os nomes correspondem às transformações de `kleur`. `gray` e `grey` são aliases conforme a API da dependência.

### Aplicação direta

```ts
const colors = useColors.ansi()
const error = colors.red('Falha ao salvar')
```

### Encadeamento

```ts
const warning = useColors.ansi().yellow().bold('Atenção')
const nested = useColors.ansi().red().bgBlack().dim('Mensagem')
```

O argumento de texto é passado ao último método da cadeia. A cadeia pode ser construída em uma linha ou em chamadas separadas na mesma instância:

```ts
const message = useColors.ansi()
message.red()
const result = message.bold('texto')
```

### Modos

```ts
useColors.ansi().red('x')
// Resultado: x envolto pelo código ANSI de vermelho, com saída ANSI habilitada.

useColors.silent().red('x')
// Resultado: "x"

useColors.raw().red('x')
// Resultado: "red(x)"

useColors.raw().red().bgBlack().dim('x')
// Resultado: "red(bgBlack(dim(x)))"
```

Use `raw()` em testes de lógica que precisam verificar os nomes e a ordem das transformações sem comparar códigos ANSI dependentes de ambiente. Use `silent()` quando o conteúdo deve permanecer sem estilo. Use `ansi()` para renderização de terminal.

## Reuso de instâncias

É seguro guardar uma instância e usá-la para várias expressões completas: cada modo com estado limpa a cadeia depois de gerar uma string.

```ts
const colors = useColors.raw()
colors.red('um') // "red(um)"
colors.bold('dois') // "bold(dois)"
```

Não compartilhe uma instância entre cadeias incompletas concorrentes. Chamadas sem texto acumulam estado até uma transformação receber o texto.

## Tipos

O subpath `@jeftelabs/colors/types` exporta `Colors` e `ColorTransformations`:

```ts
import type { Colors, ColorTransformations } from '@jeftelabs/colors/types'

function paint(colors: Colors, transformation: ColorTransformations) {
  return colors[transformation]('texto')
}
```

Observação de tipagem: hoje as sobrecargas públicas dos métodos em `Colors` declaram o argumento de texto como `string`, embora as implementações aceitem `string | number` internamente. Portanto, passe strings em código TypeScript consumidor até que essa assinatura seja ampliada intencionalmente.

## O que a API não faz

- Não escolhe automaticamente entre ANSI e modo silencioso com base em TTY ou variáveis de ambiente.
- Não exporta as classes concretas `Kleur`, `Silent` ou `Raw` pelo entry point principal.
- Não muda a configuração por chamada. A classe ANSI habilita `kleur` ao ser instanciada.
