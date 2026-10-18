# Documentação do `@jeftelabs/colors`

Esta documentação explica como instalar, usar, compreender e desenvolver a biblioteca `@jeftelabs/colors`. O conteúdo foi elaborado a partir do código, dos testes, do `package.json` e dos workflows deste repositório.

## Para quem usa o pacote

- [Visão geral e conceitos](./guides/overview.md): propósito do pacote, modos disponíveis e limites de uso.
- [Instalação e primeiros passos](./guides/getting-started.md): instalação e exemplos funcionais de utilização.
- [Referência da API](./references/api.md): exports, transformações, tipos, assinatura e semântica de encadeamento.

## Para quem desenvolve o pacote

- [Arquitetura](./architecture.md): estrutura dos módulos, fluxo das chamadas, estado e decisões de implementação.
- [Desenvolvimento, testes e publicação](./guides/development.md): ambiente local, scripts, testes, build, CI e release.
- [Instruções para agentes](../AGENTS.md): convenções de trabalho e fontes de verdade do repositório.

## Fontes e convenções

- O [README principal](../README.md) é a introdução rápida e apresenta um exemplo mínimo.
- O [`AGENTS.md`](../AGENTS.md) contém as instruções compartilhadas para agentes de IA; o [`CLAUDE.md`](../CLAUDE.md) aponta para essas instruções e para esta documentação.
- `index.ts`, `src/`, `tests/`, `package.json`, `tsconfig.json` e `.github/workflows/` são as fontes de verdade quando uma descrição divergir do comportamento atual.
- Exemplos de consumo pressupõem ESM e as exports definidas no `package.json`. O requisito de Node para desenvolvimento deve ser confirmado nos scripts e nas dependências; o pacote não declara atualmente `engines`.

Ao mudar a API, os requisitos, a configuração, o processo de desenvolvimento ou outro comportamento documentado, atualize a página correspondente e este índice quando adicionar ou remover documentos.
