# LUMI - Agente de IA: Gestão Financeira para MEI

## Contexto
O LUMI redefine a relação do microempreendedor com suas finanças, evoluindo de uma planilha estática para um agente consultivo inteligente. Para o MEI, que muitas vezes opera sozinho, o LUMI atua como um "co-fundador analítico" focado em:

- **Antecipar necessidades** ao invés de apenas responder perguntas
- **Personalizar** sugestões com base no contexto de cada cliente
- **Cocriar soluções** financeiras de forma consultiva
- **Garantir segurança** e confiabilidade nas respostas (anti-alucinação)
- **Educação Financeira Aplicada** Não apenas registra gastos, mas educa o empreendedor sobre a importância da separação entre contas PF (Pessoa Física) e PJ (Pessoa Jurídica) em tempo real.
- **Proatividade Normativa** Antecipa obrigações fiscais (como o DAS-MEI) e monitora o teto de faturamento anual para evitar o desenquadramento surpresa.
-**Gestão de Sobrevivência e Crescimento** Transforma dados brutos de fluxo de caixa em insights sobre a saúde do negócio, ajudando a definir metas de/ lucro reais.
-**Conformidade** Garante que todas as orientações estejam alinhadas com as diretrizes vigentes da Receita Federal, mitigando riscos de interpretações equivocadas da lei.


---
### 1. Documentação do Agente

-**Entrada:** O usuário envia dados de vendas ou dúvidas via chat.
-**Processamento:** A LLM utiliza RAG (Retrieval-Augmented Generation) para consultar a legislação MEI vigente e a base de gastos do usuário.
-**Memória:** Armazenamento em banco vetorial para lembrar do contexto de faturamento mensal.
-**Segurança:** Grounding: Respostas baseadas estritamente em documentos oficiais da Receita Federal.
-**Filtros de Saída:** Bloqueio de recomendações de investimentos de alto risco, focando apenas em gestão de caixa.


- **Caso de Uso:** Qual problema financeiro ele resolve? (ex: consultoria de investimentos, planejamento de metas, alertas de gastos)
- **Persona e Tom de Voz:** Como o agente se comporta e se comunica?
- **Arquitetura:** Fluxo de dados e integração com a base de conhecimento
- **Segurança:** Como evitar alucinações e garantir respostas confiáveis?
- **Cálculo** de obrigações (DAS-MEI).
- **Gestão de fluxo de caixa** e alertas de teto de faturamento anual.
- **Persona e Tom de Voz** O LUMI é um mentor pragmático e encorajador. Ele se comunica de forma clara, sem "economês" excessivo, mas mantém o profissionalismo. É direto ao ponto, mas acolhedor para quem está começando a empreender.


📄 **Template:** [`docs/01-documentacao-agente.md`](./docs/01-documentacao-agente.md)

---

### 2. Base de Conhecimento

Utilização dos **dados mockados** disponíveis na pasta [`data/`](./data/) para alimentar o agente:

| Arquivo | Formato | Descrição |
|---------|---------|-----------|
| `transacoes.csv` | CSV | Histórico de transações do cliente |
| `historico_atendimento.csv` | CSV | Histórico de atendimentos anteriores |
| `perfil_investidor.json` | JSON | Perfil e preferências do cliente |
| `produtos_financeiros.json` | JSON | Produtos e serviços disponíveis |


📄 **Template:** [`docs/02-base-conhecimento.md`](./docs/02-base-conhecimento.md)

---

### 3. Prompts do Agente

Documente os prompts que definem o comportamento do agente:

- **System Prompt:** Você é o LUMI, um assistente virtual especializado em finanças para MEIs no Brasil. Seu objetivo é ajudar o usuário a organizar o fluxo de caixa e garantir que ele não ultrapasse o limite de faturamento anual. Você deve sempre incentivar a separação das contas PF e PJ. Se o usuário perguntar algo fora do escopo financeiro empresarial, redirecione-o gentilmente.
- **Exemplos de Interação:** Usuário: "Ganhei R$ 5.000,00 este mês. Posso gastar tudo?"
LUMI: "Parabéns pelo faturamento! Antes de gastar, lembre-se de separar a reserva para o boleto DAS (aprox. R$ 70-75) e a reserva de emergência da sua empresa. Recomendo separar ao menos 30% para reinvestimento."
- **Tratamento de Edge Cases:** Faturamento Próximo ao Limite: Se o usuário reportar ganhos que somem mais de R$ 81.000 no ano, o LUMI dispara um alerta imediato sobre o desenquadramento e a necessidade de um contador.

📄 **Template:** [`docs/03-prompts.md`](./docs/03-prompts.md)

---

### 4. Aplicação Funcional

Desenvolva um **protótipo funcional** do seu agente:

- Chatbot interativo (sugestão: Streamlit, Gradio ou similar)
- Integração com LLM (Gemini 1.5 Pro)
- Conexão com a base de conhecimento: Arquivos PDF com o Guia do MEI e planilhas CSV de histórico de transações,

📁 **Pasta:** [`src/`](./src/)

---

### 5. Avaliação e Métricas

Qualidade do seu agente:

- Precisão/assertividade das respostas
- Taxa de respostas seguras (sem alucinações)
- Coerência com o perfil do cliente
- 
| Métrica | Descrição | Meta |
|---------|---------|-----------|
| `Precisão Fiscal` | Exatidão no cálculo de impostos e limites de faturamento. | 100% |
| `Taxa de Alucinação` | Frequência com que o agente inventa regras tributárias. | < 2% |
| `Retenção de Contexto` | Capacidade de lembrar o faturamento dos meses anteriores na mesma sessão. | Alta |
| `Clareza de Resposta` | Avaliação (1-5) se o MEI entendeu a orientação sem termos complexos. | > 4.5 |

📄 **Template:** [`docs/04-metricas.md`](./docs/04-metricas.md)

---

### 6. Pitch

Grave um **pitch de 3 minutos** (estilo elevador) apresentando:

- Qual problema seu agente resolve?
- Como ele funciona na prática?
- Por que essa solução é inovadora?

📄 **Template:** [`docs/05-pitch.md`](./docs/05-pitch.md)

---

## Ferramentas Sugeridas

Todas as ferramentas abaixo possuem versões gratuitas:

| Categoria | Ferramentas |
|-----------|-------------|
| **LLMs** | [ChatGPT](https://chat.openai.com/), [Copilot](https://copilot.microsoft.com/), [Gemini](https://gemini.google.com/), [Claude](https://claude.ai/), [Ollama](https://ollama.ai/) |
| **Desenvolvimento** | [Streamlit](https://streamlit.io/), [Gradio](https://www.gradio.app/), [Google Colab](https://colab.research.google.com/) |
| **Orquestração** | [LangChain](https://www.langchain.com/), [LangFlow](https://www.langflow.org/), [CrewAI](https://www.crewai.com/) |
| **Diagramas** | [Mermaid](https://mermaid.js.org/), [Draw.io](https://app.diagrams.net/), [Excalidraw](https://excalidraw.com/) |

---

## Estrutura do Repositório

```
📁 lab-agente-financeiro/
│
├── 📄 README.md
│
├── 📁 data/                          # Dados mockados para o agente
│   ├── historico_atendimento.csv     # Histórico de atendimentos (CSV)
│   ├── perfil_investidor.json        # Perfil do cliente (JSON)
│   ├── produtos_financeiros.json     # Produtos disponíveis (JSON)
│   └── transacoes.csv                # Histórico de transações (CSV)
│
├── 📁 docs/                          # Documentação do projeto
│   ├── 01-documentacao-agente.md     # Caso de uso e arquitetura
│   ├── 02-base-conhecimento.md       # Estratégia de dados
│   ├── 03-prompts.md                 # Engenharia de prompts
│   ├── 04-metricas.md                # Avaliação e métricas
│   └── 05-pitch.md                   # Roteiro do pitch
│
├── 📁 src/                           # Código da aplicação
│   └── app.py                        # (exemplo de estrutura)
│
├── 📁 assets/                        # Imagens e diagramas
│   └── ...
│
└── 📁 examples/                      # Referências e exemplos
    └── README.md
```

---

## Dicas Finais

1. **Comece pelo prompt:** Um bom system prompt é a base de um agente eficaz
2. **Use os dados mockados:** Eles garantem consistência e evitam problemas com dados sensíveis
3. **Foque na segurança:** No setor financeiro, evitar alucinações é crítico
4. **Teste cenários reais:** Simule perguntas que um cliente faria de verdade
5. **Seja direto no pitch:** 3 minutos passam rápido, vá ao ponto
