# SQL Challenge: Update Advertiser Payment Status

## Description

In this challenge, we aim to update the payment status of advertisers based on their recent payment activity. The task involves combining information from two tables:

1. **`advertiser`**: Contains the current status of advertisers.
2. **`daily_pay`**: Contains recent payment activity.

The updated payment status is determined based on a set of rules for status transitions:

### Status Transition Rules:
| Current Status | Updated Status | Payment on Day T |
|----------------|----------------|------------------|
| NEW            | EXISTING       | Paid             |
| NEW            | CHURN          | Not paid         |
| EXISTING       | EXISTING       | Paid             |
| EXISTING       | CHURN          | Not paid         |
| CHURN          | RESURRECT      | Paid             |
| CHURN          | CHURN          | Not paid         |
| RESURRECT      | EXISTING       | Paid             |
| RESURRECT      | CHURN          | Not paid         |

### Input Tables:
#### `advertiser` Table:
| Column Name | Type   |
|-------------|--------|
| user_id     | string |
| status      | string |

Example:
| user_id | status   |
|---------|----------|
| bing    | NEW      |
| yahoo   | NEW      |
| alibaba | EXISTING |

#### `daily_pay` Table:
| Column Name | Type    |
|-------------|---------|
| user_id     | string  |
| paid        | decimal |

Example:
| user_id | paid  |
|---------|-------|
| yahoo   | 45.00 |
| alibaba | 100.00 |
| target  | 13.00 |

### Expected Output:
| user_id | new_status |
|---------|------------|
| bing    | CHURN      |
| yahoo   | EXISTING   |
| alibaba | EXISTING   |

### Solution:
The following SQL query implements the logic for updating the payment status:

```sql
SELECT
  COALESCE(advertiser.user_id, daily_pay.user_id) AS user_id,
  CASE
    WHEN daily_pay.paid IS NULL THEN 'CHURN'
    WHEN daily_pay.paid IS NOT NULL AND advertiser.status IN ('NEW', 'EXISTING', 'RESURRECT') THEN 'EXISTING'
    WHEN daily_pay.paid IS NOT NULL AND advertiser.status = 'CHURN' THEN 'RESURRECT'
    WHEN daily_pay.paid IS NOT NULL AND advertiser.status IS NULL THEN 'NEW'
  END AS new_status
FROM advertiser
FULL OUTER JOIN daily_pay
  ON advertiser.user_id = daily_pay.user_id
ORDER BY user_id;
```

### Explanation of the Query:
1. **`COALESCE`**:
   Combines `user_id` from both tables, ensuring no advertiser is missed.

2. **CASE Logic**:
   - If no payment is found (`paid IS NULL`), the status becomes `CHURN`.
   - If a payment exists:
     - For `NEW`, `EXISTING`, or `RESURRECT` statuses, it transitions to `EXISTING`.
     - For `CHURN`, it transitions to `RESURRECT`.
     - If the advertiser is new (status `NULL`), it transitions to `NEW`.

3. **`FULL OUTER JOIN`**:
   Ensures all advertisers are included, whether or not they have made payments.

4. **Sorting**:
   Outputs results sorted by `user_id`.

---

# Desafio SQL: Atualizar Status de Pagamento de Anunciantes

## Descrição

Neste desafio, o objetivo é atualizar o status de pagamento dos anunciantes com base em sua atividade recente de pagamento. A tarefa envolve combinar informações de duas tabelas:

1. **`advertiser`**: Contém o status atual dos anunciantes.
2. **`daily_pay`**: Contém a atividade recente de pagamento.

O status atualizado é determinado com base em um conjunto de regras para transições de status:

### Regras de Transição de Status:
| Status Atual  | Status Atualizado | Pagamento no Dia T |
|---------------|-------------------|---------------------|
| NEW           | EXISTING          | Pago                |
| NEW           | CHURN             | Não pago           |
| EXISTING      | EXISTING          | Pago                |
| EXISTING      | CHURN             | Não pago           |
| CHURN         | RESURRECT         | Pago                |
| CHURN         | CHURN             | Não pago           |
| RESURRECT     | EXISTING          | Pago                |
| RESURRECT     | CHURN             | Não pago           |

### Tabelas de Entrada:
#### Tabela `advertiser`:
| Nome da Coluna | Tipo   |
|----------------|--------|
| user_id        | string |
| status         | string |

Exemplo:
| user_id | status   |
|---------|----------|
| bing    | NEW      |
| yahoo   | NEW      |
| alibaba | EXISTING |

#### Tabela `daily_pay`:
| Nome da Coluna | Tipo    |
|----------------|---------|
| user_id        | string  |
| paid           | decimal |

Exemplo:
| user_id | paid  |
|---------|-------|
| yahoo   | 45.00 |
| alibaba | 100.00 |
| target  | 13.00 |

### Resultado Esperado:
| user_id | new_status |
|---------|------------|
| bing    | CHURN      |
| yahoo   | EXISTING   |
| alibaba | EXISTING   |

### Solução:
A seguinte consulta SQL implementa a lógica para atualizar o status de pagamento:

```sql
SELECT
  COALESCE(advertiser.user_id, daily_pay.user_id) AS user_id,
  CASE
    WHEN daily_pay.paid IS NULL THEN 'CHURN'
    WHEN daily_pay.paid IS NOT NULL AND advertiser.status IN ('NEW', 'EXISTING', 'RESURRECT') THEN 'EXISTING'
    WHEN daily_pay.paid IS NOT NULL AND advertiser.status = 'CHURN' THEN 'RESURRECT'
    WHEN daily_pay.paid IS NOT NULL AND advertiser.status IS NULL THEN 'NEW'
  END AS new_status
FROM advertiser
FULL OUTER JOIN daily_pay
  ON advertiser.user_id = daily_pay.user_id
ORDER BY user_id;
```

### Explicação da Consulta:
1. **`COALESCE`**:
   Combina o `user_id` de ambas as tabelas, garantindo que nenhum anunciante seja perdido.

2. **Lógica do CASE**:
   - Se nenhum pagamento for encontrado (`paid IS NULL`), o status torna-se `CHURN`.
   - Se houver pagamento:
     - Para os status `NEW`, `EXISTING` ou `RESURRECT`, ele transita para `EXISTING`.
     - Para `CHURN`, ele transita para `RESURRECT`.
     - Se o anunciante for novo (status `NULL`), ele transita para `NEW`.

3. **`FULL OUTER JOIN`**:
   Garante que todos os anunciantes sejam incluídos, independentemente de terem feito pagamentos.

4. **Ordenação**:
   Produz os resultados ordenados pelo `user_id`.

