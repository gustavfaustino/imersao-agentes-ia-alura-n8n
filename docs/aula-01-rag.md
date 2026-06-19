# 📚 Aula 01 — O Cérebro da IA (RAG + Embeddings)

## 🎯 Objetivo da Aula

Nesta etapa, construímos a **base de inteligência do agente**, permitindo que ele responda perguntas com base em **informações reais**, e não apenas no conhecimento genérico do modelo.

O foco foi implementar o conceito de **RAG (Retrieval-Augmented Generation)** utilizando o n8n.

---

## 🧠 O Problema que Estamos Resolvendo

Modelos de linguagem (LLMs) possuem uma limitação importante:

* Eles podem **alucinar respostas**
* Não possuem acesso direto a **dados atualizados ou específicos**
* Não conhecem documentos internos (ex: políticas de empresa)

👉 Exemplo:

> “Quantos dias de férias eu tenho?”

Sem contexto, a IA:

* Pode inventar
* Pode responder genericamente

---

## 💡 A Solução: RAG (Retrieval-Augmented Generation)

RAG é uma arquitetura que combina:

* 🔍 **Busca de informação (Retrieval)**
* 🤖 **Geração de resposta (Generation)**

### 🔄 Fluxo do RAG

```text
Pergunta do usuário
        ↓
Busca em base de conhecimento (Vector Store)
        ↓
Recupera trechos relevantes
        ↓
Envia para o modelo de linguagem
        ↓
Resposta baseada em contexto real
```

👉 Resultado: respostas mais precisas e confiáveis.

---

## 🧬 Embeddings: Como a IA entende textos

Para que o RAG funcione, precisamos transformar texto em algo que a máquina entenda matematicamente.

### 🔹 O que são embeddings?

Embeddings são representações numéricas (vetores) de textos.

Exemplo simplificado:

| Texto                | Vetor (representação)  |
| -------------------- | ---------------------- |
| "férias"             | [0.12, -0.98, 0.44...] |
| "licença remunerada" | [0.10, -0.95, 0.40...] |

👉 Textos semelhantes → vetores próximos

---

## 🏗️ Load Data Flow (Ingestão de Dados)

Nesta aula, criamos um pipeline responsável por:

1. Buscar documentos
2. Processar o conteúdo
3. Transformar em embeddings
4. Armazenar em uma base vetorial

---

## ⚙️ Implementação no n8n

O fluxo criado (`01-load-data-flow.json`) contém os seguintes nós:

### 1. 🔘 Manual Trigger

Inicia o fluxo manualmente.

---

### 2. 🌐 HTTP Request

Responsável por buscar o documento externo:

* Fonte: Manual de RH (arquivo `.txt`)
* Tipo de dado: **não estruturado**

> O conteúdo é carregado como texto bruto

---

### 3. 📄 Data Loader

Função:

* Divide o texto em **chunks (pedaços menores)**

Por quê?

* Modelos têm limite de contexto
* Melhora a precisão da busca semântica

---

### 4. 🧠 Embeddings (Cohere)

Modelo utilizado:

* `embed-multilingual-v3.0`

Função:

* Converter cada chunk em vetor numérico

---

### 5. 🗃️ Vector Store (In-Memory)

Função:

* Armazenar os embeddings
* Permitir busca semântica posteriormente

---

## 🔍 O que é um Vector Store?

É um banco de dados otimizado para:

* Buscar por **similaridade semântica**
* Não por palavras exatas

👉 Diferente de SQL:

| SQL                  | Vector Store              |
| -------------------- | ------------------------- |
| Busca por igualdade  | Busca por similaridade    |
| Dados estruturados   | Dados não estruturados    |
| Ex: `WHERE nome = X` | Ex: “algo parecido com X” |

---

## 🔄 Pipeline Completo

```text
Manual Trigger
      ↓
HTTP Request (carrega documento)
      ↓
Data Loader (quebra em chunks)
      ↓
Embeddings (transforma em vetores)
      ↓
Vector Store (armazena)
```

---

## 🧪 Resultado Final

Após executar o fluxo:

* O sistema possui uma **base de conhecimento carregada**
* A IA consegue:

  * Buscar informações relevantes
  * Responder com base no documento
  * Reduzir alucinações

---

## ⚠️ Limitações

Importante entender:

* A memória é **temporária (in-memory)**
* Se reiniciar o n8n → perde os dados
* Não há atualização automática da base

👉 Melhorias futuras:

* Persistência (ex: Pinecone, Weaviate)
* Atualização incremental de documentos

---

## 💡 Aprendizados da Aula

* RAG é essencial para aplicações reais de IA
* Embeddings são a base da busca semântica
* Separação clara entre:

  * Dados estruturados (SQL)
  * Dados não estruturados (RAG)
* O n8n permite orquestrar tudo de forma visual e poderosa

---

## 🚀 Conclusão

Nesta aula, construímos o “cérebro” do agente.

Sem essa etapa:

* A IA seria apenas um chatbot genérico

Com RAG:

* Ela passa a responder com base em **contexto real e confiável**

👉 Esse é o primeiro passo para sistemas de IA realmente úteis no mundo real.