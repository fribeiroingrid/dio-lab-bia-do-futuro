# Base de Conhecimento

## Dados Utilizados

Descreva se usou os arquivos da pasta `data`, por exemplo:

| Arquivo | Formato | Utilização no Agente |
|---------|---------|---------------------|
|`template_vazio.csv` | CSV | Estrutura padrão para novos usuários começarem a registrar gastos.|
| `fluxo_caixa.csv` | CSV | Registro de entradas e saídas (receita vs. despesas operacionais), para usuarios com histórico. |
| `perfil_negocio.json` | JSON | Dados do MEI: setor (serviços/comércio), faturamento anual e metas de reserva. |
| `guia_tributario_mei.md` |Markdown | Base de consulta para dúvidas sobre DAS, nota fiscal e prazos. |
| `metas_saude_financeira.json` | JSON | Objetivos do usuário (ex: criar reserva de emergência, comprar equipamento). |
| `dicionario_mei.json` | JSON | Base de conhecimento estática com regras do Simples Nacional e MEI. |
| `input_conversacional` | Texto (Memória) | Dados coletados via chat para usuários sem histórico (onboarding). |
| `exemplo_demonstrativo.csv` | CSV | Dados fictícios para o usuário testar as funcionalidades de análise. |



---

## Adaptações nos Dados

> Você modificou ou expandiu os dados mockados? Descreva aqui.

O agente ativa o modo "Entrevista Inicial".

Os dados fornecidos no chat (ex: "ganho 3 mil por mês", "gasto 500 com luz") são estruturados em um dicionário temporário que simula a base de dados.

O usuário tem a opção de baixar esses dados ao final da sessão para usá-los como sua primeira planilha oficial.

---

## Estratégia de Integração

### Como os dados são carregados?
> Descreva como seu agente acessa a base de conhecimento.

O agente utiliza um Gerenciador de Estado (State Management). No início da sessão, o Python verifica a existência de arquivos na pasta data/. Se vazia, o sistema injeta um "Sinalizador de Iniciante" no contexto da LLM, disparando o fluxo de boas-vindas e configuração.

### Como os dados são usados no prompt?
> Os dados vão no system prompt? São consultados dinamicamente?

Se houver dados: O prompt inclui o resumo do CSV (ex: "Seu lucro médio é X").

Se não houver dados: O prompt recebe uma instrução de sistema: "O usuário é iniciante. Não tente analisar o passado; ajude-o a mapear seus primeiros custos operacionais e precificar seu serviço."

---

## Exemplo de Contexto Montado

> Mostre um exemplo de como os dados são formatados para o agente.

```

[STATUS: ATIVO]
[CONTEXTO] Usuário possui 6 meses de histórico. 
[INSIGHT] Saldo positivo constante, mas sem reserva de emergência.
[AÇÃO] Sugerir criação de reserva antes de novos investimentos.

[STATUS: ONBOARDING]
[CONTEXTO] Usuário acabou de abrir o MEI. Não possui registros.
[INSIGHT] Precisa de ajuda com precificação e separação de contas (PF/PJ).
[AÇÃO] Fazer perguntas curtas: 1. Qual seu nicho? 2. Qual sua meta de renda mensal?

...
```
