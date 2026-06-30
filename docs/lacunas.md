# Resumo - Lacunas, Riscos e Falhas Potenciais

## Evidências
- **Fluxos críticos de negócio sem testes**: não existe nenhum teste de integração, sistema ou E2E que valide a criação de workspaces, o isolamento de vetores durante a ingestão ou a segregação de contexto no momento em que o servidor monta o prompt para a LLM (`server/utils/chats/stream.js`).
- **Módulos de risco arquitetural sem cobertura**: o God Object `server/utils/files/index.js` e o God Switch `getLLMProvider` (violação do Open/Closed Principle) não possuem nenhum teste unitário ou de integração documentado.
- **Dependências externas descobertas**: dos 30+ provedores de LLM e 9 implementações de banco vetorial, apenas o `pgvector` tem uma pasta de teste, e ainda assim cobrindo só a função `sanitizeForJsonb`, não a conexão real nem as queries. Não há uso de bibliotecas como `nock` para simular respostas de APIs externas.
- **Ausência de testes E2E**: não há Cypress, Playwright, Selenium ou Puppeteer configurados; nenhuma pasta `cypress/e2e/`, `tests/e2e/` ou `integration/`; o pipeline de CI nunca sobe servidor e frontend simultaneamente para validar uma jornada completa.
- **Módulo MCP sem nenhuma linha de teste**: busca pelo termo "MCP" em `server/__tests__/` não retorna resultados, apesar de haver falhas documentadas em produção (Issue #4579, `TypeError: Cannot convert undefined or null to object` no boot dos servidores MCP em Docker; PR #4299, condições de corrida e vazamentos de memória no `MCPHypervisor`).

## Diagnóstico
A suíte de testes protege bem um núcleo estreito de funções utilitárias puras e deixa descoberto exatamente o que é mais complexo, mais acoplado e mais sujeito a falhas reais. O padrão observado é o de uma **pirâmide de testes invertida**: boa base unitária, mas sem a camada intermediária de integração e sem o topo de testes E2E. Isso afeta três camadas: (1) fluxo crítico de negócio (RAG, workspaces, ingestão), (2) dívida técnica arquitetural (God Object e God Switch sem rede de segurança) e (3) dependências externas (LLMs e bancos vetoriais quase sem cobertura).

## Risco
**Alto.** Os fatores se amplificam mutuamente: refatorar `getLLMProvider` sem testes pode quebrar silenciosamente integrações; alterações no streaming de chat podem vazar contexto entre workspaces (risco de privacidade); falhas em APIs de terceiros só seriam percebidas em produção; e já há precedente documentado de merges sem checklist de testes completo (PR #5071), apesar da exigência formal do `CONTRIBUTING.md`.

## Recomendação
1. Testes de contrato para LLMs e bancos vetoriais, usando mocks para simular respostas, timeouts e erros.
2. Testes de integração para validar isolamento de contexto entre workspaces durante a busca por similaridade.
3. Testes de caracterização antes de refatorar `files/index.js` e `getLLMProvider`, documentando o comportamento atual como rede de segurança mínima.
4. Priorizar novos testes por risco combinado (frequência de alteração × acoplamento × dependência externa), não por meta de cobertura percentual.
5. Criar fixtures/mocks estáticos (JSON) para simular respostas de OpenAI, Pinecone etc.
6. Validar barreiras de multi-tenancy com testes que garantam que o Workspace B nunca acessa dados do Workspace A.
7. Integrar esses testes como quality gate no GitHub Actions, bloqueando merges que quebrem os fluxos simulados.