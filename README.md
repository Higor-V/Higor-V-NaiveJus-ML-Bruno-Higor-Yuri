# Higor-V-NaiveJus-ML-Bruno-Higor-Yuri

# Classificação de demandas judiciais de saúde — MVP com Naive Bayes

> **Projeto acadêmico inspirado no contexto do e-NatJus.** O objetivo é demonstrar, em escala didática, como um classificador de texto pode organizar solicitações em quatro categorias. Este repositório não é oficial, não possui integração com o CNJ/e-NatJus e não toma decisões médicas ou jurídicas.

## 2. Descrição do objetivo e do problema

### Problema
Demandas judiciais relacionadas à saúde podem conter pedidos de natureza diferente, como fornecimento de medicamentos, realização de cirurgias, execução de exames e acesso a tratamentos. A leitura e a organização manual desses textos podem exigir tempo e dificultar a triagem inicial de grandes volumes de documentos.

O Conselho Nacional de Justiça descreve o e-NatJus como uma plataforma que reúne notas técnicas e pareceres técnico-científicos para apoiar decisões judiciais relacionadas à saúde, com base em evidências científicas. O presente trabalho propõe um protótipo didático inspirado nesse contexto, sem alegar reproduzir as funcionalidades oficiais da plataforma.

### Objetivo geral
Desenvolver um MVP capaz de classificar automaticamente o texto de uma solicitação relacionada à saúde em uma destas categorias: **medicamento, cirurgia, exame ou tratamento**, usando o algoritmo de aprendizado de máquina Naive Bayes.

### Objetivos específicos
- Criar uma base de dados pequena e fictícia, com exemplos rotulados.
- Transformar textos em representações numéricas que o algoritmo consiga processar.
- Treinar e testar um classificador Multinomial Naive Bayes.
- Apresentar métricas básicas e uma matriz de confusão para observar os acertos e os erros.
- Permitir que o usuário informe um texto fictício e veja a categoria sugerida pelo modelo.

### Pergunta norteadora
Um classificador Naive Bayes treinado com exemplos textuais consegue sugerir uma categoria para solicitações de saúde de forma automatizada, auxiliando uma futura etapa de organização e triagem?

## 3. Referências

1. **CONSELHO NACIONAL DE JUSTIÇA (CNJ).** Sistema e-NatJus. Disponível em: https://www.cnj.jus.br/programas-e-acoes/forum-da-saude-3/e-natjus/. Acesso em: 28 out. 2026.
2. **CONSELHO NACIONAL DE JUSTIÇA (CNJ).** Resolução nº 479, de 11 de novembro de 2022. Dispõe sobre o funcionamento e utilização do Sistema Nacional de Pareceres e Notas Técnicas (e-NatJus). Disponível em: https://atos.cnj.jus.br/atos/detalhar/4818. Acesso em: 28 out. 2026.
3. **SCIKIT-LEARN DEVELOPERS.** MultinomialNB. Documentação oficial da biblioteca scikit-learn. Disponível em: https://scikit-learn.org/stable/modules/generated/sklearn.naive_bayes.MultinomialNB.html. Acesso em: 28 out. 2026.
4. **SCIKIT-LEARN DEVELOPERS.** TfidfVectorizer. Documentação oficial da biblioteca scikit-learn. Disponível em: https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html. Acesso em: 28 out. 2026.
5. **SILVA, Tiago Santos; BECKER, Isabella Sigrist Siqueira; OLIVEIRA, Michel Ângelo Constantino de; LACERDA, Renata Rodrigues; SILVA, Wagner Aparecido da.** Mecanismos de Inteligência Artificial no Direito à Saúde: perspectivas e desafios na utilização do e-NatJus como apoio à decisão judicial. *Revista Gestão e Conhecimento Contemporâneo – REGECO*, Brasília, v. 4, n. 1, 2026. Material fornecido para a elaboração do projeto.

## 4. Solução prévia proposta

A solução será um classificador de texto desenvolvido em Python no Google Colab. A primeira versão seguirá este fluxo:

1. **Entrada:** recebe o texto de uma solicitação fictícia ou anonimizada.
2. **Pré-processamento:** converte sequências de 3 a 8 caracteres em uma representação TF-IDF com `TfidfVectorizer`, normalizando acentos.
3. **Classificação:** o modelo `MultinomialNB` compara os padrões aprendidos nos exemplos rotulados e indica a categoria mais provável.
4. **Saída:** mostra a categoria sugerida e as probabilidades estimadas para as quatro classes.
5. **Avaliação:** exibe acurácia, relatório de precisão/recall/F1 e matriz de confusão em um conjunto separado para teste.
6. **Supervisão:** a previsão é uma sugestão de organização, não uma conclusão clínica ou jurídica. Qualquer utilização real exigiria revisão humana, dados apropriados, validação e medidas de segurança e proteção de dados.

### Categorias do MVP
- **Medicamento:** pedidos de fornecimento, dispensação ou acesso a fármacos.
- **Cirurgia:** pedidos para realização ou autorização de operações e procedimentos cirúrgicos.
- **Exame:** solicitações de exames laboratoriais, de imagem ou diagnósticos.
- **Tratamento:** pedidos de terapias, sessões, reabilitação ou acompanhamento terapêutico.

## 5. MVP de dados e algoritmo (Google Colab)

O notebook `MVP_eNatJus_Naive_Bayes.ipynb` contém a implementação completa. A base inicial inclui **80 exemplos sintéticos (20 por categoria)**. O conjunto é dividido entre treino e teste; o modelo usa `TfidfVectorizer` para representar sequências de caracteres e `MultinomialNB` para classificação.

### Escopo e limitações
- Os dados são criados para fins didáticos; não são documentos judiciais reais.
- Uma base pequena pode gerar resultados instáveis e não permite afirmar que o modelo funcionaria adequadamente em processos reais.
- Textos podem mencionar mais de uma categoria, e o modelo sempre escolherá uma das quatro classes neste MVP.
- As probabilidades estimadas não equivalem à certeza de acerto.
- Não usar dados pessoais, prontuários, CPF, nomes de pacientes ou documentos sigilosos.
- O protótipo não avalia se um medicamento ou tratamento é eficaz, adequado ou deve ser concedido; apenas classifica o tema do texto.

## Próximas melhorias
- Aumentar e revisar a base de exemplos, sem informações pessoais identificáveis.
- Incluir uma opção de “revisão manual / categoria incerta”.
- Avaliar modelos alternativos e validar resultados em textos reais devidamente autorizados e anonimizados.
- Registrar versões do conjunto de dados e dos parâmetros do modelo.

## Estrutura sugerida do repositório

```text
seu-repositorio/
├── README.md
└── MVP_eNatJus_Naive_Bayes.ipynb
```
