# Arquitetura

## Objetivo

`@jeftelabs/colors` fornece uma interface consistente para transformar strings com as funções de `kleur`. O consumidor escolhe a implementação ao criar uma instância:

- `ansi()` delega a `kleur` e produz sequências ANSI.
- `silent()` devolve o conteúdo sem aplicar estilo.
- `raw()` representa as transformações como texto, o que permite testar qual sequência foi solicitada sem depender dos códigos de terminal.

As três implementações compartilham os mesmos métodos públicos por meio da classe abstrata `Colors`.

## Mapa dos arquivos

| Arquivo | Responsabilidade |
| --- | --- |
| `index.ts` | Cria e exporta o objeto `useColors`, com os factories `ansi`, `silent` e `raw`. |
| `src/base.ts` | Define a classe abstrata `Colors` e os métodos públicos encadeáveis. |
| `src/types.ts` | Exporta o tipo `Colors` e deriva `ColorTransformations` das chaves da API. |
| `src/kleur.ts` | Implementa o modo ANSI por meio da dependência `kleur`. |
| `src/silent.ts` | Implementa o modo que converte o valor final para texto sem estilo. |
| `src/raw.ts` | Armazena métodos chamados sem argumento e os serializa quando recebe o texto. |
| `tests/colors.spec.ts` | Testa resultados dos modos e compara ANSI com `kleur`. |
| `bin/test.ts` | Configura e inicia `@t8ngs/runner` e instala as asserções. |

## Fluxo de uma chamada

1. O consumidor chama um factory em `useColors`; `index.ts` instancia a classe correspondente.
2. Um método como `red()` ou `bold()` é herdado de `Colors`.
3. `Colors` encaminha o nome da transformação e o argumento opcional para o método protegido `transform()` da implementação concreta.
4. Sem texto, `transform()` registra/acumula o estilo e retorna a própria instância. Assim, o próximo método pode ser encadeado.
5. Com texto, a implementação retorna uma string. As implementações com estado limpam o encadeamento usado ao concluir a chamada.

Exemplo conceitual:

```ts
useColors.raw().red().bold('Falha')
// raw transforma a cadeia em "red(bold(Falha))"
```

O texto é inserido no último método chamado; por isso a ordem de aninhamento representa a ordem de chamadas, tal como `kleur.red().bold(text)`.

## Implementações

### ANSI (`src/kleur.ts`)

`Kleur` mantém opcionalmente uma cadeia de `kleur.Kleur` em `#chain`. Chamadas sem texto criam ou estendem essa cadeia. Ao receber texto, usa a cadeia acumulada e a limpa, ou chama diretamente a função correspondente de `kleur` se não houver cadeia.

O construtor define `kleur.enabled = true`. Como `kleur` é um módulo compartilhado, essa configuração altera o estado do módulo importado por outros consumidores no mesmo processo. O pacote não detecta automaticamente TTY, `NO_COLOR` ou preferências do terminal; quem precisar de comportamento diferente pode escolher `silent()`.

### Silencioso (`src/silent.ts`)

`Silent` ignora os nomes das transformações. Chamadas sem texto retornam `this` para permitir encadeamento; a chamada terminal devolve `String(text)`. Logo, números recebidos em runtime são convertidos para strings.

### Raw (`src/raw.ts`)

`Raw` acumula os nomes das transformações numa lista privada. Ao receber texto, concatena os nomes e o valor com parênteses: `red().bold('x')` resulta em `red(bold(x))`. Depois de produzir o resultado, limpa a lista para que a instância possa ser reutilizada sem carregar transformações anteriores.

## Estado e reutilização

O estado de encadeamento pertence à instância, não ao objeto factory. Cada invocação do factory cria um novo objeto. Termine cada cadeia com um argumento de texto para produzir o resultado e limpar o estado pendente. Chamadas sem argumento que nunca chegam a um texto mantêm estado acumulado nessa instância.

## Limites observados

- A API não oferece um método de configuração de cores; o modo ANSI força `kleur.enabled = true` ao criar a instância.
- `raw()` é uma serialização para inspeção/teste, não um formato de terminal nem uma representação a ser interpretada pela biblioteca.
- A implementação aceita `string | number` em `transform()`, mas as sobrecargas públicas declaradas em `Colors` atualmente expõem argumentos de texto como `string`. A documentação de tipos deve ser alinhada ao comportamento se suporte público a números for desejado.
- `ColorTransformations` é derivado das chaves públicas de `Colors`, de modo que alterações nos métodos públicos também alteram essa união. As implementações assumem que os nomes usados são transformações válidas de `kleur`.
