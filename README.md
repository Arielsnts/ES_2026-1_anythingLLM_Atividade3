# Auditoria Forense de Software
## Projeto: AnythingLLM

Este relatório apresenta uma auditoria da estratégia de testes de software do projeto AnythingLLM, com o objetivo de diagnosticar a existência, qualidade, cobertura e relevância dos testes automatizados adotados pelo projeto. A partir da análise são identificados riscos, lacunas e fragilidades na abordagem atual de qualidade, culminando em um plano de evolução baseado em boas práticas de teste.

---
## Informações Gerais
- Projeto analisado: [AnythingLLM](https://github.com/Mintplex-Labs/anything-llm)
- Vídeo da apresentação: [clique aqui]

---
## Integrantes da Equipe
- Ariel Santos Souza – 
- Emilly Kathellen de Jesus Oliveira – 
- Giovani Barros Luna Gomes – 
- Karol Sthefanny da Cruz Menezes – 
- Nycolly Souza Sena – 
- Sara Cristina Santos Chagas – 

Orientador: Glauco de Figueiredo Carneiro  

---
## Metodologia
A análise foi realizada com base em:
- **Evidência**: prints, links, trechos de código, pipelines, arquivos de configuração e issues do repositório
- **Diagnóstico**: leitura crítica da equipe sobre o que as evidências revelam
- **Risco**: classificação em Baixo, Médio ou Alto, com justificativa técnica
- **Recomendação**: proposta objetiva de melhoria, priorizada por impacto

---

## Áreas Analisadas

- [Estratégia Atual de Testes](/docs/estrategia_atual.md)
- [Qualidade Técnica dos Testes](/docs/qualidade_tecnica.md)
- [Lacunas, Riscos e Falhas Potenciais](/docs/lacunas.md)

- Para a auditoria completa: [clique aqui]

---


## Plano de Evolução

Propõe-se a seguir um plano de evolução realista e priorizado, que parte da premissa de que a maturidade em testes não se constrói de uma vez, mas de forma incremental e direcionada pelo risco real do sistema.

[Ver Plano de Evolução](/docs/plano_de_evolucao.md)

---

## Estrutura do Repositório

Este repositório foi organizado para permitir a replicação da análise.

- /docs → Resumos e Documento completo da auditoria
- /evidencias → Registros e exemplos coletados do GitHub

---

## Como Reproduzir a Análise

1. Clone ou acesse o repositório original do AnythingLLM no GitHub.
2. Vá em /docs e leia os resumos na ordem: estratégia atual → qualidade técnica → lacunas/riscos → plano de evolução.
3. Para cada resumo, abra a pasta /evidencias correspondente e confira os prints, trechos de 4. código e links citados, comparando com o que está no repositório atual.
4. Repita esse processo (resumo → evidência) para cada um dos quatro eixos, validando se os arquivos, workflows e issues ainda apresentam o mesmo comportamento descrito.
5. Caso queira aprofundar algum ponto, consulte o documento completo da auditoria em /docs, que detalha diagnóstico, risco e recomendação de cada achado.

## Referências

- AnythingLLM: https://github.com/Mintplex-Labs/anything-llm
- Gemini: https://gemini.google.com/
- Claude: https://claude.ai
