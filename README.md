# Atlas AI – Automated Conversational Agent for Customer Service & Sales

> An end-to-end AI-powered conversational system built with a multi-agent architecture, designed for real-world customer service, lead qualification, follow-up automation, and human escalation.

## Overview

Atlas AI is a production-ready conversational automation system designed to handle inbound messages, understand user intent, maintain conversational context, qualify leads, register product interest, execute follow-ups, and escalate to human support when needed.

The system was built with real operational constraints in mind, such as:

- Fragmented user messages
- Asynchronous conversations
- WhatsApp-like message flows
- Human fallback
- State consistency
- Database idempotency

This project demonstrates practical AI engineering, not demos.

## System Architecture

**Core stack:**

- n8n – Workflow orchestration
- OpenAI (LLMs) – Natural language understanding and generation
- Supabase – Customer database and state management
- Redis – Message buffering and aggregation
- PostgreSQL Memory – Conversational context memory
- Telegram – Internal alerts and human escalation
- Webhook (WhatsApp/Evolution API compatible) – Message ingestion

## High-Level Flow

1. **Message Intake**
   - Receives inbound messages via webhook
   - Ignores self-sent or group messages

2. **Customer Lookup / Creation**
   - Fetches or creates customer records in Supabase
   - Validates lock (trava) state

3. **Message Buffering**
   - Aggregates fragmented messages using Redis
   - Waits for typing pauses (7s)
   - Sends a single consolidated message to the AI

4. **AI Processing**
   - Routes message through specialized agents
   - Maintains conversational memory
   - Enforces strict response rules

5. **Response Partitioning**
   - AI responses are split into smaller chunks
   - Ensures WhatsApp-safe delivery

6. **Follow-up & Interest Tracking**
   - Registers explicit product interest
   - Executes follow-up strategies
   - Updates customer state

7. **Human Escalation**
   - Automatically locks conversation
   - Notifies support via Telegram
   - Silent execution (no AI interference)

## Multi-Agent Design

### Main Agent – Atlas

Responsible for:

- Conversational flow
- Product explanation
- Intent detection
- Short, human-like responses
- Context-aware behavior
- Strict communication rules

Key principles:

- No hallucination
- No over-talking
- Context memory per user
- Emotion check only once per day

### Sub-Agent: Interest Registration

Purpose:

- Persist explicit lead interest in the database

Characteristics:

- No human language
- No user interaction
- Idempotent database writes
- Updates only: `interessado` and `produto_interesse`

This agent is state-only, ensuring clean separation of concerns.

### Sub-Agent: Human Support Escalation

Purpose:

- Trigger human intervention when AI cannot safely proceed

Actions:

- Locks user conversation (`trava = true`)
- Sends alert to Telegram support group
- Silent execution mode

This guarantees:

- No duplicated handling
- Clear ownership transfer
- Operational safety

## Follow-up Agent

A dedicated agent designed exclusively for re-engagement.

Rules:

- Never assumes interest
- No direct selling
- No payment or link pushing
- Short, human, consultative tone
- Always ends with a clear question

Used for:

- Re-activating cold leads
- Unlocking objections
- Maintaining relationship quality

## Message Buffering Strategy

To handle real chat behavior:

- Messages are stored temporarily in Redis
- A delay window waits for user typing completion
- Messages are merged into a single coherent input
- Redis keys are deleted after processing

This prevents:

- Broken AI context
- Premature responses
- Message flooding

## Reliability & Error Handling

- Failure notifications via Telegram
- Safe continuation on non-critical errors
- Conversation locking on escalation
- Controlled retry points

Designed for long-running production usage.

## Why This Project Stands Out

- Real production architecture
- Multi-agent separation of concerns
- Memory-aware conversations
- Human escalation strategy
- State-driven automation
- Designed for WhatsApp-like environments

# Atlas AI – Agente Conversacional Automatizado para Atendimento ao Cliente e Vendas

> Um sistema conversacional de ponta a ponta alimentado por IA, construído com uma arquitetura de múltiplos agentes, projetado para atendimento ao cliente no mundo real, qualificação de leads, automação de follow-up e escalonamento humano.

## Visão Geral (PT-BR)

O Atlas AI é um sistema de automação conversacional pronto para produção, projetado para lidar com mensagens recebidas, entender a intenção do usuário, manter o contexto da conversa, qualificar leads, registrar interesse em produtos, executar follow-ups e escalar para suporte humano quando necessário.

O sistema foi construído com restrições operacionais reais em mente, como:

- Mensagens de usuário fragmentadas
- Conversas assíncronas
- Fluxos de mensagens semelhantes ao WhatsApp
- Fallback para humanos
- Consistência de estado
- Idempotência do banco de dados

Este projeto demonstra engenharia prática de IA, não demonstrações.

## Arquitetura do Sistema

**Stack principal:**

- n8n – Orquestração de fluxo de trabalho
- OpenAI (LLMs) – Compreensão e geração de linguagem natural
- Supabase – Banco de dados de clientes e gerenciamento de estado
- Redis – Buffer e agregação de mensagens
- PostgreSQL Memory – Memória de contexto conversacional
- Telegram – Alertas internos e escalonamento humano
- Webhook (compatível com WhatsApp/Evolution API) – Ingestão de mensagens

## Fluxo de Alto Nível

1. **Entrada de Mensagens**
   - Recebe mensagens recebidas via webhook
   - Ignora mensagens autoenviadas ou de grupo

2. **Consulta / Criação de Cliente**
   - Busca ou cria registros de clientes no Supabase
   - Valida o estado de bloqueio (trava)

3. **Buffer de Mensagens**
   - Agrega mensagens fragmentadas usando o Redis
   - Aguarda pausas na digitação (7s)
   - Envia uma única mensagem consolidada para a IA

4. **Processamento da IA**
   - Roteia a mensagem através de agentes especializados
   - Mantém a memória da conversa
   - Impõe regras de resposta estritas

5. **Particionamento de Resposta**
   - As respostas da IA são divididas em partes menores
   - Garante a entrega segura no WhatsApp

6. **Follow-up e Rastreamento de Interesse**
   - Registra interesse explícito no produto
   - Executa estratégias de follow-up
   - Atualiza o estado do cliente

7. **Escalonamento Humano**
   - Bloqueia automaticamente a conversa
   - Notifica o suporte via Telegram
   - Execução silenciosa (sem interferência da IA)

## Design de Múltiplos Agentes

### Agente Principal – Atlas

Responsável por:

- Fluxo da conversa
- Explicação do produto
- Detecção de intenção
- Respostas curtas e semelhantes às humanas
- Comportamento ciente do contexto
- Regras de comunicação estritas

Princípios chave:

- Sem alucinação
- Sem falar demais
- Memória de contexto por usuário
- Verificação de emoção apenas uma vez por dia

### Subagente: Registro de Interesse

Objetivo:

- Persistir o interesse explícito do lead no banco de dados

Características:

- Sem linguagem humana
- Sem interação com o usuário
- Gravações idempotentes no banco de dados
- Atualiza apenas: `interessado` e `produto_interesse`

Este agente é apenas de estado, garantindo uma separação clara de responsabilidades.

### Subagente: Escalonamento para Suporte Humano

Objetivo:

- Acionar a intervenção humana quando a IA não pode prosseguir com segurança

Ações:

- Bloqueia a conversa do usuário (`trava = true`)
- Envia alerta para o grupo de suporte no Telegram
- Modo de execução silenciosa

Isso garante:

- Sem tratamento duplicado
- Transferência clara de propriedade
- Segurança operacional

## Agente de Follow-up

Um agente dedicado, projetado exclusivamente para reengajamento.

Regras:

- Nunca assume interesse
- Sem venda direta
- Sem envio de links ou pedidos de pagamento
- Tom curto, humano e consultivo
- Sempre termina com uma pergunta clara

Usado para:

- Reativar leads frios
- Desbloquear objeções
- Manter a qualidade do relacionamento

## Estratégia de Buffer de Mensagens

Para lidar com o comportamento real do chat:

- As mensagens são armazenadas temporariamente no Redis
- Uma janela de atraso aguarda a conclusão da digitação do usuário
- As mensagens são mescladas em uma única entrada coerente
- As chaves do Redis são excluídas após o processamento

Isso evita:

- Contexto quebrado da IA
- Respostas prematuras
- Inundação de mensagens

## Confiabilidade e Tratamento de Erros

- Notificações de falha via Telegram
- Continuação segura em erros não críticos
- Bloqueio da conversa em caso de escalonamento
- Pontos de nova tentativa controlados

Projetado para uso em produção de longa duração.

## Por Que Este Projeto se Destaca

- Arquitetura de produção real
- Separação de responsabilidades com múltiplos agentes
- Conversas cientes da memória
- Estratégia de escalonamento humano
- Automação orientada por estado
- Projetado para ambientes semelhantes ao WhatsApp