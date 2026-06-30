# Resumo - Plano de Evolução da Qualidade

A premissa do plano é simples: **testar primeiro o que é mais arriscado, não o que é mais fácil de testar**, evoluindo de forma incremental sem travar o desenvolvimento ativo do projeto.

## 1. Priorização (Top 3)
1. Testes de contrato para provedores de LLM e bancos de dados vetoriais.
2. Testes de integração para o isolamento de dados entre workspaces (risco de privacidade).
3. Testes de caracterização para os módulos God Object/God Switch (`files/index.js` e `getLLMProvider`), como pré-requisito para refatorações seguras.

## 2. Camadas de Foco
A base de testes unitários já existe e tem boa qualidade. O investimento deve se concentrar na **camada de integração e contrato**, que está completamente ausente - é o "meio da pirâmide" que falta. Testes E2E ficam como meta de maturidade posterior, reservados aos fluxos mais críticos (workspace → upload → chat).

## 3. Implantação Gradual
- **Fase 1 (curto prazo)**: ativar `--coverage` no Jest e subir relatórios ao Codecov, apenas para gerar visibilidade, sem bloquear merges ainda.
- **Fase 2 (médio prazo)**: aplicar a regra do "boy scout", todo PR que toca um arquivo sem teste deve adicionar cobertura mínima da função alterada, começando pelos módulos de maior risco combinado.
- **Fase 3 (médio-longo prazo)**: introduzir testes de contrato com mocks para LLMs e bancos vetoriais, reaproveitando o padrão de fixtures já usado em `pgvector/index.test.js`.
- **Fase 4 (longo prazo)**: ativar `coverageThreshold` como quality gate obrigatório e iniciar a suíte de integração/E2E para os fluxos de workspace e RAG.

## 4. Automação
- **Ferramentas**: manter Jest como base; adotar `jest.mock`/`nock` para interceptar chamadas HTTP a LLMs e bancos vetoriais; avaliar Playwright ou Cypress para E2E futuro; usar Codecov (gratuito para repositórios públicos).
- **Pipeline**: estender `run-tests.yaml` com upload de cobertura e gate de bloqueio condicionado a `coverageThreshold` e à presença de testes correspondentes em `server/utils/` e `server/models/`.
- **Política mínima**: nenhum PR que altere lógica de negócio em módulos críticos (providers, chats, MCP, workspaces) deve ser mesclado sem teste associado.

## 5. Critérios de Sucesso
- **Entrada**: infraestrutura mínima de mocking validada (primeiro teste de contrato funcionando local e em CI).
- **Saída**:
  - Cobertura mensurável e visível publicamente via badge;
  - Pelo menos um teste de contrato por categoria de dependência externa (não só `pgvector`);
  - Zero PRs de funcionalidade nova mesclados sem teste correspondente nos últimos N PRs após o gate;
  - Pelo menos um teste de integração validando isolamento entre workspaces;
  - Redução mensurável do tempo entre introdução de uma regressão e sua detecção (hoje, no MCP, só ocorre via Issues de usuários em produção).