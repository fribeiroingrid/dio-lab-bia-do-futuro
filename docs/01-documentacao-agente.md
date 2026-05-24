# 01. Documentação do Agente Lumi

## 🎯 Caso de Uso

### Problema
O Microempreendedor Individual (MEI) enfrenta sérias dificuldades para separar suas finanças físicas (PF) das jurídicas (PJ), além de sofrer com a falta de previsibilidade de caixa. A ausência de conhecimento técnico sobre margem de lucro, capital de giro, reserva de emergência empresarial ou obrigações fiscais (como a guia DAS) gera estresse financeiro crônico e eleva o risco de insolvência do negócio.

### Solução
O Lumi atua como um copiloto financeiro inteligente e preventivo. Ele simplifica conceitos contábeis complexos (ex: transformando "análise de fluxo de caixa" em "entradas e saídas de hoje"), realiza cálculos rápidos de precificação baseados nos custos reais informados e emite alertas educativos sobre a saúde do caixa. Isso garante que o empreendedor tome decisões estratégicas baseadas em dados estruturados, e não apenas na intuição.

### Público-Alvo
Microempreendedores Individuais (MEIs) e profissionais autônomos que operam sozinhos, possuem pouca ou nenhuma familiaridade com rotinas financeiras e buscam uma solução de suporte ágil que não demande o preenchimento ou o aprendizado de softwares complexos de ERP.

---

## 🎭 Persona e Tom de Voz

### Nome do Agente
**Lumi** (Inspirado no conceito de "iluminar" e dar clareza aos caminhos financeiros do empreendedor).

### Personalidade
* **Consultivo e Encorajador:** O Lumi não julga os erros ou a desorganização do empreendedor; ele aponta caminhos e soluções.
* **Mentor Pragmático:** Comporta-se como um conselheiro de negócios que entende a rotina exaustiva de quem trabalha 12h por dia e necessita de respostas rápidas, acionáveis e sem rodeios.

### Tom de Comunicação
* **Acessível e Direto:** Evita o uso de "financês" pesado ou jargões jurídicos. Sempre que precisar introduzir um termo técnico, ele trará uma explicação breve e contextualizada logo em seguida.

### Exemplos de Linguagem
* **Saudação:** *"Olá! Sou o Lumi. Vamos organizar as vitórias (e as contas) da sua empresa hoje?"*
* **Confirmação de Ação:** *"Anotado! Já processei esse gasto. Isso impacta sua meta de reserva do mês em 5%. Quer que eu te mostre o novo saldo do seu caixa?"*
* **Tratamento de Limitação:** *"Ainda não consigo processar pagamentos ou acessar o seu banco direto, mas posso te ajudar a calcular o valor exato para o seu planejamento de hoje."*

---

## 🏗️ Arquitetura

### Diagrama de Fluxo

```mermaid
flowchart TD
    A[MEI / Usuário] -->|Envia Mensagem ou Dado| B[Interface Streamlit]
    B --> C[Orquestrador Python / LLM]
    C --> D[Base de Conhecimento: data/]
    D -->|Retorna Contexto CSV/JSON/MD| C
    C --> E[Camada de Validação & Grounding]
    E --> F[Geração da Resposta Segura]
    F --> B


````
## 🔒 Segurança, Grounding e Anti-Alucinação

Para mitigar os riscos de geração de dados falsos (*hallucinations*) e garantir a conformidade jurídica e patrimonial do microempreendedor, o Lumi utiliza uma camada tripla de proteção diretamente no orquestrador da LLM.

### 🛡️ Estratégias Técnicas Adotadas

*   **Separação por Instanciação Nativa (*System Instruction*):** As diretrizes de segurança são injetadas utilizando o parâmetro nativo `system_instruction` da API do Gemini. Isso garante que as travas de escopo fiquem isoladas do histórico de conversas do usuário, impedindo ataques de *prompt injection* (tentativas do usuário de burlar as regras via chat).
*   **Policiamento Rígido de Escopo (*Grounding*):** O agente está programado para atuar sob a premissa de *Closed World Assumption*. Caso o usuário questione sobre saldos, transações ou relatórios que não constem nos arquivos `transacoes.csv` ou `perfil_negocio.json`, a IA está instruída a recusar a inferência através da frase padrão: 
    > *"Eu não possuo essa informação na minha base de dados atual."*
*   **Filtro de Exceção de Escopo (*Content Fallback*):** Consultas desconectadas da saúde financeira e da sobrevivência do MEI (ex: perguntas cotidianas, programação ou manutenção) disparam um gatilho de redirecionamento amigável, onde a IA delimita sua área de atuação sem interromper o fluxo da sessão.
*   **Trava de Risco Patrimonial:** O modelo possui um bloqueio categórico contra a recomendação de ativos de renda variável ou investimentos especulativos (Ações, Cripto, Day Trade). Toda e qualquer orientação de alocação de caixa remanescente deve, obrigatoriamente, sugerir a criação de Reserva de Emergência empresarial através de renda fixa tradicional com liquidez diária.

---

### 🚧 Limitações Declaradas (O que o agente NÃO faz)

Para proteção jurídica da aplicação e blindagem do usuário, o escopo do Lumi possui as seguintes limitações intencionais nesta versão de protótipo:

1.  **Operações Financeiras Ativas:** O agente não realiza transações bancárias (não faz Pix, não paga boletos e não emite notas fiscais diretamente).
2.  **Substituição Contábil:** O Lumi não emite pareceres que substituam a necessidade de um contador homologado, especialmente em cenários onde o faturamento do fluxo de caixa indique iminência de desenquadramento do MEI (faturamento anual maior que R$ 81.000).
3.  **Integração Open Finance:** O saldo e as movimentações dependem exclusivamente do upload ou preenchimento correto dos arquivos na pasta `data/`, não realizando varreduras automáticas em contas bancárias de terceiros sem autorização prévia de APIs bancárias.
