# 🧾 Processamento Automático de Notas Fiscais

> Pipeline que **lê anexos de NF por e-mail**, extrai dados estruturados com IA, organiza os arquivos no **Google Drive** e registra tudo numa planilha do **Google Sheets** — sem intervenção humana.

[![n8n](https://img.shields.io/badge/n8n-workflow-orange?logo=n8n)](https://n8n.io)
[![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4o-412991?logo=openai)](https://openai.com)
[![Google Drive](https://img.shields.io/badge/Google_Drive-API-4285F4?logo=googledrive)](https://drive.google.com)
[![Google Sheets](https://img.shields.io/badge/Google_Sheets-API-34A853?logo=googlesheets)](https://sheets.google.com)

---

## 📌 O que este workflow faz

1. **Monitora** o Gmail e detecta e-mails com anexos de notas fiscais (PDF)
2. **Extrai** os arquivos PDF dos e-mails recebidos
3. **Analisa** cada NF com GPT-4o — emitente, CNPJ, valor, data, descrição dos serviços
4. **Faz upload** dos PDFs organizados no Google Drive
5. **Registra** os dados extraídos numa linha da planilha Google Sheets
6. **Processa em lote** — lida com múltiplas NFs no mesmo e-mail

### Caso de uso
Ideal para contabilidades, departamentos financeiros e pequenas empresas que recebem NFs por e-mail e precisam registrá-las manualmente. Elimina horas de trabalho repetitivo.

---

## 🏗️ Arquitetura

```
Gmail Trigger (polling — verifica novos e-mails)
        │
        ▼
   Tem anexo PDF? ──── Não ──── Fim
        │ Sim
        ▼
   Split Out (1 anexo por vez)
        │
        ▼
   Loop Over Items
   ├── Extract from File (lê o PDF)
   ├── AI Agent GPT-4o (extrai dados estruturados da NF)
   │   └── campos: emitente, CNPJ, valor, data, itens
   ├── Upload para Google Drive (arquivo PDF organizado)
   └── Append Row no Google Sheets (dados da NF)
        │
        ▼
   Merge (consolida resultados)
```

---

## 🔧 Integrações

| Serviço | Uso |
|---|---|
| **Gmail** | Trigger de leitura + download de anexos |
| **OpenAI GPT-4o** | Extração de dados estruturados do PDF |
| **Google Drive** | Armazenamento organizado dos arquivos PDF |
| **Google Sheets** | Registro tabular de todas as NFs processadas |

---

## ✅ Pré-requisitos

- [ ] n8n v1.0+ instalado
- [ ] Conta Google com Gmail, Drive e Sheets habilitados via API
- [ ] Chave de API da OpenAI
- [ ] Planilha Google Sheets criada previamente com cabeçalhos

---

## ⚙️ Configuração

### 1. Credenciais necessárias

| Nó | Credencial | Como obter |
|---|---|---|
| `Gmail Trigger` | Gmail OAuth2 | [Google Cloud Console](https://console.cloud.google.com) |
| `OpenAI Chat Model` | OpenAI API Key | [platform.openai.com/api-keys](https://platform.openai.com/api-keys) |
| `Upload file` (Drive) | Google Drive OAuth2 | Google Cloud Console → APIs |
| `Append row in sheet` | Google Sheets OAuth2 | Google Cloud Console → APIs |

### 2. Configurar a planilha

Crie uma planilha com os cabeçalhos desejados, por exemplo:

| Data Recebimento | Emitente | CNPJ | Valor | Data NF | Descrição | Link Drive |
|---|---|---|---|---|---|---|

No nó `Append row in sheet`, configure o ID da sua planilha e o nome da aba.

### 3. Configurar a pasta no Drive

No nó `Upload file`, selecione ou crie a pasta de destino no Google Drive.

### 4. Personalizar a extração

No nó **AI Agent**, edite o prompt para especificar exatamente quais campos extrair das suas NFs. Exemplo:

```
Extraia do PDF de nota fiscal os seguintes campos em JSON:
- emitente (nome da empresa)
- cnpj
- valor_total (número, sem R$)
- data_emissao (formato DD/MM/YYYY)
- descricao_servicos
```

### 5. Importar e ativar

```
Menu → Workflows → Import from File → workflow.json → Activate
```

---

## 📊 Métricas do workflow

| Métrica | Valor |
|---|---|
| Total de nós | 14 |
| Processamento | Lote (múltiplas NFs por e-mail) |
| Modelo LLM | GPT-4o (configurável) |
| Saída | Drive (PDF) + Sheets (dados) |

---

## 💡 Dicas de personalização

- **Filtro por remetente**: configure o Gmail Trigger para monitorar apenas e-mails de fornecedores específicos
- **Notificação**: adicione um nó Slack ou WhatsApp para alertar o time quando uma NF for processada
- **Validação**: adicione um nó `If` para alertar quando o valor extraído for acima de um limite
- **OCR**: para PDFs escaneados (imagem), use o nó OpenAI com visão (Vision) em vez de extração de texto

---

<details>
<summary>🇺🇸 English</summary>

# 🧾 Automatic Invoice Processing

> Pipeline that **reads invoice attachments from email**, extracts structured data with AI, organizes the files in **Google Drive** and logs everything to a **Google Sheets** spreadsheet — with no human intervention.

## 📌 What this workflow does

1. **Monitors** Gmail and detects emails with invoice attachments (PDF)
2. **Extracts** the PDF files from incoming emails
3. **Analyzes** each invoice with GPT-4o — issuer, tax ID, amount, date, service description
4. **Uploads** the organized PDFs to Google Drive
5. **Logs** the extracted data as a row in a Google Sheets spreadsheet
6. **Batch processing** — handles multiple invoices in the same email

### Use case
Ideal for accounting firms, finance departments and small businesses that receive invoices by email and need to log them manually. Eliminates hours of repetitive work.

## 🏗️ Architecture

```
Gmail Trigger (polling — checks for new emails)
        │
        ▼
   Has PDF attachment? ──── No ──── End
        │ Yes
        ▼
   Split Out (1 attachment at a time)
        │
        ▼
   Loop Over Items
   ├── Extract from File (reads the PDF)
   ├── AI Agent GPT-4o (extracts structured invoice data)
   │   └── fields: issuer, tax ID, amount, date, items
   ├── Upload to Google Drive (organized PDF file)
   └── Append Row to Google Sheets (invoice data)
        │
        ▼
   Merge (consolidates results)
```

## 🔧 Integrations

| Service | Use |
|---|---|
| **Gmail** | Reading trigger + attachment download |
| **OpenAI GPT-4o** | Structured data extraction from the PDF |
| **Google Drive** | Organized storage of the PDF files |
| **Google Sheets** | Tabular log of all processed invoices |

## 📊 Workflow metrics

| Metric | Value |
|---|---|
| Total nodes | 14 |
| Processing | Batch (multiple invoices per email) |
| LLM model | GPT-4o (configurable) |
| Output | Drive (PDF) + Sheets (data) |

</details>

---

[← Back to profile](https://github.com/Dimitrearaujo)
