# 📊 Formatador de Dados

> Ferramenta web para processar planilhas Excel, formatar números de celular e exportar arquivos CSV prontos para uso — tudo no navegador, sem instalação ou servidor.

🔗 **Acesse agora:** [https://analisedadosht.github.io/formata_dados/](https://analisedadosht.github.io/formata_dados/)

---

## 📋 Visão Geral

O **Formatador de Dados** é uma aplicação web de página única que automatiza o tratamento de planilhas Excel. O usuário faz o upload do arquivo, seleciona a finalidade do CSV a ser gerado e clica em processar. A ferramenta aplica limpeza e normalização nos dados e entrega um arquivo `.csv` estruturado, pronto para importação em ferramentas de comunicação ou CRM.

**Todo o processamento acontece localmente no navegador.** Nenhum dado é transmitido para servidores externos.

---

## ✨ Funcionalidades

- **Upload por arrastar e soltar** ou seleção via botão
- **Seletor de categoria** com três finalidades de exportação
- **Campo de data** disponível apenas quando necessário para a categoria selecionada
- **Filtragem automática**: processa apenas registros que possuem número de celular identificado
- **Formatação de telefone**: remove símbolos e garante o código do país
- **Normalização de nome**: extrai o primeiro nome, remove acentos e padroniza em maiúsculas
- **Log de processamento** em tempo real
- **Cards de estatísticas**: total de linhas, registros válidos e total exportado
- **Pré-visualização** das primeiras 20 linhas tratadas
- **Exportação em CSV** com nome de arquivo dinâmico e codificação UTF-8 compatível com Excel

---

## 🗂️ Estrutura da Planilha Esperada

A planilha deve conter ao menos as seguintes colunas (a busca é case-insensitive):

| Coluna     | Descrição                                                                                      |
|------------|-----------------------------------------------------------------------------------------------|
| `ds_fone`  | Campo de telefone. Suporta múltiplos números separados por `/`. Apenas entradas com `CEL:` são processadas. |
| `ds_razao` | Nome completo do registro. O sistema extrai apenas o primeiro nome.                           |

**Exemplo de conteúdo esperado:**

| ds_razao              | ds_fone                                           |
|-----------------------|---------------------------------------------------|
| CARLOS MENDES         | TEL: (11) 3300-0000 / CEL: (11) 99900-0000       |
| ANA LUCIA FERREIRA    | CEL: (21) 99000-0009                              |
| ROBERTO COSTA         | TEL: (31) 2200-0000                               |
| PAULA BRITO CARVALHO  | TEL: (41) 3000-0000 / CEL: (41) 99009-9999       |

> A linha de **ROBERTO COSTA** seria descartada por não conter `CEL:` no campo de telefone.

---

## 🗂️ Categorias e Colunas do CSV

A estrutura do CSV exportado varia conforme a categoria selecionada:

### 🎂 Aniversário
Requer preenchimento do campo de data.

| Coluna            | Descrição                                      |
|-------------------|------------------------------------------------|
| `TELEFONE`        | Número formatado com código do país            |
| `NOME`            | Primeiro nome, sem acentos, em maiúsculas      |
| `DATA`            | Data informada pelo usuário (`dd/mm/aaaa`)     |
| `atendent`        | Coluna auxiliar com valor fixo `atendent`      |
| `ficaparaproxima` | Coluna auxiliar com valor fixo `ficaparaproxima` |

### 📋 Vencimento de Receita
Campo de data não utilizado nesta categoria.

| Coluna                    | Descrição                                            |
|---------------------------|------------------------------------------------------|
| `TELEFONE`                | Número formatado com código do país                  |
| `NOME`                    | Primeiro nome, sem acentos, em maiúsculas            |
| `atendent`                | Coluna auxiliar com valor fixo `atendent`            |
| `ficaparaproxima`         | Coluna auxiliar com valor fixo `ficaparaproxima`     |
| `podemosFalarFuturamente` | Coluna auxiliar com valor fixo `podemosFalarFuturamente` |

### 🏷️ Promoção
Campo de data não utilizado nesta categoria. Nome não incluído.

| Coluna            | Descrição                                        |
|-------------------|--------------------------------------------------|
| `TELEFONE`        | Número formatado com código do país              |
| `atendent`        | Coluna auxiliar com valor fixo `atendent`        |
| `ficaparaproxima` | Coluna auxiliar com valor fixo `ficaparaproxima` |

---

## 📁 Nome do Arquivo Exportado

O nome do CSV gerado segue a estrutura:

```
dados_formatados_[categoria]_[data].csv
```

**Exemplos:**

```
dados_formatados_aniversario_20-03-2026.csv
dados_formatados_vencimento_receita_20-03-2026.csv
dados_formatados_promocao_20-03-2026.csv
```

> Para as categorias **Vencimento de Receita** e **Promoção**, a data no nome do arquivo corresponde à data do processamento, já que o campo de data não é utilizado nessas categorias.

---

## 🚀 Como Usar

### Opção 1 — Acessar pelo navegador (recomendado)

Acesse: [https://analisedadosht.github.io/formata_aniversariantes/](https://analisedadosht.github.io/formata_aniversariantes/)

Nenhuma instalação necessária.

### Opção 2 — Usar localmente

1. Clone o repositório:
   ```bash
   git clone https://github.com/analisedadosht/formata_aniversariantes.git
   ```
2. Abra o arquivo `index.html` diretamente no navegador.

### Passo a passo

1. **Faça o upload** da planilha arrastando o arquivo ou clicando para selecionar (`.xlsx` ou `.xls`)
2. **Selecione a categoria** desejada: Aniversário, Vencimento de Receita ou Promoção
3. **Informe a data** (apenas para a categoria Aniversário)
4. **Clique em "Processar Planilha"**
5. **Acompanhe o log** para verificar o processamento
6. **Confira a pré-visualização** das primeiras 20 linhas
7. **Clique em "Baixar CSV"** para exportar

---

## 🔧 Lógica de Processamento

```
1. Ler o campo ds_fone de cada linha
2. Verificar se contém "CEL:" → se não, descartar a linha
3. Dividir o campo por "/" e isolar o segmento com "CEL:"
4. Extrair o primeiro número de celular encontrado
5. Remover o prefixo "CEL:"
6. Adicionar código do país no início, se ausente
7. Remover espaços, hífens, parênteses e caracteres especiais
8. Ler o campo ds_razao (quando necessário para a categoria)
9. Extrair o primeiro nome
10. Converter para maiúsculas e remover acentos (normalização Unicode NFKD)
11. Montar o objeto de saída conforme as colunas da categoria selecionada
```

---

## 🛠️ Tecnologias Utilizadas

| Tecnologia | Versão | Uso |
|---|---|---|
| HTML5 / CSS3 / JavaScript | — | Interface e lógica de processamento |
| [SheetJS (xlsx)](https://sheetjs.com/) | 0.18.5 | Leitura de arquivos `.xlsx` e `.xls` no navegador |
| [Syne](https://fonts.google.com/specimen/Syne) | — | Tipografia de títulos |
| [DM Mono](https://fonts.google.com/specimen/DM+Mono) | — | Tipografia de corpo e dados |

> Todas as dependências são carregadas via CDN. Nenhum gerenciador de pacotes é necessário.

---

## 📦 Requisitos

- Navegador moderno com suporte a ES6+ (Chrome, Firefox, Edge, Safari — versões recentes)
- Conexão com internet para carregar fontes e a biblioteca SheetJS via CDN

**Para uso offline:** baixe a biblioteca SheetJS e as fontes localmente e atualize os caminhos no `<head>` do HTML.

---

## 📁 Estrutura do Projeto

```
📦 formata_aniversariantes
 ┣ 📄 aniversariantes.html   # Aplicação completa em arquivo único
 ┗ 📄 README.md              # Este arquivo
```

---

## ⚠️ Observações

- A planilha deve conter as colunas `ds_fone` e `ds_razao` (busca case-insensitive)
- Linhas sem `CEL:` no campo de telefone são descartadas automaticamente
- Quando há múltiplos números no campo, apenas o **primeiro celular** é extraído
- Números que já iniciam com o código do país não recebem o prefixo novamente
- O CSV é exportado com **BOM UTF-8** para compatibilidade com Microsoft Excel
- **Nenhum dado da planilha é enviado para a internet** — todo o processamento ocorre no navegador

---

## 📄 Licença

Este projeto é de uso interno. Sinta-se livre para adaptar conforme a necessidade do seu contexto.
