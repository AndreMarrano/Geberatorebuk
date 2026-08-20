# DESENHO FUNCIONAL DOS MÓDULOS — SISTEMA DE GERAÇÃO DE E-BOOKS DE VISTOS E IMIGRAÇÃO

**Versão:** 1.0  
**Data-base:** 19/08/2026  
**Natureza do documento:** diretriz funcional e conceitual para desenho dos módulos do sistema.  
**Observação:** este documento não constitui especificação técnica obrigatória de programação. A arquitetura técnica, tecnologias, integrações, banco de dados, agentes, APIs, modelos e infraestrutura serão definidos em etapa posterior.

---

# 1. FINALIDADE DESTE DOCUMENTO

Este documento define a lógica funcional que deverá orientar o desenvolvimento do sistema de geração de e-books sobre vistos e imigração.

O objetivo não é determinar, neste momento, como cada módulo será programado, qual tecnologia será utilizada ou se determinada função será executada por agente de IA, código determinístico, integração externa ou processo híbrido.

A finalidade é estabelecer:

- quais módulos conceituais o sistema deverá possuir;
- quais etapas deverão ocorrer;
- quais regras são invariáveis;
- em quais situações o sistema poderá avançar automaticamente;
- em quais situações será obrigatória a validação humana;
- como fontes de informação deverão ser localizadas, qualificadas e utilizadas;
- como evitar invenções, presunções, desatualização e extrapolações;
- como estruturar pesquisa, redação, revisão, identidade visual e publicação.

A implementação técnica será desenhada posteriormente com base nessas diretrizes.

---

# 2. PRINCÍPIOS INVARIÁVEIS DO SISTEMA

Independentemente do tema, país, modalidade de visto ou formato do e-book, o sistema deverá respeitar os seguintes princípios.

## 2.1. Não inventar informações

O sistema não poderá:

- preencher lacunas por plausibilidade;
- presumir requisitos;
- completar fatos com base em padrões de outros países;
- transformar prática comum em regra oficial;
- inferir automaticamente que uma regra antiga continua vigente;
- apresentar estimativa ou hipótese como fato;
- criar documentos, taxas, prazos, exigências ou procedimentos sem suporte suficiente.

Quando não houver informação confiável, o sistema deverá assumir explicitamente que existe uma lacuna.

---

## 2.2. Não avançar com dúvida material sem validação humana

Se houver:

- conflito entre fontes;
- ausência de fonte suficiente;
- regra ambígua;
- divergência entre norma e prática administrativa;
- página oficial desatualizada ou aparentemente contraditória;
- dificuldade de identificar qual norma está vigente;
- informação relevante que não possa ser confirmada;
- dúvida jurídica ou migratória que possa afetar o conteúdo final;

o sistema deverá sinalizar:

`VALIDAÇÃO_HUMANA_NECESSÁRIA`

e impedir a utilização automática daquela informação como fato consolidado.

---

## 2.3. Atualidade é parte da qualidade

O sistema deverá sempre buscar o conteúdo mais atualizado razoavelmente disponível.

Ele não poderá presumir que uma página encontrada em primeiro lugar é a regra vigente.

Sempre que possível, deverá verificar:

- data da norma;
- última atualização;
- legislação posterior;
- regulamentos;
- alterações normativas;
- decisões administrativas relevantes;
- mudança de órgão responsável;
- novo formulário;
- novo procedimento;
- alteração de valores;
- mudança de portal;
- comunicados oficiais recentes.

A pergunta não é apenas:

> “Existe uma fonte para isso?”

Mas também:

> “Essa ainda é a fonte válida e atual?”

---

# 3. ESTRUTURA GERAL DO SISTEMA

O sistema deverá ser pensado como um fluxo modular.

Uma visão conceitual inicial:

```text
DEFINIÇÃO DO TEMA
↓
DELIMITAÇÃO DO PRODUTO
↓
PLANO DE PESQUISA
↓
BUSCA DE FONTES
↓
QUALIFICAÇÃO DAS FONTES
↓
EXTRAÇÃO DO CONHECIMENTO
↓
DETECÇÃO DE LACUNAS E CONFLITOS
↓
VALIDAÇÃO HUMANA
↓
CONSTRUÇÃO DO SUMÁRIO
↓
DEFINIÇÃO DA IDENTIDADE VISUAL
↓
REDAÇÃO
↓
REVISÃO FACTUAL
↓
REVISÃO DE ESCOPO E RESPONSABILIDADE
↓
REVISÃO GRAMATICAL
↓
REVISÃO DE CONSISTÊNCIA
↓
DIAGRAMAÇÃO
↓
QA FINAL
↓
VALIDAÇÃO HUMANA FINAL
↓
PUBLICAÇÃO / VERSIONAMENTO
```

Essa sequência poderá ser ajustada na implementação, mas representa o fluxo lógico desejado.

---

# 4. MÓDULO 1 — DEFINIÇÃO DO TEMA E DO PRODUTO

O processo começa pela escolha do tema.

O usuário deverá poder:

1. selecionar um tema já cadastrado;
2. selecionar uma categoria existente;
3. criar um novo tema;
4. informar livremente o assunto que pretende transformar em e-book.

Exemplos:

- Visto B1/B2 dos Estados Unidos;
- Renovação do visto americano;
- DS-160;
- Study Permit do Canadá;
- Visto D7 de Portugal;
- Visto D8;
- Residência não lucrativa da Espanha;
- Visto para atividade desportiva em Portugal;
- Guia de entrevista consular;
- Comprovação financeira para determinado processo.

---

## 4.1. Resultado esperado

Esse módulo deverá transformar a intenção inicial em um projeto estruturado.

Exemplo conceitual:

```yaml
tema:
pais:
categoria:
publico:
jurisdicao:
tipo_de_produto:
objetivo:
resultado_esperado:
escopo:
fora_do_escopo:
idioma_do_ebook:
data_base:
```

---

## 4.2. Regra de escopo

O sistema deverá impedir temas excessivamente amplos quando isso comprometer:

- atualidade;
- precisão;
- capacidade de execução;
- manutenção do produto.

Sempre que possível, deverá sugerir decomposição.

Exemplo:

Em vez de:

`Guia completo de imigração para os Estados Unidos`

preferir:

`Visto B1/B2 para brasileiros — guia prático`

ou criar uma coleção de produtos independentes.

---

# 5. MÓDULO 2 — PLANO DE PESQUISA

Antes da busca, o sistema deverá transformar o tema em perguntas de pesquisa.

Ele não deverá simplesmente solicitar:

> “Pesquise sobre o visto X.”

O sistema deverá decompor o problema.

Exemplos:

- qual a base legal da categoria?
- qual órgão é competente?
- quem pode solicitar?
- qual a finalidade?
- quais critérios de elegibilidade?
- quais hipóteses de impedimento?
- qual formulário?
- quais documentos são oficialmente exigidos?
- quais documentos dependem do caso?
- qual taxa?
- existe biometria?
- existe entrevista?
- onde o processo é protocolado?
- existe regra específica para brasileiros?
- qual a validade?
- quais são os direitos conferidos?
- quais são as limitações?
- qual o procedimento pós-aprovação?
- há regra para dependentes?
- existem alterações recentes?
- há notícias ou comunicados relevantes que afetem a prática?
- existem controvérsias ou relatos recorrentes que mereçam contexto?

O resultado desse módulo é um `PLANO_DE_PESQUISA`.

---

# 6. MÓDULO 3 — LOCALIZAÇÃO DE FONTES

Este módulo deverá possuir capacidade de pesquisa ampla.

A pesquisa **não poderá ficar limitada**:

- a português;
- ao Brasil;
- a sites de consulados brasileiros;
- a páginas de embaixadas;
- a resultados de busca nacionais.

O sistema deverá pesquisar, conforme o caso:

- no idioma oficial do país;
- em inglês;
- em português;
- em outros idiomas relevantes;
- diretamente em portais legislativos estrangeiros;
- em ministérios;
- em agências migratórias;
- em autoridades administrativas;
- em atos normativos;
- em regulamentos;
- em instruções;
- em decisões;
- em comunicados oficiais.

A busca deverá priorizar a fonte originária da regra.

---

# 7. CLASSIFICAÇÃO DAS FONTES

Nem todas as fontes possuem a mesma finalidade.

O sistema deverá classificar as fontes conforme sua natureza.

---

## 7.1. CLASSE A — FONTES NORMATIVAS PRIMÁRIAS

São as fontes de maior autoridade para determinação de regras.

Exemplos:

- constituição;
- leis;
- decretos;
- regulamentos;
- portarias;
- instruções normativas;
- códigos;
- regulamentos de imigração;
- atos oficiais;
- tratados;
- convenções;
- normas europeias ou supranacionais;
- legislação consolidada.

Quando houver legislação oficial disponível no idioma original, ela deverá ser considerada preferencialmente.

Traduções poderão ser utilizadas como apoio, mas o sistema deverá identificar quando a tradução não possui valor oficial.

---

## 7.2. CLASSE B — FONTES ADMINISTRATIVAS OFICIAIS

Exemplos:

- ministérios;
- departamentos de Estado;
- agências de imigração;
- embaixadas;
- consulados;
- autoridades fronteiriças;
- portais oficiais de vistos;
- páginas oficiais de formulários;
- FAQs governamentais;
- manuais administrativos;
- comunicados oficiais.

Essas fontes podem explicar como a norma é aplicada na prática.

---

## 7.3. CLASSE C — OPERADORES OFICIALMENTE DESIGNADOS

Exemplos:

- VFS Global;
- BLS;
- TLScontact;
- centros de solicitação;
- prestadores oficialmente contratados pelo governo.

Essas fontes podem ser relevantes para:

- protocolo;
- agendamento;
- biometria;
- logística;
- entrega de documentos;
- taxas de serviço;
- funcionamento de centros.

Elas não devem substituir a legislação ou autoridade governamental para estabelecer regra jurídica quando houver fonte oficial superior disponível.

---

## 7.4. CLASSE D — JURISPRUDÊNCIA, DECISÕES E DOCUMENTOS INSTITUCIONAIS

Quando pertinentes ao tema, poderão ser utilizados:

- decisões judiciais;
- decisões administrativas;
- orientações oficiais;
- relatórios governamentais;
- documentos parlamentares;
- pareceres institucionais;
- relatórios de auditoria;
- estudos oficiais.

Essas fontes podem ser relevantes principalmente para:

- controvérsias;
- interpretação;
- mudanças de entendimento;
- contexto regulatório.

---

# 8. FONTES DE CONTEXTO

O sistema não deverá utilizar apenas fontes oficiais.

Existem conteúdos que podem melhorar substancialmente a compreensão do usuário sem constituir fonte normativa.

Esses materiais deverão ser tratados separadamente.

---

## 8.1. CLASSE E — NOTÍCIAS

Notícias podem ser utilizadas para:

- contextualizar mudanças;
- explicar alterações recentes;
- identificar problemas operacionais;
- demonstrar repercussões;
- informar situações excepcionais;
- mencionar atrasos;
- relatar mudanças práticas ainda em implementação.

Entretanto:

> notícia não substitui fonte oficial para afirmar requisito migratório.

Sempre que uma notícia afirmar que determinada regra mudou, o sistema deverá buscar a origem oficial da mudança.

---

## 8.2. Qualificação de notícias

O sistema deverá avaliar:

- reputação do veículo;
- autoria;
- especialização;
- data da publicação;
- presença de fontes;
- correspondência com atos oficiais;
- se a notícia diferencia fato de opinião;
- se outras fontes confiáveis confirmam a informação.

Exemplo de classificação:

```yaml
fonte:
tipo: noticia
credibilidade: alta
uso_permitido:
  - contexto
  - repercussao
uso_nao_permitido:
  - fundamento_unico_de_requisito
```

---

# 9. RELATOS, EXPERIÊNCIAS E FONTES NÃO OFICIAIS

O sistema poderá utilizar relatos quando eles forem úteis para compreender experiência prática.

Exemplos:

- relatos de entrevistas;
- problemas em centros de atendimento;
- dificuldades com formulários;
- experiências de fronteira;
- pedidos adicionais de documentos;
- dúvidas recorrentes;
- comportamento de determinado portal;
- experiências práticas pós-chegada.

Podem vir de:

- fóruns;
- Reddit;
- grupos;
- blogs;
- vídeos;
- avaliações;
- comunidades;
- entrevistas;
- relatos publicados por usuários.

---

## 9.1. Regra fundamental

Relatos nunca devem ser apresentados como regra oficial.

Exemplo inadequado:

> “O consulado sempre pergunta isso.”

Exemplo permitido:

> “Há relatos recorrentes de requerentes que receberam perguntas sobre esse ponto. Isso não significa que a pergunta seja obrigatória ou que todos passem pela mesma experiência.”

---

## 9.2. Qualificação de relatos

O sistema deverá considerar:

- quantidade de relatos semelhantes;
- independência entre os relatos;
- data;
- local;
- categoria migratória;
- consistência;
- possibilidade de verificação;
- existência de provas;
- reputação da pessoa ou comunidade;
- compatibilidade com as regras oficiais.

Classificação possível:

- alta confiabilidade contextual;
- confiabilidade moderada;
- baixa confiabilidade;
- relato isolado;
- não verificável.

---

## 9.3. Uso permitido de relatos

Relatos podem servir para:

- identificar dúvidas frequentes;
- construir FAQ;
- localizar pontos problemáticos do processo;
- contextualizar experiência;
- decidir quais assuntos precisam de explicação adicional;
- orientar novas pesquisas.

Relatos não devem servir como fundamento exclusivo para:

- requisito;
- taxa;
- direito;
- obrigação;
- validade;
- prazo;
- elegibilidade;
- regra jurídica;
- documento obrigatório.

---

# 10. MÓDULO 4 — QUALIFICAÇÃO DAS FONTES

Localizar uma página não significa aprová-la.

Cada fonte deverá passar por qualificação.

O sistema deverá verificar:

- natureza da fonte;
- órgão ou autor;
- competência;
- país;
- idioma;
- data;
- última atualização;
- vigência;
- autoridade;
- relação com o tema;
- existência de norma posterior;
- possíveis conflitos;
- versão;
- estabilidade do conteúdo.

Resultado possível:

```text
APROVADA
APROVADA_COM_RESTRIÇÃO
USO_APENAS_CONTEXTUAL
NECESSITA_VALIDAÇÃO_HUMANA
REJEITADA
```

---

# 11. MÓDULO 5 — CONTROLE DE ATUALIDADE

Este módulo deverá verificar se o conteúdo localizado é o mais atual disponível.

Isso é especialmente importante em imigração, porque páginas antigas permanecem indexadas mesmo depois de alterações relevantes.

O sistema deverá buscar:

- versões consolidadas de leis;
- datas de entrada em vigor;
- alterações legislativas;
- atos posteriores;
- revogações;
- mudanças de nomenclatura de órgãos;
- atualizações de portais;
- comunicados mais recentes;
- novas tabelas de taxas;
- novas provas de fundos;
- novos formulários;
- alterações de fluxo.

---

## 11.1. Conflito temporal

Se houver:

- página oficial antiga;
- norma nova;
- FAQ ainda não atualizada;

o sistema não poderá escolher automaticamente uma delas sem análise.

Deverá sinalizar o conflito.

---

# 12. MÓDULO 6 — EXTRAÇÃO DO CONHECIMENTO

Depois de aprovar as fontes, o sistema deverá extrair informações específicas.

O objetivo é criar uma base factual estruturada.

Exemplo:

```yaml
claim_id:
afirmacao:
tipo:
fonte:
data_da_fonte:
consultado_em:
trecho_de_suporte:
classificacao:
volatilidade:
status:
```

---

## 12.1. Classificação do conteúdo

Todo fato relevante deverá ser classificado, quando aplicável, como:

### REQUISITO OFICIAL

Exigido diretamente por norma ou autoridade competente.

### DOCUMENTO OFICIALMENTE SOLICITADO

Documento listado formalmente pela autoridade.

### DOCUMENTO DE SUPORTE

Pode ajudar a demonstrar requisito, mas não necessariamente é obrigatório.

### PRÁTICA ADMINISTRATIVA

Conduta observada no funcionamento do órgão ou operador.

### BOA PRÁTICA

Recomendação prudencial, sem caráter obrigatório.

### CONTEXTO

Informação histórica, política ou administrativa.

### RELATO

Experiência não oficial.

### NOTÍCIA

Informação jornalística contextual.

---

# 13. MÓDULO 7 — DETECÇÃO DE LACUNAS

O sistema deverá comparar:

- o que precisa ser respondido;
- o que foi efetivamente comprovado.

Cada item deverá receber um status.

```text
VALIDADO
PARCIALMENTE_VALIDADO
NÃO_APLICÁVEL
LACUNA
CONFLITO
```

O sistema não poderá utilizar:

`PROVAVELMENTE`

como substituto de comprovação.

---

# 14. MÓDULO 8 — VALIDAÇÃO HUMANA DA PESQUISA

A validação humana é uma etapa central do sistema.

Ela não deve existir apenas no final.

Após pesquisa e qualificação, o sistema deverá apresentar um resumo para aprovação.

Exemplo:

```text
Fontes oficiais identificadas: 12
Normas localizadas: 4
Fontes administrativas: 6
Notícias contextuais: 3
Relatos avaliados: 9

Fatos validados: 84
Fatos voláteis: 11
Lacunas: 2
Conflitos: 1
Itens que exigem decisão humana: 3
```

---

## 14.1. Validação humana obrigatória quando houver

- conflito entre normas;
- dúvida de vigência;
- ausência de fonte oficial suficiente;
- interpretação jurídica relevante;
- divergência entre prática e regra;
- notícia sem confirmação oficial;
- relato utilizado para contexto sensível;
- dúvida sobre qualidade da fonte;
- informação potencialmente desatualizada;
- mudança normativa recente;
- tema com alto risco de erro.

Sem aprovação, o sistema não deverá transformar o item em fato consolidado.

---

# 15. MÓDULO 9 — CONSTRUÇÃO DO SUMÁRIO

Somente depois de uma base factual minimamente segura o sistema deverá construir o sumário.

O sumário deverá levar em conta:

- escopo;
- público;
- quantidade de informação;
- objetivo do produto;
- complexidade;
- lacunas;
- conteúdo oficial disponível;
- tamanho pretendido.

O sistema deverá evitar incluir capítulos apenas para “encher” o e-book.

---

# 16. VALIDAÇÃO HUMANA DO SUMÁRIO

Antes da redação, o usuário deverá poder:

- aprovar;
- remover;
- alterar;
- reorganizar;
- incluir tópicos.

Essa etapa evita que o sistema escreva um produto tecnicamente correto, mas comercialmente mal desenhado.

---

# 17. MÓDULO 10 — IDENTIDADE VISUAL

A identidade visual não será necessariamente padronizada entre todos os produtos.

O sistema deverá possuir um módulo dedicado a construir ou validar a direção visual de cada e-book.

Esse módulo deverá correlacionar:

- tema;
- país;
- público;
- tipo de produto;
- tom;
- posicionamento;
- preço;
- nível de sofisticação;
- canal de venda.

---

## 17.1. O módulo poderá perguntar

- O produto deve parecer institucional, editorial, premium, jovem ou minimalista?
- Haverá relação visual com uma marca existente?
- Existe paleta obrigatória?
- O material deve ser mais jurídico ou mais comercial?
- Haverá fotografias?
- Haverá ícones?
- Haverá ilustrações?
- O design deve remeter ao país?
- Existe um produto anterior que deve servir de referência?

---

## 17.2. Resultado

O módulo deverá produzir um `VISUAL_BRIEF`.

Exemplo:

```yaml
posicionamento:
publico_visual:
paleta:
tipografia:
grid:
fotografia:
ilustracao:
icones:
boxes:
tabelas:
checklists:
capa:
restricoes:
```

---

## 17.3. Regra

Não utilizar automaticamente bandeiras, brasões ou clichês visuais apenas porque determinado país está sendo tratado.

A identidade deverá ser coerente com o público e a proposta.

---

# 18. VALIDAÇÃO HUMANA DA IDENTIDADE VISUAL

A identidade visual deverá ser aprovada antes da diagramação final.

O sistema poderá sugerir.

A decisão deve permanecer humana.

---

# 19. MÓDULO 11 — REDAÇÃO

O módulo de redação deverá trabalhar com a base factual previamente construída.

Regra central:

> O módulo de redação não deve pesquisar silenciosamente para preencher lacunas.

Se surgir necessidade de novo fato, o sistema deverá retornar ao módulo de pesquisa.

---

## 19.1. Inputs do módulo

- Project Brief;
- sumário;
- base factual;
- regras editoriais;
- identidade visual;
- limites do produto;
- conteúdo fora de escopo;
- fontes contextuais qualificadas.

---

## 19.2. Uso das fontes

O texto deverá diferenciar claramente:

- regra oficial;
- contexto;
- opinião;
- recomendação;
- notícia;
- relato.

O leitor nunca deverá receber um relato como se fosse determinação consular.

---

# 20. MÓDULO 12 — REVISÃO FACTUAL

Um módulo separado deverá revisar o conteúdo depois de redigido.

Ele deverá questionar cada afirmação factual relevante.

Possíveis resultados:

```text
SUSTENTADA
PARCIALMENTE_SUSTENTADA
NÃO_SUSTENTADA
DESATUALIZADA
CONTRADITÓRIA
EXCESSIVAMENTE_CATEGÓRICA
NECESSITA_VALIDAÇÃO_HUMANA
```

Esse módulo deverá verificar especialmente:

- taxas;
- valores;
- formulários;
- prazos;
- órgãos;
- requisitos;
- regras de dependentes;
- direitos;
- proibições;
- validade;
- procedimentos.

---

# 21. MÓDULO 13 — REVISÃO DE ESCOPO E RESPONSABILIDADE

O sistema deverá verificar se o texto ultrapassou a fronteira entre informação geral e assessoria individual.

Deverá identificar:

- promessas de aprovação;
- recomendações categóricas inadequadas;
- análise individual simulada;
- documentos apresentados como obrigatórios sem fonte;
- instruções para ocultação;
- scripts manipulativos;
- afirmações indevidas de garantia.

---

# 22. MÓDULO 14 — REVISÃO GRAMATICAL

A revisão gramatical deverá ser obrigatória.

O módulo deverá verificar:

- ortografia;
- concordância;
- regência;
- pontuação;
- sintaxe;
- repetição;
- clareza;
- coerência;
- padronização;
- consistência terminológica;
- português brasileiro;
- uso adequado de estrangeirismos.

---

## 22.1. Regra de proteção técnica

O módulo gramatical não poderá alterar silenciosamente o significado jurídico ou migratório.

Quando uma correção puder modificar o sentido técnico, deverá sinalizar:

`REVISÃO_HUMANA_RECOMENDADA`

---

# 23. MÓDULO 15 — CONSISTÊNCIA GLOBAL

Esse módulo deverá analisar o e-book inteiro.

Verificações:

- terminologia;
- siglas;
- nomes de órgãos;
- números;
- valores;
- datas;
- links;
- referências cruzadas;
- títulos;
- tabelas;
- checklists;
- nomenclatura das categorias;
- coerência entre capítulos.

---

# 24. MÓDULO 16 — DIAGRAMAÇÃO

Depois de conteúdo e identidade visual aprovados, o sistema poderá gerar o material visual.

A tecnologia será definida posteriormente.

Possibilidades futuras:

- templates;
- Canva;
- HTML/CSS;
- sistema próprio;
- PDF automatizado;
- ferramenta externa;
- combinação entre automação e edição manual.

Este documento não determina qual solução deverá ser adotada.

---

# 25. MÓDULO 17 — QA PÓS-DIAGRAMAÇÃO

O arquivo final deverá passar por controle de qualidade.

Verificar:

- texto cortado;
- problemas de quebra;
- páginas vazias;
- títulos órfãos;
- tabelas;
- links;
- imagens;
- resolução;
- contraste;
- legibilidade;
- paginação;
- sumário;
- referências;
- checklists.

---

# 26. MÓDULO 18 — VALIDAÇÃO HUMANA FINAL

Nenhum produto deverá ser publicado automaticamente sem uma validação final.

O responsável deverá poder visualizar:

- versão final;
- data de pesquisa;
- fontes;
- itens voláteis;
- alertas;
- pendências;
- revisão factual;
- revisão gramatical;
- QA.

---

# 27. MÓDULO 19 — VERSIONAMENTO

Cada e-book deverá possuir versão.

Exemplo:

```text
B1B2-BR-v1.0
B1B2-BR-v1.1
B1B2-BR-v2.0
```

O sistema deverá registrar:

- data de criação;
- data da última revisão;
- data da última validação de fontes;
- fontes utilizadas;
- fatos voláteis;
- mudanças realizadas.

---

# 28. RASTREABILIDADE DO CONHECIMENTO

A arquitetura conceitual deverá permitir rastrear:

```text
FONTE
↓
FATO
↓
TRECHO DO E-BOOK
↓
VERSÃO
```

Isso permitirá futuramente identificar:

> “Quais produtos utilizam uma regra que acabou de mudar?”

Essa rastreabilidade é parte importante da sustentabilidade do sistema.

---

# 29. SEPARAÇÃO ENTRE IA E CONTROLE DETERMINÍSTICO

Na etapa técnica futura, deverá ser avaliado quais funções realmente precisam de IA.

Regra conceitual:

> Quanto mais binária, verificável e crítica for uma regra, menos ela deverá depender exclusivamente de um modelo de linguagem.

Exemplos de atividades adequadas à IA:

- pesquisa semântica;
- identificação de fontes;
- interpretação;
- tradução;
- estruturação;
- resumo;
- redação;
- revisão semântica;
- sugestão visual;
- classificação preliminar.

Exemplos de atividades que podem ser mais adequadas a validação determinística:

- domínio autorizado;
- campo obrigatório;
- presença de aprovação humana;
- controle de versão;
- data;
- URL;
- hash;
- status;
- workflow;
- campos faltantes;
- bloqueio de publicação.

---

# 30. COMPONENTES CONCEITUAIS DO SISTEMA

A solução poderá ser composta, futuramente, por quatro tipos principais de componentes.

## 30.1. ORQUESTRADOR

Controla:

- fluxo;
- estados;
- ordem;
- permissões;
- chamadas;
- bloqueios.

---

## 30.2. AGENTES OU MÓDULOS DE IA

Executam tarefas que exigem:

- interpretação;
- pesquisa;
- linguagem;
- comparação;
- síntese;
- geração.

---

## 30.3. VALIDADORES DETERMINÍSTICOS

Controlam regras objetivas.

---

## 30.4. HUMAN GATES

Representam decisões que não devem ser delegadas automaticamente.

---

# 31. HUMAN GATES MÍNIMOS

A princípio, recomenda-se aprovação humana obrigatória nos seguintes pontos:

## Gate 1 — Escopo

Antes da pesquisa aprofundada.

## Gate 2 — Base factual

Especialmente quando houver:

- conflito;
- lacuna;
- regra nova;
- interpretação;
- fonte não oficial relevante.

## Gate 3 — Sumário

Antes da redação.

## Gate 4 — Identidade visual

Antes da diagramação final.

## Gate 5 — Publicação

Antes de liberar o produto.

Outros gates poderão ser adicionados de acordo com risco e complexidade.

---

# 32. IDIOMAS E TRADUÇÃO

A pesquisa não deverá ser limitada por idioma.

O sistema deverá:

- identificar o idioma oficial relevante;
- pesquisar no idioma original quando isso melhorar a qualidade;
- pesquisar também em inglês quando útil;
- usar português para apresentação final ao público brasileiro;
- preservar nomenclaturas oficiais quando necessário.

Quando houver tradução:

- indicar quando é tradução livre;
- evitar tratar tradução não oficial como texto normativo oficial;
- conferir termos jurídicos e migratórios;
- manter o original quando a tradução puder gerar perda de precisão.

---

# 33. LEGISLAÇÃO INTERNACIONAL E SUPRANACIONAL

O sistema deverá estar apto a considerar estruturas que ultrapassam a legislação nacional.

Exemplos:

- União Europeia;
- Espaço Schengen;
- tratados internacionais;
- convenções;
- acordos bilaterais;
- regulamentos europeus;
- regras regionais.

Quando uma norma supranacional afetar o tema, ela deverá entrar no mapa de fontes.

---

# 34. CONFLITO ENTRE REGRA E PRÁTICA

Em imigração, pode existir diferença entre:

- o que a norma estabelece;
- o que a página administrativa informa;
- o que o posto consular pratica;
- o que usuários relatam.

O sistema não deverá apagar essas diferenças.

Ele deverá classificá-las.

Exemplo:

> “A regra oficial estabelece X. Há relatos de aplicação prática Y em determinado contexto. Não foi localizada orientação oficial que transforme essa prática em regra geral.”

Quando a diferença for material, exigir validação humana.

---

# 35. MATRIZ DE CONFIABILIDADE

Uma fonte poderá receber uma avaliação multidimensional.

Exemplo:

```yaml
autoridade:
atualidade:
verificabilidade:
relevancia:
independencia:
coerencia_com_fontes_oficiais:
tipo_de_uso_permitido:
```

A classificação não precisa ser necessariamente numérica.

O importante é impedir que fontes heterogêneas sejam tratadas como equivalentes.

---

# 36. REGRAS PARA NOTÍCIAS

Notícias podem:

- contextualizar;
- explicar mudança;
- registrar repercussão;
- sinalizar tendência;
- indicar problema operacional.

Notícias não podem:

- substituir a norma;
- criar requisito;
- ser apresentadas como posição oficial sem confirmação.

Quando uma notícia mencionar uma mudança normativa, o sistema deverá procurar o ato oficial.

---

# 37. REGRAS PARA RELATOS

Relatos podem:

- ilustrar;
- contextualizar;
- revelar dúvida recorrente;
- sinalizar prática;
- melhorar FAQ.

Relatos não podem:

- determinar regra;
- substituir exigência oficial;
- gerar promessa;
- justificar afirmação categórica.

Sempre que utilizados, devem ser claramente identificados como relatos.

---

# 38. REGRAS PARA CONTEÚDO NÃO ENCONTRADO

Se o sistema não localizar resposta suficiente:

1. registrar a lacuna;
2. ampliar a pesquisa;
3. pesquisar em outro idioma;
4. verificar legislação;
5. buscar atos complementares;
6. verificar outra autoridade competente;
7. verificar atualização;
8. se ainda não houver resposta, solicitar validação humana.

Nunca inventar.

---

# 39. ESTADO DE BLOQUEIO

O sistema deverá ser capaz de interromper a produção.

Exemplos:

```text
BLOQUEADO_POR_LACUNA
BLOQUEADO_POR_CONFLITO
BLOQUEADO_POR_FONTE_INSUFICIENTE
BLOQUEADO_POR_ATUALIDADE
BLOQUEADO_POR_VALIDAÇÃO_HUMANA
```

Bloqueio não é falha do sistema.

É comportamento de segurança esperado.

---

# 40. ROADMAP FUNCIONAL RESUMIDO

## FASE 1 — Entrada

1. Tema.
2. Produto.
3. Escopo.
4. Público.

## FASE 2 — Conhecimento

5. Plano de pesquisa.
6. Busca multilíngue.
7. Localização de legislação.
8. Fontes administrativas.
9. Notícias.
10. Relatos.
11. Qualificação.
12. Atualidade.
13. Extração.
14. Lacunas.
15. Validação humana.

## FASE 3 — Produto editorial

16. Sumário.
17. Validação do sumário.
18. Identidade visual.
19. Validação visual.

## FASE 4 — Conteúdo

20. Redação.
21. Revisão factual.
22. Revisão de escopo.
23. Revisão gramatical.
24. Consistência global.

## FASE 5 — Arquivo final

25. Diagramação.
26. QA.
27. Validação humana.
28. Versionamento.
29. Publicação.

---

# 41. DIRETRIZ FINAL

O sistema deverá ser construído com a premissa de que a IA é uma ferramenta de pesquisa, organização, interpretação e produção, mas não uma autoridade autônoma.

Ela poderá:

- localizar;
- comparar;
- resumir;
- estruturar;
- escrever;
- revisar;
- sugerir.

Ela não deverá decidir silenciosamente que uma informação incerta é verdadeira.

A regra central do sistema é:

> **Quando houver informação confiável, utilizar.  
> Quando houver divergência, qualificar.  
> Quando houver incerteza relevante, bloquear.  
> Quando houver lacuna, pesquisar.  
> Quando ainda houver dúvida, exigir validação humana.**

Este documento define o **desenho funcional dos módulos**.

A próxima etapa deverá transformar essas diretrizes em arquitetura técnica, definindo:

- componentes;
- agentes;
- skills;
- APIs;
- modelos;
- banco de dados;
- estrutura de estados;
- integrações;
- formatos de armazenamento;
- mecanismos de validação;
- pipeline de execução.

Essas decisões técnicas deverão respeitar os princípios aqui estabelecidos, mas não estão rigidamente determinadas por este documento.
