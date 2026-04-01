# Código da Aplicação

Esta pasta contém o código do seu agente financeiro.

## Estrutura Sugerida

```
lumi_bot/
├── notebook_lumi.ipynb  # Versão principal para execução no Google Colab
├── agente.py            # Classe principal com a lógica da LLM (Gemini)
├── data_manager.py      # Funções para leitura de CSV e JSON do MEI
└── requirements.txt     # Dependências do projeto
```

## Exemplo de requirements.txt

```
google-generativeai
pandas
python-dotenv
ipython
```

## Como Rodar

```bash
# Instalar dependências
!pip install -q -U google-generativeai pandas

# Rodar a aplicação
streamlit run app.py
```
