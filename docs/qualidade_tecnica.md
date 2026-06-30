# Resumo - Qualidade Técnica dos Testes

## Visão Geral
A única pasta de teste documentada de forma aprofundada é `server/__tests__/utils/vectorDbProviders/pgvector/index.test.js` (78 linhas, 9 casos), validando a função `sanitizeForJsonb`. Em um projeto com mais de 1.764 commits, 195 contribuidores, 40 provedores de LLM e 10 bancos vetoriais, essa é a suíte mais formalmente bem construída encontrada, mas extremamente localizada. O `CONTRIBUTING.md` exige testes para todo bug fix/feature, porém PRs como o #3077 (Agent Builder) e o #5071 (auto-mergeado em 12h com checklist incompleto) mostram que essa regra não é aplicada de forma consistente.

## Evidências
- **Nomenclatura**: os 9 casos de teste (`returns null/undefined as-is`, `deeply sanitizes objects`, `does not mutate original objects/arrays`, etc.) descrevem comportamento observável, não detalhes de implementação, combinado com o `describe("PGVector.sanitizeForJsonb")`, segue a convenção de leitura fluente do Jest.
- **Isolamento e reprodutibilidade**: a instância testada é criada uma única vez no escopo do arquivo, mas como a função é pura (sem efeitos colaterais), cada `it` é independente. Não há `beforeEach`/`afterEach`. Os inputs são literais e determinísticos (sem rede, relógio ou randomização).
- **Mocks e fixtures**: não há uso de mocks, o que é apropriado já que a função testada é uma utilidade pura. Os dados de teste cobrem casos limítrofes relevantes (caracteres Unicode de controle `\u0000`–`\u001F`, objetos aninhados em 3 níveis).

## Diagnóstico
Dentro do escopo testado, a qualidade técnica é alta: nomes claros, testes isolados, reprodutíveis e com fixtures bem construídas. O problema não está no teste em si, mas no que ele **não representa**: não há nenhuma evidência de como a equipe lidaria com mocking de dependências externas (LLMs, bancos vetoriais), padrão necessário para testar os componentes mais críticos do sistema.

## Risco
**Alto.** Não pela qualidade do teste analisado, mas pela assimetria entre a cobertura existente (uma função utilitária) e a superfície de risco real do sistema: roteamento entre 40 provedores de LLM, pipeline de RAG, sistema de agentes e módulo MCP seguem sem qualquer teste. Some-se a isso a ausência de precedente de mocking para I/O externo e a política de testes do `CONTRIBUTING.md` sistematicamente contornada.

## Recomendação
1. Priorizar cobertura pelos pontos estruturalmente críticos (God Objects `systemSettings.js`, `updateENV.js`, e os 9 arquivos tocados ao adicionar um novo provedor de LLM).
2. Estabelecer um padrão reutilizável de mocking para LLMs e bancos vetoriais, reaproveitando o estilo de fixtures já usado em `pgvector/index.test.js`.
3. Criar testes de contrato para validar a interface mínima que todo provedor de LLM deve cumprir.
4. Configurar gate de CI que bloqueie PRs em `server/utils/` ou `server/models/` sem testes correspondentes.
5. Adotar a regra do "boy scout": todo PR que altera um arquivo sem teste deve adicionar cobertura mínima da função alterada.
6. Instituir revisão obrigatória de segundo nível para PRs de grande porte, verificando presença de testes antes da aprovação.