# 🎂 Aniversariantes — Processador de Planilhas

> Ferramenta web para processar planilhas de aniversariantes, extrair e formatar números de celular, e exportar os dados tratados em CSV — tudo no navegador, sem necessidade de servidor ou instalação.

🔗 **Acesse agora:** [https://analisedadosht.github.io/formata_aniversariantes/](https://analisedadosht.github.io/formata_aniversariantes/)

---

## 📋 Visão Geral

Este projeto nasceu da necessidade de automatizar o tratamento de planilhas Excel contendo dados de clientes aniversariantes. O fluxo original era feito por um script Python; esta versão traz a mesma lógica para uma interface web moderna, acessível diretamente pelo navegador sem nenhuma instalação.

O site recebe uma planilha `.xlsx` ou `.xls`, aplica uma série de transformações nos dados (limpeza de telefones, normalização de nomes, remoção de acentos) e entrega um arquivo `.csv` pronto para uso em ferramentas de disparo de mensagens ou CRM.

**Tudo é processado localmente no navegador.** Nenhum dado é enviado para servidores externos.

---

## ✨ Funcionalidades

- **Upload por arrastar e soltar** ou seleção via botão
- **Campo de data** para informar manualmente a data dos aniversariantes
- **Detecção automática de colunas** `ds_fone` e `ds_razao` na planilha (case-insensitive)
- **Filtragem de celulares**: processa apenas linhas que contêm `CEL:` no campo de telefone
- **Formatação do número**: remove símbolos, espaços, parênteses e traços; adiciona prefixo `55` (Brasil) quando ausente
- **Normalização do nome**: extrai o primeiro nome, converte para maiúsculas e remove acentos
- **Ano dinâmico nas colunas**: as colunas `aniversariante[ANO]Sim` e `aniversariante[ANO]Nao` são geradas com o ano extraído da data informada pelo usuário
- **Log de processamento** em tempo real com detalhes de cada etapa
- **Cards de estatísticas**: total de linhas lidas, registros com celular encontrado e total exportado
- **Pré-visualização** das primeiras 20 linhas processadas diretamente na tela
- **Exportação em CSV** com BOM UTF-8 (compatível com Excel)

---

## 🗂️ Estrutura da Planilha Esperada

A planilha deve conter ao menos as seguintes colunas (os nomes são buscados sem distinção de maiúsculas/minúsculas):

| Coluna     | Descrição                                                                 |
|------------|---------------------------------------------------------------------------|
| `ds_fone`  | Campo de telefone. Pode conter múltiplos números separados por `/`. O sistema extrai apenas os que contêm o prefixo `CEL:`. |
| `ds_razao` | Nome completo do cliente. O sistema extrai apenas o primeiro nome.        |

**Exemplo de conteúdo esperado na planilha:**

| ds_razao                  | ds_fone                                              |
|---------------------------|------------------------------------------------------|
| JOAO CARLOS SILVA         | TEL: (11) 3300-0000 / CEL: (11) 99900-0000          |
| MARIA FERNANDA SOUZA      | CEL: (21) 90000-0001                                 |
| PEDRO AUGUSTO LIMA        | TEL: (31) 2200-0000                                  |
| BEATRIZ OLIVEIRA SANTOS   | TEL: (41) 3000-0000 / CEL: (41) 90009-9999          |

> Neste exemplo, a linha de **PEDRO AUGUSTO LIMA** seria descartada por não conter `CEL:` no campo de telefone.

**Exemplo de valor válido para `ds_fone`:**
```
TEL: (11) 3300-0000 / CEL: (11) 99900-0000
```

**Exemplo de valor inválido (linha será ignorada):**
```
TEL: (11) 3300-0000
```

---

## 📤 Estrutura do CSV Exportado

O arquivo gerado contém as seguintes colunas:

| Coluna                      | Descrição                                                        |
|-----------------------------|------------------------------------------------------------------|
| `NOME`                      | Primeiro nome do cliente, sem acentos, em maiúsculas            |
| `TELEFONE`                  | Número formatado com código do país (`55`), sem símbolos        |
| `DATA`                      | Data informada pelo usuário no formato `dd/mm/aaaa`             |
| `aniversariante[ANO]Sim`    | Coluna auxiliar com o valor `aniversariante[ANO]Sim`            |
| `aniversariante[ANO]Nao`    | Coluna auxiliar com o valor `aniversariante[ANO]Nao`            |

> O `[ANO]` é substituído automaticamente pelo ano extraído da data informada pelo usuário. Por exemplo, se a data digitada for `20/03/2026`, as colunas serão `aniversariante2026Sim` e `aniversariante2026Nao`.

**Exemplo de saída CSV gerada** (com dados fictícios, data `20/03/2026`):

```csv
NOME,TELEFONE,DATA,aniversariante2026Sim,aniversariante2026Nao
JOAO,5511999900000,20/03/2026,aniversariante2026Sim,aniversariante2026Nao
MARIA,5521900000001,20/03/2026,aniversariante2026Sim,aniversariante2026Nao
BEATRIZ,5541900009999,20/03/2026,aniversariante2026Sim,aniversariante2026Nao
```

---

## 🚀 Como Usar

### Opção 1 — Acessar pelo navegador (recomendado)

Acesse diretamente: [https://analisedadosht.github.io/formata_aniversariantes/](https://analisedadosht.github.io/formata_aniversariantes/)

Não é necessário instalar nada.

### Opção 2 — Usar localmente

1. Clone ou baixe este repositório:
   ```bash
   git clone https://github.com/analisedadosht/formata_aniversariantes.git
   ```
2. Abra o arquivo `aniversariantes.html` diretamente no navegador (duplo clique no arquivo).

### Passo a passo de uso

1. **Faça o upload da planilha** arrastando o arquivo para a área indicada ou clicando para selecionar. Formatos aceitos: `.xlsx` e `.xls`.
2. **Informe a data** dos aniversariantes no campo de data. O ano desta data será usado nos nomes das colunas do CSV.
3. **Clique em "Processar Planilha"**. O botão só é habilitado quando tanto o arquivo quanto a data estiverem preenchidos.
4. **Acompanhe o log** de processamento para verificar as etapas executadas e possíveis avisos.
5. **Confira a pré-visualização** das primeiras 20 linhas processadas.
6. **Clique em "Baixar CSV"** para fazer o download do arquivo tratado.

---

## 🔧 Lógica de Processamento

O tratamento aplicado a cada linha da planilha segue esta sequência:

```
1. Ler o valor do campo ds_fone
2. Verificar se contém "CEL:" → se não, descartar a linha
3. Dividir o campo por "/" e filtrar apenas os segmentos com "CEL:"
4. Pegar o primeiro número de celular encontrado
5. Remover o prefixo "CEL:"
6. Adicionar "55" no início, se o número ainda não começar com "55"
7. Remover espaços, hífens, parênteses e outros caracteres especiais
8. Ler o campo ds_razao
9. Extrair o primeiro nome (dividir por espaço e pegar o índice [0])
10. Converter para maiúsculas
11. Remover acentos (normalização Unicode NFKD)
12. Montar o objeto de saída com NOME, TELEFONE, DATA e colunas dinâmicas de ano
```

**Exemplo de transformação** (dados fictícios):

| Etapa | Valor |
|---|---|
| `ds_fone` original | `TEL: (11) 3300-0000 / CEL: (11) 99900-0000` |
| Após filtrar `CEL:` | `CEL: (11) 99900-0000` |
| Após remover prefixo | `(11) 99900-0000` |
| Após adicionar `55` | `55(11) 99900-0000` |
| **TELEFONE final** | `5511999900000` |
| `ds_razao` original | `JOAO CARLOS SILVA` |
| **NOME final** | `JOAO` |

---

## 🛠️ Tecnologias Utilizadas

| Tecnologia | Versão | Uso |
|---|---|---|
| HTML5 / CSS3 / JavaScript | — | Interface e lógica de processamento |
| [SheetJS (xlsx)](https://sheetjs.com/) | 0.18.5 | Leitura de arquivos `.xlsx` e `.xls` no navegador |
| [Syne](https://fonts.google.com/specimen/Syne) | — | Tipografia de títulos |
| [DM Mono](https://fonts.google.com/specimen/DM+Mono) | — | Tipografia de corpo e dados |

> Todas as dependências são carregadas via CDN. Não há necessidade de instalação de pacotes ou gerenciador de dependências.

---

## 📦 Requisitos

- Navegador moderno com suporte a ES6+ (Chrome, Firefox, Edge, Safari — versões recentes)
- Conexão com internet apenas para carregar as fontes e a biblioteca SheetJS via CDN

**Para uso offline**, baixe localmente a biblioteca SheetJS e as fontes e atualize os caminhos no `<head>` do HTML.

---

## 📁 Estrutura do Projeto

```
📦 formata_aniversariantes
 ┣ 📄 aniversariantes.html   # Aplicação completa (HTML + CSS + JS em arquivo único)
 ┗ 📄 README.md              # Este arquivo
```

---

## ⚠️ Observações Importantes

- A planilha **deve conter as colunas `ds_fone` e `ds_razao`** com esses nomes (a busca é case-insensitive).
- Linhas onde `ds_fone` **não contenha `CEL:`** são ignoradas — apenas celulares são processados.
- O campo de telefone suporta **múltiplos números separados por `/`**, mas somente o **primeiro celular** encontrado é extraído por linha.
- Números que **já começam com `55`** não recebem o prefixo novamente, evitando duplicação.
- O CSV é exportado com **BOM UTF-8**, garantindo que caracteres especiais sejam exibidos corretamente ao abrir no Microsoft Excel.
- O processamento ocorre **100% no lado do cliente (browser)**. Nenhum dado da planilha é transmitido pela internet.

---

## 🖥️ Interface

A interface foi desenvolvida com tema escuro de alto contraste. Seções presentes:

1. **Área de upload** — Drag & drop ou seleção manual do arquivo
2. **Campo de data** — Input para informar a data dos aniversariantes
3. **Botão de processar** — Habilitado somente quando arquivo e data estão preenchidos
4. **Log de processamento** — Saída em tempo real com status de cada etapa
5. **Cards de estatísticas** — Total de linhas, registros com celular e total exportado
6. **Pré-visualização** — Tabela com as primeiras 20 linhas tratadas
7. **Botão de download** — Exporta o CSV final

---

## 📜 Origem do Projeto

Este site é uma reimplementação em JavaScript/HTML de um script Python original que realizava o mesmo tratamento de dados usando `pandas` e `xlrd`. A migração para o navegador elimina a necessidade de ambiente Python instalado e torna a ferramenta acessível a qualquer usuário, sem conhecimentos técnicos.

---

## 📄 Licença

Este projeto é de uso interno. Sinta-se livre para adaptar conforme a necessidade do seu contexto.
