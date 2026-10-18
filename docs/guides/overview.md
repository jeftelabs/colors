# Visão geral

## O que é

`@jeftelabs/colors` é uma biblioteca ESM que oferece uma interface encadeável sobre as transformações de texto de [`kleur`](https://github.com/lukeed/kleur). O consumidor escolhe como cada cadeia deve produzir o resultado: com códigos ANSI, sem estilo ou como uma descrição textual das transformações.

```ts
import useColors from '@jeftelabs/colors'

useColors.ansi().red().bold('Falha')
useColors.silent().red().bold('Falha')
useColors.raw().red().bold('Falha')
```

## Modos de saída

| Factory | Resultado | Quando usar |
| --- | --- | --- |
| `ansi()` | Texto decorado por `kleur` com saída ANSI habilitada. | Exibir cor e estilo em uma saída de terminal compatível. |
| `silent()` | O texto de entrada convertido para string, sem decoração. | Preservar o conteúdo quando cores devem ser omitidas. |
| `raw()` | Nome das transformações e texto em notação aninhada. | Inspecionar ou testar quais transformações foram aplicadas. |

Por exemplo, `raw().red().bold('Falha')` produz `red(bold(Falha))`. Esse formato descreve as chamadas; ele não contém códigos ANSI.

## O que o pacote faz

- Expõe os mesmos métodos de cor e estilo nos três modos.
- Permite compor transformações encadeando métodos sem argumento e concluir a cadeia passando o texto ao último método.
- Fornece uma representação determinística para testes que não dependem da presença de um terminal.

## O que o pacote não faz

- Não escolhe automaticamente um modo com base em TTY, `NO_COLOR` ou outra variável de ambiente.
- Não oferece uma configuração por instância para habilitar/desabilitar ANSI. A implementação ANSI define `kleur.enabled = true` no construtor.
- Não substitui `kleur`; usa as funções dessa biblioteca para o modo ANSI.

## Caminho de leitura

Para começar a usar, siga [Instalação e primeiros passos](./getting-started.md). Para assinaturas e lista completa de métodos, consulte a [Referência da API](../references/api.md). Para entender como a implementação funciona por dentro, leia [Arquitetura](../architecture.md).
