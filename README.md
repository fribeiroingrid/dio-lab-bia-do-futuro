# Lumi - Agente de IA: Gestão Financeira para MEI 🚀

## 📋 Contexto

O **Lumi** redefine a relação do microempreendedor com suas finanças, evoluindo de uma planilha estática para um agente consultivo inteligente. Para o MEI, que muitas vezes opera sozinho, o Lumi atua como um copiloto focado em:

*   **Educação Financeira Aplicada:** Não apenas registra gastos, mas educa o empreendedor sobre a importância da separação entre contas PF (Pessoa Física) e PJ (Pessoa Jurídica) em tempo real.
*   **Proatividade Normativa:** Antecipa obrigações fiscais (como o vencimento da guia DAS-MEI) e monitora o teto de faturamento anual para evitar o desenquadramento surpresa.
*   **Gestão de Sobrevivência e Crescimento:** Transforma dados brutos de fluxo de caixa em insights sobre a saúde do negócio, auxiliando na definição de um Pró-labore justo e na criação de capital de giro.
*   **Conformidade e Anti-alucinação:** Garante que todas as orientações estejam estritamente alinhadas com as diretrizes vigentes da Receita Federal, mitigando riscos de interpretações equivocadas da lei.

---

### 1. Documentação do Agente

*   **Caso de Uso:** O agente resolve a desorganização financeira e a mistura de patrimônio (PF/PJ). Ele realiza a análise preditiva do fluxo de caixa, emite alertas educativos ao detectar despesas pessoais pagas com recursos da empresa e gerencia o progresso de metas de curto/longo prazo.
*   **Persona e Tom de Voz:** O LUMI atua como um mentor pragmático, encorajador e acolhedor. Sua comunicação é direta, sem "economês" ou jargões jurídicos excessivos, tornando os conceitos contábeis acessíveis a quem está começando a empreender.
*   **Arquitetura:** O fluxo de dados opera sob uma estrutura RAG (*Retrieval-Augmented Generation*). 
    *   **Entrada:** Dúvidas ou novos lançamentos enviados via chat.
    *   **Processamento:** A LLM cruza os dados do histórico transacional com as regras de negócio parametrizadas.
    *   **Base de Conhecimento:** Arquivos de texto estruturados com a legislação MEI e guias práticos de tributação.
*   **Segurança:** Implementação de técnicas de *Grounding* para blindar o agente contra alucinações. O sistema possui filtros de saída rígidos que bloqueiam recomendações de investimentos de alto risco ou especulativos, direcionando o foco do MEI exclusivamente para a liquidez e proteção do caixa.

📄 *Mais detalhes em:* `docs/01-documentacao-agente.md`

---

### 2. Base de Conhecimento

Utilização dos dados estruturados disponíveis na pasta `data/` para alimentar o contexto do agente:

| Arquivo | Formato | Descrição | Utilização no Agente |
|---------|---------|-----------|----------------------|
| `transacoes.csv` | CSV | Histórico de fluxo de caixa do cliente (entradas e saídas). | Utilizado pelo motor analítico para calcular o lucro líquido e identificar mistura de contas. |
| `perfil_negocio.json` | JSON | Dados cadastrais do MEI, metas financeiras e Pró-labore alvo. | Funciona como a memória de longo prazo para personalizar os insights e alertas do usuário. |
| `solucoes_financeiras.json` | JSON | Catálogo de produtos e serviços disponíveis (ex: microcrédito, CDB de liquidez diária, seguros). | Base de dados consultada pela IA para recomendar soluções de proteção ou alocação de caixa. |
| `historico_atendimento.csv` | CSV | Logs de interações e dúvidas anteriores do microempreendedor. | Utilizado para manter a consistência do atendimento e identificar temas recorrentes (taxas, DAS). |
| `guia_tributario_mei.md` | Markdown | Legislação oficial, regras do Simples Nacional e limites de faturamento. | Base de conhecimento estática para garantir respostas 100% seguras sobre obrigações fiscais. |

📄 *Mais detalhes em:* `docs/02-base-conhecimento.md`

---

### 3. Prompts do Agente

*   **System Prompt:**
    ```text
    Você é o LUMI, um assistente virtual especialista em finanças para MEIs no Brasil. Seu objetivo é ajudar o usuário a organizar o fluxo de caixa e garantir a sobrevivência do negócio. Você deve monitorar rigorosamente o limite de faturamento anual (R$ 81.000) e alertar o usuário sempre que ele utilizar o caixa PJ para despesas pessoais (PF). Mantenha um tom educativo, empático e livre de termos técnicos complexos.
    ```
*   **Exemplos de Interação:**
    *   **Usuário:** *"Faturei R$ 5.000,00 este mês. Posso transferir tudo para minha conta pessoal?"*
    *   **LUMI:** *"Parabéns pelo faturamento! Antes de transferir, lembre-se de que a empresa possui custos fixos e a guia do DAS (aprox. R$ 70-75) para pagar. O ideal é definirmos um Pró-labore fixo para você e manter o restante no caixa como capital de giro. Quer que eu te ajude a calcular um valor seguro para essa retirada?"*
*   **Tratamento de Edge Cases:** Se o volume acumulado de entradas indicado no `transacoes.csv` atingir 80% do limite legal anual, o LUMI interrompe as orientações de rotina e dispara um alerta crítico recomendando formalmente o suporte de um contador para o processo de desenquadramento.

📄 *Mais detalhes em:* `docs/03-prompts.md`

---

### 4. Aplicação Funcional

O protótipo do ecossistema do agente foi desenvolvido contendo:
*   **Chatbot Interativo:** Interface construída em **Streamlit**, permitindo o diálogo fluido e a visualização rápida da saúde do fluxo de caixa.
*   **Integração com LLM:** Processamento nativo via API do **Gemini 1.5 Pro** para orquestração de chamadas e inferência de intenções.
*   **Conexão com a Base:** Integração via Python utilizando `Pandas` para manipulação analítica das planilhas e arquivos JSON da pasta `data/`.

📁 *Código-fonte em:* `src/`

---

### 5. Avaliação e Métricas

| Métrica | Descrição | Meta |
|---------|-----------|------|
| **Precisão Fiscal** | Exatidão no cálculo de impostos, prazos e regras do Simples Nacional. | 100% |
| **Taxa de Alucinação** | Frequência com que o agente inventa dados ou regras não contidas nas bases. | < 2% |
| **Retenção de Contexto** | Capacidade de correlacionar lançamentos anteriores na mesma sessão de chat. | Alta |
| **Clareza de Resposta** | Avaliação de legibilidade e ausência de termos complexos (escala 1-5). | > 4.5 |

📄 *Mais detalhes em:* `docs/04-metricas.md`

---

### 6. Pitch

Apresentação executiva detalhando:
1.  **O Problema:** A alta taxa de mortalidade das microempresas no Brasil por falta de capital de giro e confusão patrimonial.
2.  **A Solução:** Como o LUMI transforma dados frios de planilhas em tomada de decisão em tempo real na linguagem do empreendedor.
3.  **O Diferencial:** O foco em RAG preventivo focado em sobrevivência de negócios e não em investimentos tradicionais de varejo.

📄 *Mais detalhes em:* `docs/05-pitch.md`

---

## 🛠️ Ferramentas Utilizadas

*   **LLMs & Processamento:** Google Gemini 1.5 Pro
*   **Ambiente de Desenvolvimento:** Python 3, Streamlit, Google Colab
*   **Manipulação de Dados:** Pandas, JSON Library
*   **Arquitetura & Fluxos:** Mermaid (Diagramas de sequência)

---

## 📁 Estrutura do Repositório
```text
📁 Lumi-Agente-projeto-DIO/
│
├── 📄 README.md                      # Documentação principal
│
├── 📁 data/                          # Dados de entrada do agente
│   ├── transacoes.csv                # Histórico de fluxo de caixa (CSV)
│   ├── perfil_negocio.json           # Perfil cadastral e metas do MEI (JSON)
│   ├── solucoes_financeiras.json     # Catálogo de produtos/crédito (JSON)
│   ├── historico_atendimento.csv     # Logs de suporte anteriores (CSV)
│   └── guia_tributario_mei.md        # Base normativa em Markdown
│
├── 📁 docs/                          # Detalhamento de engenharia
│   ├── 01-documentacao-agente.md     # Casos de uso e arquitetura
│   ├── 02-base-conhecimento.md       # Estratégia e mapeamento de dados
│   ├── 03-prompts.md                 # Engenharia de prompts e contextos
│   ├── 04-metricas.md                # Indicadores e avaliação de qualidade
│   └── 05-pitch.md                   # Roteiro e estrutura do Pitch
│
├── 📁 src/                           # Código da aplicação
│   └── app.py                        # Script do protótipo funcional em Streamlit
│
└── 📁 assets/                        # Diagramas e mídias do repositório

## Estrutura do Repositório

```
