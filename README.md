# NatJus — Classificação de Solicitações de Saúde com Naive Bayes

Projeto acadêmico de **Machine Learning** que lê uma solicitação médica/judicial (PDF, DOCX ou TXT) e sugere em qual categoria ela se encaixa:

| Categoria | Exemplos |
|---|---|
| 💊 **medicamento** | fornecimento de fármaco, insulina, medicamento de alto custo |
| 🏥 **cirurgia** | artroplastia, catarata, transplante, procedimento cirúrgico |
| 🔬 **exame** | ressonância, tomografia, biópsia, exames laboratoriais |
| 🩺 **tratamento** | fisioterapia, hemodiálise, radioterapia, terapia ocupacional |

O contexto é o **NatJus** (Núcleo de Apoio Técnico do Judiciário), que dá suporte técnico a magistrados em ações judiciais de saúde. A classificação automática ajuda a triar os pedidos. **A decisão final continua sendo humana.**

> ⚠️ **Aviso:** o modelo foi treinado com **dados fictícios**, criados para fins acadêmicos. Ele não deve ser usado em processos reais sem treino com dados verdadeiros e validação por especialistas.

---

## ✨ Funcionalidades

- **Leitura de arquivos** PDF, DOCX (incluindo tabelas) e TXT (UTF-8 ou Windows-1252).
- **Pré-processamento** com *stopwords* do português e *stopwords* jurídicas: termos como "requerente", "autor" e "processo" aparecem em todas as categorias e não ajudam a diferenciar. Números (CPF, datas, nº de processo) são ignorados.
- **Vetorização TF-IDF combinada:** palavras (1–2 gramas) + n-gramas de caracteres (3–5), o que lida com variações como *cirurgia / cirúrgico*.
- **Naive Bayes** com comparação entre `MultinomialNB` e `ComplementNB` e ajuste do `alpha` por validação cruzada repetida.
- **Explicabilidade:** termos que mais caracterizam cada categoria e termos do documento que mais pesaram na decisão.
- **Análise por trechos:** o documento é classificado inteiro e parágrafo por parágrafo, o que detecta **pedidos secundários** (ex.: cirurgia + medicamento).
- **Alerta de revisão humana** quando a pontuação é baixa, quando a 1ª e a 2ª categorias estão próximas ou quando há pedidos de outras categorias.
- **Exportação** dos resultados em CSV e do modelo em `.joblib`.
- **Servidor MCP** (Model Context Protocol) que deixa o classificador disponível como ferramenta para assistentes de IA, como o Claude Desktop.

---

## 📊 Resultados (dados fictícios)

Base: **120 exemplos**, 30 por categoria.

| Avaliação | Resultado |
|---|---|
| Teste separado (25% da base) | **86,7%** de acurácia |
| Validação cruzada (base inteira, 5 dobras) | **90,8%** de acurácia |
| F1-macro na validação cruzada do treino (MultinomialNB, α = 0,5) | **90,8%** |

**Por que MultinomialNB e não ComplementNB?** O ComplementNB teve F1 um pouco maior (≈93%), mas as pontuações dele ficam quase sempre em 100% ou 0%. Com isso, o alerta de revisão por baixa confiança deixa de funcionar. O MultinomialNB dá pontuações graduais, que são mais úteis para a triagem.

> Os valores podem variar um pouco conforme a versão do scikit-learn.

---

## 🚀 Como executar

### Opção 1: Google Colab (recomendado)

1. Abra o [Google Colab](https://colab.research.google.com).
2. Vá em **Arquivo → Abrir notebook → Fazer upload** e selecione `NatJus_NaiveBayes_MCP.ipynb`.
3. Vá em **Ambiente de execução → Executar tudo**.
4. Na seção 7, clique em **Escolher arquivos** e envie as solicitações (PDF, DOCX ou TXT).
5. Ao final, o arquivo `resultados_natjus.csv` é baixado automaticamente.

### Opção 2: Localmente (Jupyter)

```bash
git clone https://github.com/<seu-usuario>/<nome-do-repositorio>.git
cd <nome-do-repositorio>
pip install scikit-learn pandas matplotlib pypdf python-docx joblib "fastmcp>=2.10" jupyter
jupyter notebook NatJus_NaiveBayes_MCP.ipynb
```

> A célula de upload (seção 7) usa `google.colab.files` e só funciona no Colab. Localmente, use `classificar_documento(texto)` direto ou a ferramenta `classificar_arquivo` do servidor MCP.

---

## 🗂️ Estrutura do notebook

| Seção | Conteúdo |
|---|---|
| 0 | Instalação e importação das bibliotecas |
| 1 | Base de dados fictícia (120 exemplos) |
| 2 | Pré-processamento (*stopwords*) e definição do pipeline |
| 3 | Treino, ajuste de hiperparâmetros, avaliação e matrizes de confusão |
| 4 | Modelo final e termos mais característicos de cada categoria |
| 5 | Extração de texto de PDF, DOCX e TXT |
| 6 | Classificação com análise por trechos e alerta de revisão |
| 7 | Upload e classificação de arquivos, com exportação em CSV |
| 8 | Salvamento do modelo (`modelo_natjus.joblib`) |
| 9 | Servidor MCP e teste com cliente MCP |

### Exemplo de saída

```
Categoria sugerida: MEDICAMENTO
Pontuação: 85.3%  |  Margem sobre a 2ª: 72.0%

Pontuações por categoria:
  medicamento  ██████████████████████████████████       85.3%
  cirurgia     █████                                    13.3%
  ...

Termos do documento que mais pesaram: fornecimento medicamento, uso continuo, farmacia
Possíveis pedidos secundários: cirurgia

⚠️  ENCAMINHAR PARA REVISÃO HUMANA: há possíveis pedidos de outras categorias: cirurgia
```

---

## 🔌 Servidor MCP

O [Model Context Protocol](https://modelcontextprotocol.io) é um protocolo aberto que permite a assistentes de IA chamar ferramentas externas. O notebook gera o arquivo `servidor_mcp_natjus.py`, que expõe:

| Ferramenta | Descrição |
|---|---|
| `classificar_solicitacao(texto)` | Classifica o texto de uma solicitação |
| `classificar_arquivo(caminho)` | Lê um PDF/DOCX/TXT do disco e classifica |
| `listar_categorias(termos_por_categoria)` | Lista as categorias e os termos mais característicos |

Cada classificação retorna um JSON com categoria, pontuações, margem, pedidos secundários, termos relevantes e o indicador de revisão humana.

### Usando no Claude Desktop

1. Coloque `servidor_mcp_natjus.py` e `modelo_natjus.joblib` na mesma pasta. Os dois são gerados pelo notebook, nas seções 8 e 9.
2. Instale as dependências:
   ```bash
   pip install scikit-learn pypdf python-docx joblib fastmcp
   ```
3. Adicione ao `claude_desktop_config.json`:
   ```json
   {
     "mcpServers": {
       "natjus": {
         "command": "python",
         "args": ["C:/caminho/para/servidor_mcp_natjus.py"]
       }
     }
   }
   ```
4. Reinicie o Claude Desktop e peça, por exemplo: *"Classifique a solicitação do arquivo C:/docs/peticao.pdf"*.

> Use a **mesma versão do scikit-learn** em que o modelo foi treinado para carregar o `.joblib` sem problemas.

---

## 🛠️ Tecnologias

- Python 3
- [scikit-learn](https://scikit-learn.org): TF-IDF, Naive Bayes, validação cruzada
- pandas, NumPy e Matplotlib
- pypdf e python-docx: extração de texto
- joblib: persistência do modelo
- [FastMCP](https://gofastmcp.com): servidor MCP
- Google Colab

---

## ⚠️ Limitações

- **Base pequena e fictícia:** as métricas mostram que o método funciona, mas não dizem como ele se sairia com petições reais.
- **Naive Bayes supõe independência entre as palavras**, e as pontuações não são probabilidades calibradas.
- **PDFs digitalizados** (imagem) precisam de OCR, que não está incluído.
- Uma solicitação pode ter **mais de um pedido**. A análise por trechos sinaliza isso, mas a decisão final é humana.

### Possíveis melhorias futuras

- Treinar com petições reais anonimizadas.
- Adicionar OCR (ex.: Tesseract) para documentos digitalizados.
- Classificação multirrótulo para pedidos com várias categorias.
- Calibração das pontuações (`CalibratedClassifierCV`).

---

## 👥 Autores

- Higor Vieira
- Yuri Lima


