# Mini Console Legal (OpenRouter + Llama 3.3) — Classificador de mensagens jurídicas

Este projeto é um **mini console interativo** em Jupyter Notebook para **classificar mensagens típicas de um escritório** (estilo WhatsApp) usando uma LLM via **OpenRouter**.

Você digita uma mensagem e o notebook devolve um **JSON enxuto** com:

- `classe`: `Processual` | `Financeiro` | `Comercial` | `Invalida`
- `justificativa`: explicação curta do porquê aquela classe foi escolhida

> Eu mantive a saída intencionalmente minimalista pra ficar direta e fácil de integrar em pipeline.

---

## Por que isso existe

Em escritórios e departamentos jurídicos, chegam mensagens o tempo todo e nem tudo é “pesquisa jurídica”. Esse console faz a triagem inicial, separando o que é:

- **Processual**: prazos, intimações, audiências, andamento, petições, movimentações
- **Financeiro**: honorários, boletos, pagamentos, parcelamento, nota fiscal, custos
- **Comercial**: proposta, contratação, contrato, escopo, agendamento de reunião
- **Invalida**: mensagem sem conteúdo útil (vazia, só emoji, “kkkk”, ponto, etc.)

---

## Saída (JSON)

Exemplo:

```json
{
  "classe": "Financeiro",
  "justificativa": "A mensagem pergunta sobre percentual de honorários, indicando tema de pagamento/valores."
}
```

---

## Estrutura do projeto

- `Code.ipynb`  
  Notebook principal com:
  - leitura da chave do `.env`
  - chamada à API do OpenRouter
  - prompt com **contrato de JSON mínimo**
  - exemplos (few-shot) para estabilizar a classificação
  - parse robusto e validações
  - modo interativo (console)


  Arquivo com a chave do OpenRouter (**não deve ser versionado**).

---

## Requisitos

- Python 3.10+ (funciona em 3.11/3.12 também)
- Jupyter Notebook ou JupyterLab
- Bibliotecas:
  - `requests`
  - `python-dotenv`

Instalação:

```bash
pip install requests python-dotenv
```

### Nota para revisão (chave temporária)
Para facilitar revisão, foi disponibilizada uma **chave temporária** com **limite de US$0** (sem custo) válida por **até 7 dias**.

---

## Como rodar

1) Entre na pasta do projeto:

```bash
cd "/caminho/do/projeto"
```

2) Rode o Jupyter:

```bash
jupyter notebook
```

3) Abra `Code.ipynb` e execute as células.

4) Quando o console iniciar, digite mensagens e veja o JSON retornar.

Para sair: `sair`

---

## Exemplos para testar rápido

**Processual**
- `saiu intimação no proc 1234? qual o prazo?`

**Financeiro**
- `os honorários ficam em 20 por cento?`

**Comercial**
- `vocês atendem contrato mensal? manda proposta`

**Invalida**
- `🤔`
- `kkkkk`
- `   `
- `.`

---

## Como o pipeline funciona (explicação curta)

1. **Entrada**: você digita uma mensagem  
2. **Prompt**: o modelo é instruído a:
   - escolher **uma** classe entre as quatro
   - escrever uma justificativa curta
   - responder **somente JSON**
3. **Chamada**: o notebook envia `system + user` para o endpoint de chat completions do OpenRouter  
4. **Parse**: tenta `json.loads`; se vier texto a mais, extrai o bloco `{...}`  
5. **Validação**:
   - garante que `classe` está nas categorias permitidas
   - garante que `justificativa` é texto
6. **Saída**: imprime o JSON final no console

---

## Próximos passos (se eu fosse evoluir)

- log em `.jsonl` com timestamp
- modo “batch” (lista de mensagens)
- API local (FastAPI) para integração com frontend
- RAG para respostas com base documental (quando houver corpus)
