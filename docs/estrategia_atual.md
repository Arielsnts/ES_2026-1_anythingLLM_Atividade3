# Resumo - Estratégia Atual dos Testes

## Evidências
- O projeto possui pastas dedicadas `__tests__` no `server` e no `collector`, organizadas em subpastas como `/models` (regras de negócio: filas, interpolação de prompts, validação de username) e `/utils` (infraestrutura: SQLConnectors, TextSplitter, agentFlows, agents, chats, files, helpers, middleware, safeJSONStringify, vectorDbProviders/pgvector).
- Toda a suíte usa **Jest (v29.7.0)** com uso intenso de `jest.mock` para isolar Prisma, variáveis de ambiente e rede.
- O `package.json` raiz centraliza a execução com `"test": "jest"`, permitindo rodar tudo com `yarn test`/`npm test` a partir da raiz. Os `package.json` de `server` e `collector` não possuem script `test` próprio.
- Existe um workflow de CI **"Run backend tests"** (`run-tests.yaml`), disparado em Pull Requests, com filtros por path, cancelamento de execuções obsoletas (`cancel-in-progress`), cache triplo de dependências, instalação determinística (`--frozen-lockfile`) e bloqueio (`exit 1`) em caso de falha.
- Existe também um workflow paralelo de **Lint** (`lint.yaml`), cobrindo server, frontend e collector, com a mesma lógica de cache e concorrência.
- **Não há nenhum indicador de cobertura de código**: ausência de badges, Codecov/SonarCloud, flags `--coverage` ou `collectCoverage`. Os logs de CI mostram apenas resultado binário (ex.: 194 testes passados em 5.85s), sem matriz de cobertura.

## Diagnóstico
A estratégia atual é tecnicamente madura na automação e no isolamento de componentes (CI/CD eficiente, cache inteligente, lint paralelo), mas opera de forma **metricamente cega**: o Jest só responde pass/fail, sem nunca medir quanto do código é de fato exercitado pelos testes.

## Risco
**Médio.** O sucesso constante das esteiras de CI gera uma falsa sensação de segurança, já que não há visibilidade sobre quais partes do sistema (agentes, RAG, middlewares, integrações com LLMs) estão de fato protegidas por testes.

## Recomendação
1. Adicionar a flag de cobertura: `"test": "jest --coverage"`.
2. Subir os relatórios de cobertura (`coverage/lcov.info`) para uma ferramenta como o Codecov via novo passo no `run-tests.yaml`.
3. Configurar `coverageThreshold` no Jest como quality gate, bloqueando PRs que reduzam a cobertura mínima estipulada.