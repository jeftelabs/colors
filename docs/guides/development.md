# Desenvolvimento, testes e publicação

## Requisitos e configuração

- O pacote é ESM e usa TypeScript.
- O fluxo de teste usa `@jeftelabs/exec` e exige Node.js 24 ou superior. O `package.json` atual não declara um campo `engines`, portanto esse requisito vem do loader usado pelo comando de testes, e não de uma validação de instalação do pacote.
- `tsconfig.json` estende `@arapucajs/tsconfig/tsconfig.package.json`, usa `rootDir: "./"`, `outDir: "./build"` e habilita `rewriteRelativeImportExtensions`.
- A opção `rewriteRelativeImportExtensions` é necessária porque os imports relativos no código e nos testes incluem `.ts`, e o loader `@jeftelabs/exec` recusa esses imports sem a configuração.
- `kleur` é dependência de runtime; runner, asserções, TypeScript, ESLint, tsdown e ferramentas de cobertura são dependências de desenvolvimento.

## Instalação local

```sh
npm install
```

Para instalações reproduzíveis de CI, use `npm ci`, que respeita o `package-lock.json`.

## Scripts

| Comando | O que faz | Observações |
| --- | --- | --- |
| `npm run quick:test` | Executa `bin/test.ts` no Node com `@jeftelabs/exec`. | Caminho mais rápido para rodar os testes sem cobertura. |
| `npm test` | Executa o `pretest` (`npm run lint`) e então `c8 npm run quick:test`. | Produz cobertura de texto e HTML; `tests/**` é excluído da medição. |
| `npm run lint` | Roda ESLint sobre o repositório. | Também é executado automaticamente antes de `npm test`. |
| `npm run typecheck` | Executa `tsc --noEmit`. | Verifica tipos conforme a configuração herdada. |
| `npm run build` | Chama `npm run compile`. | O lifecycle `precompile` roda lint e limpa `build/`; compile executa tsdown e emite declarações. |
| `npm run clean` | Remove `build/`. | Artefatos gerados são recriados pelo build. |
| `npm run format` | Executa Prettier em todo o repositório. | Escreve arquivos; confira o diff para evitar mudanças não relacionadas. |

## Testes

Os casos em `tests/colors.spec.ts` cobrem os três modos:

- **Raw:** compara a serialização das chamadas e a ordem das transformações.
- **Silent:** verifica que as transformações deixam o texto inalterado.
- **Kleur/ANSI:** compara cada método suportado com o resultado direto da dependência `kleur`.

O runner carrega `tests/**/*.spec.ts` por meio de `bin/test.ts`, que configura `@t8ngs/runner` e registra o plugin `@t8ngs/assert`. Ao adicionar uma transformação, atualize a API base, as implementações e os casos de teste correspondentes.

Comandos de verificação úteis:

```sh
npm run quick:test
npm run typecheck
npm run build
```

`npm test` é a verificação combinada com lint e cobertura.

## Build e pacote publicado

O objeto `tsdown` em `package.json` define `index.ts` e `src/types.ts` como entradas, gera ESM em `build/` e deixa a emissão de declarações para o TypeScript. O campo `files` do pacote inclui `build/` e exclui os testes e o runner. O campo `exports` publica:

- `.` → `./build/index.js` (API runtime default exportada).
- `./types` → `./build/src/types.js` (exports de tipos; o build/declaration gera declarações associadas).

`prepublishOnly` executa o build. `version` também executa o build. Revise os artefatos e o pacote com o processo de release antes de publicar; não edite `build/` diretamente.

## CI e automação de release

Os workflows em `.github/workflows/` têm responsabilidades separadas:

- `checks.yml` valida mensagens de commit com commitlint em pushes para `main`. Apesar do nome, o workflow atual não executa testes, lint nem typecheck.
- `release.yml` usa Release Please em pushes para `main`; quando um release é criado, compila e anexa um arquivo `.tgz` à release do GitHub.
- `deploy.yml` publica a versão em GitHub Packages e npm quando uma release é publicada. Ele instala dependências e executa o build antes de `npm publish`.
- `stale.yml` e `labels.yml` apoiam a gestão de issues/PRs.

Não presuma que CI verifica o código: confira os workflows presentes e os gatilhos antes de mudar o processo.

## Alterações comuns

### Adicionar um método de transformação

1. Adicione método e sobrecargas em `src/base.ts`.
2. Confira se a assinatura protegida e `ColorTransformations` em `src/types.ts` continuam coerentes.
3. As implementações `Kleur`, `Raw` e `Silent` usam o nome da transformação recebido e, em geral, não precisam de branches específicas.
4. Adicione testes para o resultado ANSI, o comportamento silencioso e a representação raw.
5. Atualize `docs/references/api.md` e outros guias se o conjunto de métodos ou o comportamento documentado mudar.
6. Execute verificações adequadas, incluindo `npm run quick:test` e `npm run typecheck`.

### Alterar exports ou build

Alinhe `index.ts`, `package.json` (`exports`, `files`, `tsdown`) e os exemplos da documentação. Confirme que o código de runtime e as declarações geradas representam o mesmo contrato público.

## Publicação e segurança operacional

Os workflows de deploy publicam em registries externos usando credenciais configuradas no GitHub. Não rode `npm publish`, não crie releases e não faça commit/push sem pedido explícito do usuário. Para desenvolvimento local, prefira build, testes e inspeção do pacote sem publicar.
