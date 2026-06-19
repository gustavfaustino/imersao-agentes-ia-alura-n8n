# 📱 Aula 03 — Integração com Telegram, Guardrails e Sessões

## 🎯 Objetivo da Aula

Nesta etapa final, transformamos o agente em uma aplicação real, acessível por qualquer usuário através do Telegram.

Saímos de um ambiente controlado (n8n) e construímos um sistema completo com:

* Interface de comunicação (Telegram)
* Processamento automático via Webhooks
* Controle de comportamento (Guardrails)
* Memória individual por usuário (Session ID)

---

## 🌎 O Problema

Até a Aula 02, o agente:

* Funcionava apenas dentro do n8n
* Não possuía interface real com usuários
* Não tinha controle robusto de entrada

👉 Ou seja: era um protótipo técnico, não um produto.

---

## 💡 A Solução

Criamos um sistema completo de atendimento com:

* Entrada via Telegram
* Processamento inteligente de mensagens
* Respostas automáticas em tempo real

---

## 📲 Integração com Telegram

O Telegram atua como:

* Interface de entrada (input)
* Interface de saída (output)

---

### 🔄 Fluxo básico

```text
Usuário → Telegram → Webhook (n8n) → IA → Resposta → Telegram
```

---

## 🔗 Webhooks e Automação

O fluxo começa com o nó:

### 🔹 Telegram Trigger

Função:

* Escutar mensagens enviadas ao bot
* Disparar o workflow automaticamente

👉 Isso transforma o sistema em **event-driven**

---

## 🧠 Arquitetura do Fluxo

O fluxo completo implementado segue esta lógica:

```text
Mensagem do usuário
        ↓
Classificador de intenção (LLM)
        ↓
Switch (roteamento)
        ↓
AI Agent (RAG + SQL + memória)
        ↓
Resposta enviada ao Telegram
```

---

## 🤖 Classificação de Intenção (Guardrail inicial)

Antes de chegar no agente principal, a mensagem passa por um classificador.

### 🔹 Objetivo

* Entender a intenção do usuário
* Evitar processamento desnecessário
* Filtrar entradas fora do escopo

---

### 🧪 Categorias definidas

* **RH** → perguntas válidas do domínio
* **SAUDAÇÃO** → "oi", "bom dia"
* **AJUDA** → “o que você faz?”
* **GERAL** → qualquer outro caso

---

### ⚙️ Implementação

Utilizado modelo da Google Gemini como classificador leve.

👉 A IA responde apenas com uma label (ex: "RH")

---

## 🔀 Switch (Roteamento Inteligente)

Após a classificação:

* O fluxo é direcionado conforme a intenção

### Exemplo:

| Intenção | Ação                |
| -------- | ------------------- |
| RH       | Envia para AI Agent |
| SAUDAÇÃO | Resposta simples    |
| AJUDA    | Explicação do bot   |
| GERAL    | Fallback amigável   |

👉 Isso reduz custo e melhora UX.

---

## 🛡️ Guardrails (Controle de Comportamento)

Os guardrails garantem que o agente:

* Não saia do escopo (RH)
* Não invente informações
* Siga regras definidas

---

### 📌 Onde estão os guardrails?

1. **Classificador (antes do agente)**
2. **Prompt do AI Agent**
3. **Switch (controle de fluxo)**

👉 Camadas múltiplas = maior segurança

---

## 🧠 Memória por Usuário (Session ID)

Aqui está outro ponto crítico de produção.

---

### 💡 Problema

Sem controle de sessão:

* Todas as conversas se misturam
* Não há contexto contínuo

---

### ✅ Solução

Cada usuário possui um identificador único:

```text
chat.id (Telegram)
```

---

### ⚙️ Implementação

* O `chat.id` é usado como **Session ID**
* Associado ao nó de memória (Buffer Memory)

👉 Resultado:

* Cada usuário tem sua própria conversa
* O agente lembra interações anteriores

---

## 🔄 Pipeline Completo

```text
Usuário envia mensagem
        ↓
Telegram Trigger (Webhook)
        ↓
Classificador (Gemini)
        ↓
Switch (roteamento)
        ↓
AI Agent:
   - Memória (Session ID)
   - RAG (Vector Store)
   - SQL (MySQL)
        ↓
Resposta gerada
        ↓
Telegram (envio da mensagem)
```

---

## 🧪 Exemplo de Execução

### Entrada:

> “Oi”

→ Classificação: SAUDAÇÃO
→ Resposta simples

---

### Entrada:

> “Quantos dias de férias eu tenho?”

→ Classificação: RH
→ AI Agent:

* Usa memória (identifica usuário)
* Consulta MySQL
  → Retorna resposta personalizada

---

### Entrada:

> “Qual o sentido da vida?”

→ Classificação: GERAL
→ Resposta controlada (fallback)

---

## ⚠️ Limitações

* Dependência do Telegram API
* Classificação pode falhar em edge cases
* Sem autenticação real de usuário

---

## 💡 Melhorias Futuras

* Autenticação de usuários
* Logs de conversa
* Monitoramento de uso
* Deploy em produção (cloud)

---

## 📈 Aprendizados da Aula

* Como integrar IA com aplicações reais
* Uso de Webhooks para sistemas reativos
* Implementação de guardrails em múltiplas camadas
* Gestão de estado com Session ID
* Arquitetura completa de um agente conversacional

---

## 🚀 Conclusão

Nesta aula, o projeto deixa de ser um experimento e se torna um **produto funcional**.

Agora o agente:

* Está acessível por usuários reais
* Responde em tempo real
* Mantém contexto individual
* Opera com segurança e controle

👉 Esse é o ponto onde engenharia de IA encontra produto real.
