# SQS

# STANDARD 
- ordem BEST-EFFORT ORDERING (não garantida)
- at-least-once delivery (pode haver duplicatas)
- escala indefinidamente
- mais rapido
- qualquer nome

Usar quando:
- Alta vazão é necessaria
- ordem das mensagens não é critica
- aplicação pode lidar com mensagens duplicadas (idempotencia)
- custo e simplicidade são prioritarios

Casos de uso:
- processamento de logs
- notificações por email
- processamento de imagens

---

# FIFO
- ordem estritamente garantida (First-in-first-out)
- sem duplicatas
- até 3mil mensagens/s com batching. 300 mensagens/s sem batch
- maior latencia
- nome deve terminar com .fifo
- message group id obrigatorio
- deduplication id obrigatorio ou baseado em conteudo

Usar quando:
- ordem de processamento é critica
- duplicatas bão são aceitaveis

Caso de use:
- transações financeiras
- pedidos de e-commerce 

--- 

# Visibility Timeout
Periodo em que uma mensagem fica invisivel na fila apos ser recebida por um consumidor. 
Durante esse tempo, nenhum outro consumidor pode processar a mesma mensagem.

Configurações:
- Padrão: 30 segundos
- Minimo: 0 segundos
- Maximo: 12 horas (43.200 segundos)
- Pode ser alterado com ChangeMessageVisibility API

--- 

# Dead Letter Queue (DLQ)
Fila especial para armazenar mensagens que falharam no processamento após multiplas tentativas.

Beneficios:
- Isola mensagens com problemas
- Facilita debugging e analise de falhas
- Permite auditoria de falhas

Importante: DLQ deve ser do mesmo tipo da fila original (standart/FIFO)

---

# Long Polling vs Short Polling

Short Polling (padrão)
- Retorna imediatamente
- waitTimeSeconds: 0
- Maior custo
- Muitas respostas vazias
- menor latencia
- mais request

Long Polling (recomendado)
- aguarda até timeout ou mensagem chegar
- waitTimeSeconds: 1 a 20 segundos
- menos chamadas (menor custo)
- poucas ou nenhuma resposta vazia
- latencia ligeiramente maior (aguarda até timeout)
- menos requests
- use sempre que possivel

Configurar Long Polling:
1. Queue level:
- ReceiveMessageWaitTimeSeconds > 0 na criação da fila
- Aplica long polling para todos os consumers
- Valor recomendado: 20 segundos (maximo)
2. API call:
- WaitTimeSeconds na chamada ReceiveMessage
- Sobrescreve configuração da fila para aquela chamada

**Importante:**
- WaitTimeSeconds == 0: Short Polling (padrão)
- WaitTimeSeconds > 0: Long Polling (recomendado)
- Long Polling reduz custo em até 50% comparado ao short polling
- Sempre prefira long polling para economia e eficiência

## Arquitetura recomendada:
Fila principal (Long Polling = 20s) > Lambda > Se falhar 3x > DLQ > CloudWatch Alarm > SNS







