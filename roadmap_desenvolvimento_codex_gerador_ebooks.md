# ROADMAP DE DESENVOLVIMENTO NO CODEX — GERADOR LOCAL DE E-BOOKS DE VISTOS E IMIGRAÇÃO

**Versão:** 1.0  
**Data-base:** 19/08/2026  
**Finalidade:** orientar o desenvolvimento incremental do sistema no Codex, módulo por módulo, com validações humanas entre etapas.

---

# 1. PAPEL DESTE DOCUMENTO

Este arquivo deve ser utilizado em conjunto com os outros dois documentos-base do projeto:

1. `base_contexto_sistema_ebooks_vistos.md`
2. `desenho_funcional_modulos_sistema_ebooks_vistos.md`
3. `roadmap_desenvolvimento_codex_gerador_ebooks.md` — este documento

Os três arquivos possuem papéis diferentes:

- **Base de contexto:** define a lógica editorial, o posicionamento dos e-books, os benchmarks e os critérios de qualidade.
- **Desenho funcional:** define os módulos conceituais, gates humanos, tratamento de fontes, revisão, identidade visual e fluxo.
- **Roadmap de desenvolvimento:** define a ordem de implementação técnica e o comportamento esperado do Codex durante o desenvolvimento.

Este roadmap **não substitui** os outros arquivos.

Em caso de dúvida de produto, usar a Base de Contexto.

Em caso de dúvida sobre comportamento funcional, usar o Desenho Funcional.

Em caso de dúvida sobre sequência de desenvolvimento, usar este Roadmap.

---

# 2. OBJETIVO TÉCNICO INICIAL

Construir um aplicativo local para Windows capaz de:

1. criar projetos de e-books;
2. receber fontes manualmente;
3. organizar e indexar essas fontes;
4. utilizar um modelo de linguagem local;
5. extrair fatos das fontes;
6. identificar lacunas, conflitos e incertezas;
7. exigir validação humana quando necessário;
8. construir o sumário;
9. gerar o conteúdo do e-book;
10. revisar fatos, escopo, gramática e consistência;
11. definir e registrar identidade visual;
12. gerar arquivos finais;
13. manter histórico e versionamento.

A primeira versão será **local-first**.

Ela não dependerá obrigatoriamente de:

- servidor web;
- HostGator;
- OpenAI API;
- ambiente do ChatGPT;
- banco de dados remoto;
- serviços pagos de terceiros.

---

# 3. PREMISSAS DE ARQUITETURA

## 3.1. Ambiente inicial

Sistema operacional principal:

`Windows`

Execução:

- PowerShell;
- terminal;
- posteriormente interface gráfica local.

Linguagem preferencial:

`Python 3.12+`

Modelo local preferencial:

- Ollama como camada de execução;
- modelo configurável;
- inicialmente um modelo local compatível com a máquina disponível.

O sistema não deve ficar acoplado a um único modelo.

---

## 3.2. Arquitetura desacoplada

O sistema deve possuir interfaces abstratas para que, futuramente, seja possível trocar:

- Ollama por OpenAI API;
- modelo local por modelo remoto;
- armazenamento em arquivos por banco PostgreSQL;
- interface local por interface web;
- geração local de PDF por serviço externo.

O núcleo do sistema não deve depender da interface.

---

# 4. REGRA PRINCIPAL PARA O CODEX

O Codex deve trabalhar **um módulo por vez**.

Não implementar todo o projeto de uma só vez.

Ao concluir um módulo:

1. rodar os testes;
2. informar o que foi criado;
3. informar os arquivos modificados;
4. explicar como testar manualmente;
5. indicar limitações conhecidas;
6. aguardar autorização humana antes de iniciar o próximo módulo.

O Codex não deve avançar automaticamente para a próxima fase sem autorização.

---

# 5. REGRAS DE DESENVOLVIMENTO

Durante todo o projeto, o Codex deverá seguir estas regras:

## 5.1. Não quebrar módulos já aprovados

Toda alteração posterior deve preservar comportamento existente, salvo autorização expressa.

---

## 5.2. Testes obrigatórios

Cada módulo deve possuir testes automatizados quando tecnicamente aplicável.

O Codex deve preferir:

- `pytest`;
- testes unitários;
- testes de integração local;
- fixtures pequenas e controladas.

---

## 5.3. Sem segredos no código

Nunca inserir:

- API keys;
- senhas;
- tokens;
- credenciais.

Usar:

`.env`

e fornecer:

`.env.example`

---

## 5.4. Logs

O sistema deve registrar ações relevantes.

Exemplos:

- projeto criado;
- fonte inserida;
- arquivo processado;
- modelo acionado;
- etapa aprovada;
- etapa bloqueada;
- erro;
- versão gerada.

---

## 5.5. Rastreabilidade

Nenhum trecho factual importante deve perder sua relação com a fonte.

Preservar a cadeia:

`fonte → evidência → fato → conteúdo → versão`

---

## 5.6. Human gates reais

A aprovação humana não deve existir apenas como texto.

Ela deve existir no estado do projeto.

Exemplo:

```text
research_status = "pending_human_approval"
```

Enquanto o estado não for aprovado, módulos seguintes devem bloquear execução.

---

# 6. ESTRUTURA DE PASTAS INICIAL

Criar estrutura semelhante a:

```text
ebook-generator/
│
├── app/
│   ├── core/
│   ├── models/
│   ├── services/
│   ├── modules/
│   ├── validators/
│   ├── repositories/
│   ├── exporters/
│   └── ui/
│
├── data/
│   ├── projects/
│   ├── sources/
│   ├── indexes/
│   ├── outputs/
│   └── backups/
│
├── prompts/
│
├── templates/
│
├── tests/
│
├── docs/
│
├── scripts/
│
├── .env.example
├── pyproject.toml
├── README.md
└── main.py
```

A estrutura poderá ser ajustada pelo Codex se houver motivo técnico claro.

---

# 7. MODELO DE DADOS CONCEITUAL

O sistema deverá possuir entidades equivalentes a:

## Project

Representa um e-book em desenvolvimento.

Campos mínimos:

```yaml
id:
title:
theme:
country:
visa_category:
target_audience:
jurisdiction:
product_type:
objective:
scope:
out_of_scope:
language:
created_at:
updated_at:
status:
current_stage:
version:
```

---

## Source

Representa uma fonte.

```yaml
id:
project_id:
title:
source_type:
authority_class:
publisher:
country:
language:
url:
local_file:
publication_date:
last_updated:
retrieved_at:
credibility:
allowed_usage:
status:
notes:
```

---

## Evidence

Trecho específico de uma fonte.

```yaml
id:
source_id:
location:
quoted_or_extracted_text:
context:
```

---

## Claim

Fato ou proposição utilizada pelo e-book.

```yaml
id:
project_id:
statement:
classification:
volatility:
status:
confidence:
requires_human_validation:
```

---

## ClaimEvidence

Relação entre Claim e Evidence.

---

## HumanDecision

```yaml
id:
project_id:
stage:
decision:
user:
date:
notes:
```

---

## Chapter

```yaml
id:
project_id:
order:
title:
status:
content:
```

---

## VisualBrief

```yaml
project_id:
positioning:
palette:
typography:
layout:
image_style:
boxes:
tables:
cover:
restrictions:
status:
```

---

## ProjectVersion

Histórico de publicação.

---

# 8. MÁQUINA DE ESTADOS

Implementar estados explícitos.

Sugestão:

```text
NEW
PROJECT_DEFINED
SOURCES_PENDING
SOURCES_INGESTED
SOURCES_QUALIFIED
KNOWLEDGE_EXTRACTED
RESEARCH_BLOCKED
RESEARCH_PENDING_APPROVAL
RESEARCH_APPROVED
OUTLINE_DRAFTED
OUTLINE_PENDING_APPROVAL
OUTLINE_APPROVED
VISUAL_BRIEF_DRAFTED
VISUAL_BRIEF_PENDING_APPROVAL
VISUAL_BRIEF_APPROVED
DRAFTING
DRAFT_COMPLETE
FACT_REVIEW
SCOPE_REVIEW
GRAMMAR_REVIEW
CONSISTENCY_REVIEW
LAYOUT
QA
FINAL_APPROVAL
PUBLISHED
ARCHIVED
```

Os estados podem ser refinados durante o desenvolvimento.

---

# 9. FASE 0 — PREPARAÇÃO DO REPOSITÓRIO

## Objetivo

Criar uma base limpa e executável.

## Implementar

- estrutura de pastas;
- ambiente virtual;
- `pyproject.toml`;
- configuração do `pytest`;
- `.gitignore`;
- `.env.example`;
- logger;
- arquivo de configuração;
- comando inicial.

## Resultado esperado

Executar:

```powershell
python main.py
```

e receber uma resposta simples de sistema inicializado.

## Critério de aceite

- projeto roda no Windows;
- testes básicos passam;
- README explica instalação.

## Human gate

Aprovar estrutura antes da Fase 1.

---

# 10. FASE 1 — CONFIGURAÇÃO E DETECÇÃO DO MODELO LOCAL

## Objetivo

Criar uma camada abstrata de LLM.

## Implementar

Interface equivalente a:

```python
class LLMProvider:
    def generate(...)
    def structured_generate(...)
    def health_check(...)
```

Primeiro provider:

`OllamaProvider`

Configuração via `.env`:

```text
LLM_PROVIDER=ollama
OLLAMA_BASE_URL=http://localhost:11434
OLLAMA_MODEL=<modelo>
```

## Requisito

O restante do sistema não deve chamar Ollama diretamente.

Sempre usar `LLMProvider`.

Isso permitirá futuramente adicionar:

`OpenAIProvider`

sem alterar os módulos editoriais.

## Testes

- health check;
- resposta simples;
- tratamento de Ollama indisponível;
- timeout.

## Human gate

Usuário instala/configura Ollama e confirma funcionamento.

---

# 11. FASE 2 — CRIAÇÃO E GERENCIAMENTO DE PROJETOS

## Objetivo

Permitir criar, abrir, editar e listar projetos.

## Interface inicial

Pode ser CLI.

Exemplo:

```text
1. Novo projeto
2. Abrir projeto
3. Listar projetos
4. Sair
```

## Novo projeto

Solicitar:

- tema;
- país;
- categoria;
- público;
- jurisdição;
- tipo de produto;
- objetivo;
- escopo;
- fora de escopo.

## Persistência inicial

Para MVP, utilizar SQLite.

Motivos:

- local;
- simples;
- sem servidor;
- confiável;
- permite consultas;
- facilita migração futura.

Não usar JSON como armazenamento primário do sistema inteiro.

JSON pode ser utilizado para exportação e artefatos intermediários.

## Critério de aceite

Projeto deve permanecer disponível após fechar e reabrir o programa.

---

# 12. FASE 3 — HUMAN GATES E WORKFLOW

## Objetivo

Implementar o mecanismo de aprovação antes dos módulos inteligentes.

Criar funções:

```text
approve_stage()
reject_stage()
request_changes()
block_stage()
```

Registrar:

- usuário;
- data;
- etapa;
- decisão;
- observação.

## Regra

Nenhum módulo posterior poderá executar se o gate anterior obrigatório não estiver aprovado.

## Critério de aceite

Tentar gerar sumário antes de aprovar pesquisa deve gerar bloqueio real.

---

# 13. FASE 4 — INGESTÃO MANUAL DE FONTES

## Objetivo

Permitir que o usuário alimente o projeto sem depender de busca online.

## Tipos iniciais

Aceitar:

- `.pdf`
- `.txt`
- `.md`
- `.html`
- `.docx`

Opcional posteriormente:

- imagens;
- OCR;
- páginas salvas;
- URL.

## Fluxo

```text
Adicionar fonte
→ copiar arquivo para área controlada do projeto
→ extrair texto
→ registrar metadados
→ manter arquivo original
```

## Nunca

Excluir ou sobrescrever automaticamente o arquivo original.

## Metadados mínimos solicitados ao usuário quando não puderem ser detectados

- título;
- origem;
- idioma;
- tipo;
- data;
- URL, se houver.

---

# 14. FASE 5 — EXTRAÇÃO DE TEXTO DOS DOCUMENTOS

## Objetivo

Transformar fontes em texto pesquisável.

## Bibliotecas possíveis

- PDF: `pypdf` ou equivalente;
- DOCX: `python-docx`;
- HTML: `BeautifulSoup`;
- TXT/MD: leitura direta.

OCR deve ser uma etapa separada e somente quando necessário.

## Armazenar

- texto completo;
- localização original;
- páginas, quando disponível;
- hash do arquivo.

## Critério

O sistema deve conseguir informar de qual página ou seção veio um trecho quando essa informação estiver disponível.

---

# 15. FASE 6 — QUALIFICAÇÃO DAS FONTES

## Objetivo

Classificar a natureza e o uso permitido de cada fonte.

## Categorias

- normativa primária;
- administrativa oficial;
- operador oficialmente designado;
- institucional/jurisprudencial;
- notícia;
- relato;
- outra.

## Processo híbrido

O modelo pode sugerir classificação.

A aplicação deve permitir correção manual.

## Saída

```text
APROVADA
APROVADA_COM_RESTRIÇÃO
CONTEXTUAL
VALIDAÇÃO_HUMANA
REJEITADA
```

## Regra

O sistema nunca deve tratar notícia ou relato como requisito oficial.

---

# 16. FASE 7 — BUSCA INTERNA NAS FONTES

## Objetivo

Permitir ao sistema localizar trechos relevantes sem enviar todos os documentos completos ao modelo.

## MVP

Pode começar com:

- pesquisa textual;
- divisão em chunks;
- índice simples.

## Evolução

Adicionar embeddings locais e busca semântica.

Possíveis ferramentas locais:

- FAISS;
- Chroma;
- SQLite com extensão apropriada;
- outro mecanismo avaliado pelo Codex.

## Regra

A escolha deve privilegiar:

- execução local;
- simplicidade;
- rastreabilidade;
- baixo acoplamento.

---

# 17. FASE 8 — PLANO DE PESQUISA AUTOMÁTICO

## Objetivo

A partir do tema, gerar as perguntas que precisam ser respondidas.

Exemplo:

```yaml
research_questions:
  - question:
    category:
    importance:
    status:
```

O plano deverá cobrir:

- base legal;
- autoridade;
- finalidade;
- elegibilidade;
- requisitos;
- documentos;
- formulários;
- taxas;
- etapas;
- biometria;
- entrevista;
- validade;
- limitações;
- dependentes;
- pós-aprovação;
- alterações recentes;
- contexto relevante.

## Human gate

Permitir ao usuário editar e aprovar o plano.

---

# 18. FASE 9 — MÓDULO DE EXTRAÇÃO DE CONHECIMENTO

## Objetivo

Responder às perguntas de pesquisa exclusivamente a partir das fontes disponíveis.

O LLM deve receber:

- pergunta;
- trechos recuperados;
- metadados;
- regras de fonte.

## Saída estruturada

```yaml
answer:
status:
claims:
sources:
evidence:
limitations:
requires_human_validation:
```

## Regra crítica

O prompt deverá conter:

> Se as fontes fornecidas não sustentarem a resposta, retorne LACUNA. Não utilize conhecimento externo para completar.

---

# 19. FASE 10 — REGISTRO DE CLAIMS E EVIDÊNCIAS

## Objetivo

Converter conhecimento extraído em unidades rastreáveis.

Cada Claim deve possuir pelo menos uma Evidence válida quando a afirmação for factual.

## Tipos de Claim

- requisito oficial;
- documento solicitado;
- documento de suporte;
- prática administrativa;
- boa prática;
- contexto;
- notícia;
- relato.

## Regra

Claim sem evidência:

`UNSUPPORTED`

e não pode ser usado automaticamente na redação.

---

# 20. FASE 11 — DETECTOR DE LACUNAS E CONFLITOS

## Objetivo

Auditar a base de conhecimento.

## Identificar

- perguntas sem resposta;
- claims sem evidência;
- duas fontes oficiais divergentes;
- versões temporais diferentes;
- datas incompatíveis;
- taxas diferentes;
- órgãos diferentes;
- regra oficial x relato.

## Estados

```text
VALIDATED
PARTIAL
GAP
CONFLICT
HUMAN_REVIEW
```

## Resultado

Gerar relatório de pesquisa.

---

# 21. FASE 12 — HUMAN GATE DA BASE FACTUAL

## Objetivo

Não permitir redação antes de aprovação.

Tela/CLI deve mostrar:

- quantidade de fontes;
- fontes por classe;
- claims;
- lacunas;
- conflitos;
- itens voláteis;
- itens pendentes.

Usuário poderá:

- aprovar;
- editar;
- rejeitar;
- adicionar fonte;
- mandar pesquisar novamente futuramente.

---

# 22. FASE 13 — MÓDULO OPCIONAL DE PESQUISA ONLINE

Esta fase pode ser implementada depois do MVP manual.

## Objetivo

Permitir localizar novas fontes online.

## Princípio

A pesquisa online não deve alterar as regras de confiabilidade.

Ela apenas amplia a forma de obtenção.

## Fluxo

```text
pergunta de pesquisa
→ busca
→ candidatos
→ qualificação
→ captura
→ armazenamento
→ extração
```

## Requisitos futuros

- busca multilíngue;
- preferência por legislação;
- domínio oficial;
- atualização;
- data;
- origem;
- verificação de versão.

## Observação

A implementação poderá utilizar posteriormente:

- mecanismo de busca;
- OpenAI;
- API externa;
- scraping permitido;
- integração manual.

A escolha não é obrigatória nesta fase.

---

# 23. FASE 14 — GERADOR DE SUMÁRIO

## Objetivo

Gerar a estrutura do e-book somente com base:

- no Project Brief;
- no plano de pesquisa;
- na base factual aprovada;
- nas regras editoriais dos arquivos MD.

## Saída

Capítulos com:

- título;
- finalidade;
- claims necessários;
- boxes sugeridos;
- checklist;
- conteúdo fora de escopo.

## Regra

O módulo não pode introduzir tema sem base factual quando o tema depender de afirmação externa.

---

# 24. FASE 15 — HUMAN GATE DO SUMÁRIO

Usuário deve poder:

- aprovar;
- reordenar;
- excluir;
- incluir;
- solicitar nova versão.

Redação permanece bloqueada até aprovação.

---

# 25. FASE 16 — MÓDULO DE IDENTIDADE VISUAL

## Objetivo

Gerar perguntas e proposta de identidade visual.

Não diagramar ainda.

## Inputs

- tema;
- público;
- país;
- posicionamento;
- tipo de produto;
- marca;
- referências fornecidas.

## Perguntas ao usuário

- institucional, editorial, premium, jovem, minimalista?
- utilizar identidade da GoWorld?
- cores obrigatórias?
- usar fotos?
- usar bandeiras?
- tamanho/formato?
- destino do arquivo?
- referências visuais?

## Saída

`VisualBrief`

## Human gate obrigatório

O usuário aprova antes da diagramação.

---

# 26. FASE 17 — REDATOR DE CAPÍTULOS

## Objetivo

Gerar o e-book capítulo por capítulo.

Não gerar o e-book inteiro em uma única chamada.

## Fluxo

Para cada capítulo:

1. selecionar Claims necessários;
2. buscar Evidences;
3. carregar diretrizes editoriais;
4. redigir;
5. registrar quais Claims foram utilizados.

## Regra crítica

O redator não pode pesquisar nem inventar informação nova.

Se faltar conhecimento:

```text
KNOWLEDGE_GAP
```

O capítulo fica bloqueado.

---

# 27. FASE 18 — REVISOR FACTUAL

## Objetivo

Comparar texto produzido com Claims e Evidences.

## Verificar

- afirmação sem suporte;
- excesso de certeza;
- valor diferente da fonte;
- data incorreta;
- regra transformada em recomendação;
- recomendação transformada em requisito;
- relato tratado como oficial.

## Resultado

Relatório de revisão com localização do problema.

---

# 28. FASE 19 — REVISOR DE ESCOPO E RESPONSABILIDADE

## Objetivo

Garantir que o material permaneça dentro da proposta do produto.

## Procurar

- promessa de aprovação;
- garantia;
- estratégia individual;
- análise personalizada;
- instrução de omissão;
- manipulação de entrevista;
- documento não oficial tratado como obrigatório.

---

# 29. FASE 20 — REVISOR GRAMATICAL

## Objetivo

Eliminar erros linguísticos.

## Verificar

- ortografia;
- pontuação;
- regência;
- concordância;
- sintaxe;
- repetição;
- padronização;
- terminologia.

## Regra

Não alterar significado técnico silenciosamente.

Se houver risco:

`HUMAN_REVIEW`

---

# 30. FASE 21 — REVISOR DE CONSISTÊNCIA GLOBAL

## Objetivo

Analisar o conjunto.

Verificar:

- nomes;
- siglas;
- terminologia;
- valores;
- datas;
- links;
- referências;
- títulos;
- numeração;
- coerência entre capítulos.

---

# 31. FASE 22 — GERADOR DO ARQUIVO EDITORIAL

Antes do PDF final, gerar uma versão canônica.

Preferência inicial:

`Markdown`

e/ou

`HTML`

Isso facilita:

- versionamento;
- diff;
- correções;
- exportação futura.

Não fazer do PDF o único formato de armazenamento.

---

# 32. FASE 23 — DIAGRAMAÇÃO AUTOMÁTICA MVP

## Objetivo

Gerar uma primeira versão visual funcional.

Sugestão inicial:

```text
Markdown
→ HTML
→ CSS
→ PDF
```

Isso é mais controlável do que tentar automatizar diretamente uma ferramenta visual externa.

## Futuro

Poderá haver:

- templates;
- Canva;
- editor visual;
- renderização externa.

---

# 33. FASE 24 — QA DO PDF

## Objetivo

Validar o arquivo gerado.

Verificar automaticamente quando possível:

- número de páginas;
- arquivos vazios;
- links;
- imagens;
- fontes ausentes;
- quebra de elementos;
- texto não renderizado.

Checklist humano:

- legibilidade;
- alinhamento;
- hierarquia;
- estética;
- páginas quebradas;
- capa.

---

# 34. FASE 25 — HUMAN GATE FINAL

Antes de publicar:

Mostrar:

- versão;
- data;
- fontes;
- claims;
- lacunas resolvidas;
- revisões;
- alertas;
- PDF;
- arquivo fonte.

Aprovação explícita obrigatória.

---

# 35. FASE 26 — VERSIONAMENTO

## Objetivo

Salvar versão publicada.

Estrutura:

```text
/project/
    /v1.0/
        ebook.md
        ebook.html
        ebook.pdf
        sources.json
        claims.json
        decisions.json
        manifest.json
```

Nunca sobrescrever uma versão publicada.

---

# 36. FASE 27 — MANIFESTO DO E-BOOK

Cada versão publicada deverá possuir um `manifest`.

Exemplo:

```yaml
project:
version:
published_at:
research_validated_at:
source_count:
claim_count:
volatile_claims:
open_warnings:
visual_brief_version:
content_hash:
```

Esse arquivo permitirá auditoria futura.

---

# 37. FASE 28 — SISTEMA DE ATUALIZAÇÃO

Depois do MVP completo, implementar fluxo de revisão.

## Objetivo

Atualizar produto existente sem começar do zero.

Fluxo:

```text
abrir versão
→ revisar fontes
→ identificar fatos voláteis
→ adicionar fontes novas
→ comparar Claims
→ localizar capítulos afetados
→ atualizar somente o necessário
→ nova versão
```

---

# 38. FASE 29 — MONITORAMENTO DE FONTES

Fase futura.

O sistema poderá registrar URLs oficiais e verificar:

- data de alteração;
- hash;
- conteúdo novo;
- página removida.

Não alterar o e-book automaticamente.

A mudança apenas abre:

`REVIEW_REQUIRED`

---

# 39. FASE 30 — INTERFACE GRÁFICA LOCAL

Somente depois que o núcleo estiver estável.

Opções a avaliar:

- Streamlit;
- NiceGUI;
- PySide;
- interface web local;
- outra solução.

Critério:

A UI deve consumir os serviços do núcleo.

Não colocar regra de negócio diretamente na interface.

---

# 40. FASE 31 — BACKUP E EXPORTAÇÃO

Implementar:

- exportar projeto completo;
- importar projeto;
- backup;
- restaurar;
- copiar banco e arquivos.

Formato portátil preferencial:

`.zip`

com manifest.

---

# 41. FASE 32 — PROVIDER OPENAI OPCIONAL

Somente se desejado.

Adicionar:

`OpenAIProvider`

usando a mesma interface `LLMProvider`.

Possíveis usos:

- pesquisa;
- revisão crítica;
- capítulo complexo;
- comparação com modelo local.

Deve ser opt-in.

O sistema deve continuar funcionando localmente sem API.

---

# 42. FASE 33 — MIGRAÇÃO FUTURA PARA WEB

Não implementar inicialmente.

Mas preservar compatibilidade.

Quando desejado:

```text
núcleo Python
→ API interna
→ frontend web
→ servidor
```

O mesmo banco conceitual e workflow poderão ser reutilizados.

---

# 43. PRIORIDADE DE IMPLEMENTAÇÃO

## MVP 1 — Fundação

Fases:

0 a 5

Resultado:

- sistema roda;
- projeto existe;
- fontes entram;
- documentos viram texto.

---

## MVP 2 — Base factual

Fases:

6 a 12

Resultado:

- fontes qualificadas;
- pesquisa estruturada;
- Claims;
- Evidence;
- lacunas;
- validação humana.

Este é o primeiro grande marco do projeto.

---

## MVP 3 — Geração editorial

Fases:

14 a 21

Resultado:

- sumário;
- visual brief;
- capítulos;
- revisões.

---

## MVP 4 — Produto final

Fases:

22 a 27

Resultado:

- Markdown;
- HTML;
- PDF;
- QA;
- publicação;
- versão.

---

## MVP 5 — Manutenção

Fases:

28 a 31

Resultado:

- atualização;
- monitoramento;
- UI;
- backup.

---

# 44. ORDEM RECOMENDADA DE TRABALHO NO CODEX

A cada nova sessão de desenvolvimento:

1. carregar os três arquivos MD do projeto;
2. informar ao Codex qual fase será implementada;
3. pedir que ele leia o estado atual do repositório;
4. pedir que proponha somente o plano daquela fase;
5. revisar o plano;
6. autorizar implementação;
7. executar testes;
8. testar manualmente;
9. corrigir;
10. aprovar;
11. fazer commit;
12. somente então iniciar a fase seguinte.

---

# 45. PROMPT OPERACIONAL RECOMENDADO PARA O CODEX

Quando iniciar uma fase, utilizar instrução semelhante a:

```text
Leia integralmente os três arquivos de especificação deste projeto:

- base_contexto_sistema_ebooks_vistos.md
- desenho_funcional_modulos_sistema_ebooks_vistos.md
- roadmap_desenvolvimento_codex_gerador_ebooks.md

Estamos implementando exclusivamente a FASE X.

Antes de alterar qualquer arquivo:
1. inspecione o repositório atual;
2. identifique o que já existe;
3. apresente um plano objetivo para esta fase;
4. informe arquivos que pretende criar ou alterar;
5. informe testes que pretende implementar;
6. não implemente fases posteriores;
7. aguarde minha autorização.

Após autorizado:
- implemente somente a fase aprovada;
- preserve módulos já aceitos;
- rode os testes;
- informe resultados;
- explique como testar manualmente no Windows/PowerShell;
- liste limitações e pendências;
- não avance para a próxima fase sem autorização.
```

---

# 46. REGRAS PARA O CODEX QUANDO HOUVER AMBIGUIDADE

O Codex deve:

- preferir solução simples;
- preservar modularidade;
- evitar dependência desnecessária;
- evitar framework pesado sem necessidade;
- não criar infraestrutura futura antes da hora;
- não escolher serviço pago sem autorização;
- não mudar stack sem explicar;
- não remover dados existentes;
- não sobrescrever versões publicadas.

Se houver duas opções tecnicamente razoáveis, apresentar a decisão antes de implementá-la quando ela puder impactar arquitetura futura.

---

# 47. REGRAS PARA DEPENDÊNCIAS

Antes de adicionar biblioteca:

1. justificar;
2. verificar se é necessária;
3. preferir biblioteca madura;
4. registrar no projeto;
5. evitar duplicidade funcional.

Não instalar:

- bancos externos;
- serviços cloud;
- filas;
- Docker;
- Redis;
- frameworks pesados;

sem necessidade concreta ou autorização.

Esses componentes poderão existir futuramente, mas não devem ser adicionados por antecipação.

---

# 48. REGRAS DE PRIVACIDADE LOCAL

Por padrão:

- documentos permanecem locais;
- fontes permanecem locais;
- projetos permanecem locais;
- nenhum conteúdo é enviado para serviço externo sem configuração expressa.

Quando algum provider remoto for habilitado, a aplicação deve deixar isso claro.

---

# 49. REGRAS DE RECUPERAÇÃO DE ERRO

Operação de IA pode falhar.

O sistema deve:

- salvar estado antes da chamada;
- registrar erro;
- permitir repetir;
- não corromper projeto;
- não perder aprovação anterior;
- não duplicar resultado silenciosamente.

---

# 50. REGRAS DE IDEMPOTÊNCIA

Sempre que possível, repetir uma operação deve ser seguro.

Exemplo:

Processar novamente o mesmo arquivo não deve criar cinco fontes duplicadas.

Usar:

- hash;
- identificadores;
- controles de duplicidade.

---

# 51. PROMPTS VERSIONADOS

Prompts do sistema devem ficar em arquivos separados.

Exemplo:

```text
/prompts/
    qualify_source_v1.md
    extract_claims_v1.md
    detect_gaps_v1.md
    generate_outline_v1.md
    write_chapter_v1.md
    factual_review_v1.md
    grammar_review_v1.md
```

Não espalhar prompts grandes dentro do código.

Registrar qual versão do prompt gerou determinado resultado quando relevante.

---

# 52. SAÍDAS ESTRUTURADAS

Sempre que o modelo participar de processo interno, preferir saída estruturada.

Exemplo:

JSON validado por schema.

Evitar depender de interpretação de texto livre para:

- status;
- claims;
- fontes;
- conflitos;
- decisões;
- classificação.

Texto livre deve ser reservado principalmente para conteúdo editorial.

---

# 53. VALIDAÇÃO DE SCHEMA

Toda saída estruturada do modelo deve ser validada pelo código.

Se vier inválida:

- tentar reparo controlado;
- ou repetir a chamada;
- ou bloquear.

Nunca assumir silenciosamente campos ausentes.

---

# 54. BANCO DE DADOS

SQLite será suficiente para o MVP.

O schema deve ser desenhado para permitir futura migração.

Evitar SQL específico desnecessário.

Entidades principais:

- projects;
- sources;
- evidences;
- claims;
- claim_evidences;
- research_questions;
- human_decisions;
- chapters;
- visual_briefs;
- project_versions;
- logs.

---

# 55. ARQUIVOS DO PROJETO

Cada projeto deve possuir diretório próprio.

Exemplo:

```text
data/projects/PROJ-0001/
    sources/
    extracted/
    working/
    outputs/
    versions/
```

O banco armazena referências e metadados.

Arquivos grandes permanecem em filesystem.

---

# 56. IDENTIFICADORES

Usar IDs estáveis.

Exemplos:

```text
PROJ-000001
SRC-000001
CLM-000001
EVD-000001
CHP-000001
```

UUID também é aceitável.

O importante é não depender do nome do arquivo como identificador.

---

# 57. FUTURO CATÁLOGO DE TEMAS

Depois do núcleo:

Criar catálogo de temas pré-definidos.

Exemplo:

```yaml
USA:
  - B1/B2
  - F-1
  - J-1

CANADA:
  - Visitor Visa
  - Study Permit

PORTUGAL:
  - D7
  - D8
```

Cada template poderá sugerir:

- perguntas de pesquisa;
- sumário inicial;
- tipos de fonte esperados.

Mas nunca deve trazer requisitos factuais permanentes sem nova validação.

---

# 58. NÃO CODIFICAR FATOS MIGRATÓRIOS NO SOFTWARE

Regra crítica:

Não hardcodar no código:

- taxas;
- valores mínimos;
- lista de documentos;
- validade;
- prazos;
- requisitos;
- nomes de formulários.

Esses dados pertencem à base de conhecimento do projeto, não à lógica do software.

---

# 59. DEFINIÇÃO DE PRONTO PARA CADA FASE

Uma fase somente está concluída quando:

- código implementado;
- testes passando;
- documentação atualizada;
- teste manual explicado;
- sem erro crítico conhecido;
- usuário validou.

---

# 60. DEFINIÇÃO DE PRONTO PARA O MVP COMPLETO

O MVP será considerado funcional quando for possível:

1. criar projeto;
2. inserir fontes manualmente;
3. extrair texto;
4. qualificar fontes;
5. gerar plano de pesquisa;
6. extrair Claims;
7. manter Evidence;
8. detectar lacunas;
9. exigir aprovação humana;
10. gerar e aprovar sumário;
11. criar Visual Brief;
12. redigir capítulos;
13. revisar fatos;
14. revisar escopo;
15. revisar gramática;
16. revisar consistência;
17. gerar Markdown/HTML;
18. gerar PDF;
19. realizar QA;
20. publicar versão;
21. manter rastreabilidade.

---

# 61. DIRETRIZ FINAL AO CODEX

Este projeto deve ser construído como um sistema editorial auditável, e não como um simples wrapper para um modelo de linguagem.

O modelo de IA é um componente do sistema.

Ele não é o sistema.

A aplicação deve controlar:

- estados;
- fontes;
- evidências;
- aprovações;
- versões;
- erros;
- limites;
- rastreabilidade.

O comportamento desejado é:

> **Fonte antes de fato.  
> Fato antes de texto.  
> Validação antes de avanço.  
> Conteúdo antes de design.  
> Revisão antes de publicação.**

O desenvolvimento deve ser incremental.

Cada módulo deve poder ser testado e aprovado isoladamente.

Não antecipar complexidade.

Não automatizar decisões humanas críticas.

Não depender de infraestrutura externa no MVP.

Construir o núcleo de forma que, futuramente, possa receber:

- OpenAI API;
- busca online;
- interface web;
- subdomínio;
- hospedagem;
- banco remoto;
- múltiplos usuários;

sem reescrever a lógica central.
