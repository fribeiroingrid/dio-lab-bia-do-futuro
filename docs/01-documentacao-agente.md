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
