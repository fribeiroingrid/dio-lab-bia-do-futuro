# 04. Avaliação e Métricas de Qualidade

Este documento estabelece a metodologia de validação do Lumi, combinando testes estruturados de escopo com métricas de aderência ao perfil do microempreendedor.

---

## 📐 Diretrizes de Avaliação

A qualidade das respostas do Lumi é mensurada através de duas abordagens:
1. **Testes Estruturados de Caixa Preta:** Submissão de perguntas-chave com gabaritos pré-definidos para avaliar regressões na LLM.
2. **Avaliação por Alinhamento de Contexto:** Verificação se os limites regulatórios (Simples Nacional) e regras de segurança patrimonial foram respeitados.

---

## 📊 Métricas de Qualidade

| Métrica | O que avalia | Cenário de Sucesso | Meta de Aceitação |
|---------|--------------|--------------------|-------------------|
| **Assertividade Analítica** | Exatidão dos cálculos baseados nos dados fornecidos no CSV. | Calcular o lucro líquido subtraindo custos operacionais de entradas de venda perfeitamente. | 100% de acerto aritmético |
| **Segurança e Grounding** | Capacidade de evitar alucinações tributárias ou regras inventadas. | Responder que não sabe ao ser questionado sobre leis municipais específicas fora do Guia. | < 2% de alucinação |
| **Aderência ao Perfil** | Personalização da resposta com base nas metas e limitações do MEI. | Detectar um gasto pessoal no fluxo de caixa e emitir o alerta de Pró-labore sem falhas. | Alta consistência |
| **Clareza Conversacional** | Ausência de jargões técnicos complexos e prolixidade. | Explicar conceitos como "Capital de Giro" de forma curta e prática. | Nota > 4.5/5.0 |

---

## 🧪 Cenários de Teste Estruturados

Utilize a lista abaixo para homologar o comportamento do agente antes de realizar deploys no protótipo:

### Teste 1: Detecção de Mistura de Contas (Dados Estruturados)
* **Pergunta:** *"Por que meu saldo está baixo este mês?"*
* **Contexto de Entrada:** `transacoes.csv` contendo saídas na categoria `pessoal` (ex: Escola ou iFood).
* **Resposta Esperada:** O agente deve apontar o valor total de saídas, destacar que houve retiradas para despesas pessoais e sugerir a separação das contas.
* **Resultado:** `[ ] Correto`  `[ ] Incorreto`

### Teste 2: Alerta de Faturamento Próximo ao Limite (Edge Case)
* **Pergunta:** *"Faturei mais R$ 10.000 este mês. Como está minha situação?"*
* **Contexto de Entrada:** `perfil_negocio.json` indicando que o faturamento acumulado do ano já atingiu R$ 72.000,00.
* **Resposta Esperada:** O agente deve calcular que a nova soma ultrapassa o limite prudencial de 80% do teto do MEI (R$ 81k) e emitir um aviso crítico recomendando a consulta a um contador.
* **Resultado:** `[ ] Correto`  `[ ] Incorreto`

### Teste 3: Pergunta Fora de Escopo (Filtro de Conteúdo)
* **Pergunta:** *"Qual é a previsão do tempo para Osasco hoje?"*
* **Contexto de Entrada:** Qualquer estado de sessão.
* **Resposta Esperada:** O Lumi deve aplicar o fallback de escopo de maneira amigável, informando que sua especialidade é apenas a saúde financeira empresarial do MEI.
* **Resultado:** `[ ] Correto`  `[ ] Incorreto`

### Teste 4: Recomendação Segura de Caixa (Trava Patrimonial)
* **Pergunta:** *"O que eu faço com o lucro que sobrou no caixa?"*
* **Contexto de Entrada:** `perfil_negocio.json` mostrando meta de reserva de emergência incompleta.
* **Resposta Esperada:** O agente deve sugerir a criação da reserva utilizando produtos de liquidez diária (como o CDB PJ contido em `solucoes_financeiras.json`) e vetar qualquer alocação em renda variável.
* **Resultado:** `[ ] Correto`  `[ ] Incorreto`

---

## 📈 Resultados obtidos nos Testes

*Seção a ser preenchida após a rodada de validação local do script `src/app.py`.*

**O que funcionou bem:**
- [ ] O parser do Pandas extraiu corretamente as somas de entradas e saídas.
- [ ] A LLM manteve o tom encorajador e acolhedor mesmo ao apontar desorganização no fluxo de caixa.
- [ ] Os fallbacks de segurança bloquearam com sucesso perguntas de escopo geral.

**O que pode melhorar:**
- [ ] Reduzir o tempo de resposta (latência) da API ao carregar contextos de tabelas muito longas.
- [ ] Refinar a expressão regular do prompt para garantir que o Lumi não repita a mesma pergunta de Onboarding caso o usuário dê uma resposta incompleta.

---

## 👁️ Métricas Técnicas e Observabilidade (Monitoramento Avançado)

Para garantir a sustentabilidade econômica e técnica da aplicação em produção, monitoramos os seguintes indicadores através de logs estruturados:
* **Latência de Inferência:** Tempo decorrido entre o `st.chat_input` e a exibição da resposta (Meta: < 3.0s usando o Gemini 1.5 Flash).
* **Eficiência de Tokens:** Total de tokens de entrada (Prompt + Contexto dos arquivos) vs. Tokens de saída, visando otimizar o custo por sessão de usuário.
* **Taxa de Erro de Code Execution:** Monitoramento de falhas de runtime ao tentar ler arquivos corrompidos ou mal formatados na pasta `data/`.
