# 🗄️ Aula 02 — Memória e Dados Estruturados (RAG + SQL)

## 🎯 Objetivo da Aula

Após construir a base de conhecimento com RAG, o próximo passo foi adicionar **dados reais e personalizados** ao agente.

Nesta aula, evoluímos de um agente “informativo” para um agente **contextual e orientado a dados**, integrando:

* Banco de dados relacional (MySQL)
* Parâmetros dinâmicos controlados pela IA
* Arquitetura híbrida (RAG + SQL)

---

## 🧠 O Problema que Ainda Existia

Na Aula 01, o agente conseguia:

* Responder perguntas com base em documentos
* Evitar alucinações

Porém, ele **não conseguia personalizar respostas**.

👉 Exemplo:

> “Quantos dias de férias eu tenho?”

Com apenas RAG:

* A IA explicaria a política de férias
* Mas não saberia **o saldo do usuário**

---

## 💡 A Solução: Dados Estruturados (SQL)

Para resolver isso, adicionamos um banco de dados relacional:

* 📊 Estruturado
* 📌 Preciso
* 👤 Personalizado por usuário

---

## 🗄️ Integração com MySQL

O agente agora possui acesso a uma tabela:

```sql id="k2m9z1"
funcionarios
- id
- nome
- saldo_ferias
- banco_horas
```

👉 Isso permite respostas como:

> “Você possui 12 dias de férias disponíveis.”

---

## ⚡ Arquitetura Híbrida (RAG + SQL)

Agora o agente combina dois mundos:

### 🔹 Dados não estruturados (RAG)

* Políticas de RH
* Regras gerais
* Documentação

### 🔹 Dados estruturados (SQL)

* Informações pessoais
* Dados numéricos
* Registros específicos

---

### 🔄 Fluxo Híbrido

```text id="l0p9ds"
Pergunta do usuário
        ↓
Agente IA
   ↓           ↓
RAG         MySQL
   ↓           ↓
Contexto + Dados reais
        ↓
Resposta final
```

👉 Esse modelo é conhecido como **Hybrid AI**

---

## 🤖 Implementação no n8n

O fluxo principal (`02-fluxo-rh-agente.json`) introduz um novo conceito:

➡️ **Tools (Ferramentas do agente)**

---

## 🧰 Tools do Agente

O agente agora possui acesso a:

### 1. 🔍 Vector Store (RAG)

* Busca semântica
* Responde dúvidas gerais

---

### 2. 🗃️ MySQL Tool

Responsável por:

* Executar queries no banco
* Retornar dados estruturados

---

## 🔎 Parâmetros Dinâmicos ($fromAI)

Aqui está um dos pontos mais avançados da aula.

### 💡 O que é isso?

Permite que a própria IA:

* Interprete a mensagem do usuário
* Extraia informações relevantes
* Monte a query automaticamente

---

### 🧪 Exemplo prático

Entrada do usuário:

> “Quais são os dados do João Silva?”

A IA gera internamente:

```sql id="f0x9wq"
SELECT * FROM funcionarios
WHERE nome LIKE '%João Silva%'
```

👉 Isso acontece graças ao `$fromAI`

---

### ⚠️ Observação importante

Você definiu no fluxo:

* Uso obrigatório de nome completo
* Busca parcial com `LIKE %nome%`

Isso evita:

* Erros de busca
* Queries inválidas
* Problemas de correspondência

---

## 🧠 Prompt Engineering (Regras do Agente)

O comportamento do agente é fortemente controlado por instruções:

* Responder apenas sobre RH
* Pedir nome do usuário se necessário
* Não inventar dados
* Usar SQL apenas quando apropriado

👉 Isso funciona como um **contrato de comportamento da IA**

---

## 🔄 Pipeline Completo do Agente

```text id="3v9mka"
Usuário envia mensagem
        ↓
AI Agent recebe input
        ↓
Decide quais tools usar:
   - Vector Store (RAG)
   - MySQL (dados reais)
        ↓
Executa buscas
        ↓
Combina contexto + dados
        ↓
Gera resposta final
```

---

## 🧪 Exemplo de Execução

### Entrada:

> “Quantos dias de férias eu tenho?”

### Processo:

1. IA identifica necessidade de dado pessoal
2. Solicita nome (se necessário)
3. Executa query no MySQL
4. Recupera saldo de férias
5. Gera resposta

---

### Entrada:

> “Como funciona o banco de horas?”

### Processo:

1. IA usa RAG
2. Busca no Vector Store
3. Retorna explicação baseada no manual

---

## ⚠️ Limitações

* Dependência de dados corretos no banco
* Query pode falhar se nome não for encontrado
* Segurança precisa ser considerada (exposição de dados)

👉 Melhorias futuras:

* Autenticação de usuário
* Controle de acesso por perfil
* Logs de consulta

---

## 💡 Aprendizados da Aula

* Diferença clara entre:

  * Dados estruturados (SQL)
  * Dados não estruturados (RAG)
* Como criar um agente híbrido
* Uso de IA para gerar queries dinamicamente
* Importância de controle via prompt

---

## 🚀 Conclusão

Nesta aula, transformamos o agente em algo muito mais poderoso:

Antes:

* Apenas informativo

Agora:

* Contextual
* Personalizado
* Orientado a dados reais

👉 Esse é o tipo de arquitetura utilizada em sistemas de IA corporativos modernos.
