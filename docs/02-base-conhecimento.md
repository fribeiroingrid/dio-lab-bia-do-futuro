# 02. Estratégia de Dados e Base de Conhecimento

## 📂 Dados Utilizados

O ecossistema do Lumi consome dados estruturados e semiestruturados localizados na pasta `data/` para contextualizar as decisões e garantir o ancoramento (*grounding*) das respostas da LLM.

| Arquivo | Formato | Utilização no Agente |
|---------|---------|---------------------|
| `transacoes.csv` | CSV | **Motor Analítico:** Histórico de movimentações reais (entradas e saídas) utilizado para calcular o lucro líquido e mapear "misturas de contas" (categorias marcadas como *pessoal*). |
| `perfil_negocio.json` | JSON | **Memória de Longo Prazo:** Contém os metadados do MEI, faturamento médio estimado, valor do Pró-labore alvo e progresso de metas de expansão do CNPJ. |
| `solucoes_financeiras.json` | JSON | **Catálogo de Recomendações:** Produtos homologados e seguros para o perfil MEI (crédito produtivo, seguros de renda protegida e CDBs de liquidez diária para capital de giro). |
| `historico_atendimento.csv` | CSV | **Treinamento Conversacional:** Logs de interações anteriores para que o agente reconheça padrões de dúvidas recorrentes (DAS, taxas de maquininha, etc.). |
| `guia_tributario_mei.md` | Markdown | **Base Normativa:** Manual consultivo contendo limites legais de faturamento (R$ 81.000), prazos e regras do Simples Nacional para evitar alucinações fiscais. |
| `template_vazio.csv` | CSV | **Onboarding:** Estrutura limpa e padronizada enviada como modelo para novos usuários que ainda não possuem controle financeiro. |
| `input_conversacional` | Texto | **Memória de Curto Prazo:** Dados coletados dinamicamente via chat durante a sessão atual do usuário. |

---

## 🛠️ Adaptações e Expansão dos Dados

Para tornar o agente acessível a quem está começando e não possui planilhas, implementamos a lógica de **"Entrevista Inicial Automatizada"**:

1. Caso o usuário não possua histórico financeiro prévio, o Lumi ativa o modo de onboarding conversacional.
2. À medida que o empreendedor responde perguntas simples no chat (ex: *"ganho R$ 3.000 por mês"* ou *"gasto R$ 500 com luz"*), o script Python captura esses inputs e os estrutura em um dicionário temporário em memória (`input_conversacional`).
3. Ao final da sessão, a aplicação consolida esses dados e gera um arquivo `.csv` inicial personalizado. O usuário pode fazer o download deste arquivo para utilizá-lo como sua primeira ferramenta de controle oficial.

---

## ⚙️ Estratégia de Integração

### Como os dados são carregados?
O agente utiliza um gerenciador de estados (*State Management*) nativo do Streamlit (`st.session_state`). No início da execução do script Python, o sistema verifica a existência e a integridade dos arquivos na pasta `data/`. 

Se a pasta estiver vazia ou os arquivos estiverem zerados, a aplicação injeta dinamicamente um **"Sinalizador de Iniciante"** no contexto da LLM, modificando o fluxo de comportamento para o modo Boas-Vindas e Configuração.

### Como os dados são usados no prompt?
A montagem do prompt para a API do Gemini ocorre de duas formas distintas baseadas no estado do usuário:

*   **Com Histórico Existente:** O script lê o `transacoes.csv` e o `perfil_negocio.json` usando Pandas e injeta no prompt um resumo agregador estruturado (ex: *"O usuário possui um faturamento acumulado de X, lucro líquido médio de Y e cometeu Z misturas de contas pessoais neste mês"*).
*   **Sem Histórico (Iniciante):** O prompt de sistema recebe uma instrução de contingência: *"O usuário é um iniciante absoluto. Não tente analisar dados passados. Concentre sua atuação em ajudá-lo a mapear seus primeiros custos operacionais e estruturar sua precificação básica."*

---

## 📝 Exemplo de Contexto Montado

Abaixo estão os modelos de estruturas de contexto injetadas dinamicamente na LLM conforme o momento da jornada do microempreendedor:

### Cenário A: Usuário Ativo (Com Histórico)
```text
[STATUS: ATIVO]
[CONTEXTO] Usuário possui 6 meses de histórico no arquivo transacoes.csv.
[PERFIL] Setor: Comércio. Faturamento Acumulado: R$ 42.000,00.
[INSIGHT ANALÍTICO] Saldo em caixa positivo constante, mas o campo 'reserva_emergencia' está zerado no perfil_negocio.json.
[DIRETRIZ DE AÇÃO] Priorizar respostas que incentivem a criação do colchão de liquidez empresarial utilizando produtos de 'solucoes_financeiras.json' antes de sugerir novos gastos de expansão.
````
## 💻 Implementação Técnica: Carregamento da Base de Conhecimento

Abaixo está o trecho do código em Python (`src/app.py`) responsável por realizar o mapeamento, leitura e tratamento de erros dos arquivos da pasta `data/`, preparando os dados estruturados para o motor da LLM:

```python
import os
import json
import pandas as pd

def carregar_dados():
    """
    Função do ecossistema do Lumi responsável pelo pipeline de ingestão
    da base de conhecimento local para a memória volátil do agente.
    """
    caminho_perfil = 'data/perfil_negocio.json'
    caminho_transacoes = 'data/transacoes.csv'
    
    perfil = None
    transacoes = None
    
    # 1. Ingestão e Tratamento do Arquivo JSON de Perfil
    if os.path.exists(caminho_perfil):
        try:
            with open(caminho_perfil, 'r', encoding='utf-8') as f:
                perfil = json.load(f)
        except json.JSONDecodeError:
            # Proteção contra arquivos JSON corrompidos ou mal formatados
            perfil = None
            
    # 2. Ingestão e Parser Analítico da Planilha de Transações via Pandas
    if os.path.exists(caminho_transacoes):
        try:
            transacoes = pd.read_csv(caminho_transacoes)
            # Garante a formatação do tipo de dado para evitar erros de agregação
            transacoes['valor'] = pd.to_numeric(transacoes['valor'], errors='coerce')
        except Exception:
            transacoes = None
            
    return perfil, transacoes
