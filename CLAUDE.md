# CLAUDE.md

Orientações para Claude e outros agentes que trabalhem neste repositório.

O [`AGENTS.md`](./AGENTS.md) contém as instruções canônicas para agentes. Leia-o antes de começar. Este projeto é o pacote `@jeftelabs/colors`, uma biblioteca ESM que oferece modos ANSI, silencioso e textual para transformações de cor.

A documentação detalhada está organizada em [`docs/README.md`](./docs/README.md), com guias para consumidores, arquitetura, desenvolvimento e referências de API. Consulte o índice conforme a tarefa e atualize a página correspondente quando o comportamento descrito mudar.

## Documentação obrigatória

**É extremamente obrigatório documentar detalhadamente toda criação ou modificação feita neste projeto.** Sempre que criar algo novo ou modificar qualquer arquivo, atualize ou crie documentação que explique o que mudou, por que existe, como funciona e como usar ou manter. Escreva para que tanto pessoas quanto agentes de IA entendam o projeto sem depender de contexto externo.

Atualize a documentação existente mesmo para mudanças internas ou pequenas. Crie ou amplie uma página em `docs/` quando o assunto ainda não estiver coberto, atualize [`docs/README.md`](./docs/README.md) ao adicionar, remover ou reorganizar páginas e mantenha o caminho de descoberta no [`README.md`](./README.md). Antes de concluir a tarefa, confira que a documentação reflete o código e que os links estão válidos. Siga também todas as instruções de [`AGENTS.md`](./AGENTS.md).

## Contexto rápido

- API pública: [`index.ts`](./index.ts); implementação e tipos: [`src/`](./src/).
- Testes: [`tests/colors.spec.ts`](./tests/colors.spec.ts), executados por [`bin/test.ts`](./bin/test.ts).
- Scripts, exports e publicação: [`package.json`](./package.json); configuração TypeScript: [`tsconfig.json`](./tsconfig.json).
- O loader `@jeftelabs/exec` executa os testes em TypeScript. Mantenha `rewriteRelativeImportExtensions` habilitado no `tsconfig.json` para permitir os imports relativos `.ts` usados pelo projeto.
- Use `npm run quick:test` para rodar apenas os testes, `npm test` para lint e cobertura, `npm run typecheck` para verificar tipos e `npm run build` para compilar o pacote.

Para comportamento detalhado, convenções de alteração e segurança, siga o [`AGENTS.md`](./AGENTS.md) e confirme os scripts no `package.json` antes de executar comandos. A documentação para consumidores começa em [`docs/guides/getting-started.md`](./docs/guides/getting-started.md); a referência completa está em [`docs/references/api.md`](./docs/references/api.md).
