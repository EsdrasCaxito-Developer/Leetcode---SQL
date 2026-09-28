 README — Estudos de Banco de Dados com LeetCode✓

# 📚 Estudos de Banco de Dados com LeetCode

 Este repositório foi criado com o objetivo de **estudar SQL e Banco de Dados através dos exercícios do LeetCode**, organizando os problemas por dificuldade e registrando o raciocínio utilizado para resolvê-los.

 A ideia é que este material sirva tanto para o meu próprio aprendizado quanto para **ajudar outros estudantes que também estão aprendendo SQL**.
 
 > 💡 O objetivo não é apenas encontrar a resposta, mas entender **por que a solução funciona**.

---

 ## 🎯 Objetivos

 - Aprender SQL na prática.
- Desenvolver raciocínio para resolver problemas de Banco de Dados.
- Entender consultas `SELECT`, `WHERE`, `GROUP BY`, `HAVING`, `JOIN`, subqueries e funções de janela.
- Aprender a identificar padrões nos exercícios.
- Registrar diferentes formas de resolver um mesmo problema.
- Criar um material que possa ajudar outros estudantes.
- Evoluir gradualmente de exercícios simples para problemas mais complexos.

---

 # 📊 Ordem dos exercícios

 Os exercícios estão organizados inicialmente do **mais fácil para o mais difícil**.

 | # | Exercício | Dificuldade | Principais conceitos |
| --- | --- | --- | --- |
| 1 | [175\. Combine Two Tables](<https://leetcode.com/problems/combine-two-tables/>) | 🟢 Fácil | `LEFT JOIN` |
| 2 | [181\. Employees Earning More Than Their Managers](<https://leetcode.com/problems/employees-earning-more-than-their-managers/>) | 🟢 Fácil | `JOIN`, comparação |
| 3 | [182\. Duplicate Emails](<https://leetcode.com/problems/duplicate-emails/>) | 🟢 Fácil | `GROUP BY`, `HAVING`, `COUNT` |
| 4 | [183\. Customers Who Never Order](<https://leetcode.com/problems/customers-who-never-order/>) | 🟢 Fácil | `LEFT JOIN`, `IS NULL` |
| 5 | [196\. Delete Duplicate Emails](<https://leetcode.com/problems/delete-duplicate-emails/>) | 🟢 Fácil → 🟡 Intermediário | `DELETE`, subquery, duplicados |

> ⚠️ A dificuldade apresentada aqui é uma classificação de estudo, não uma nota de qualidade dos exercícios.

---

 # 🟢 1. Combine Two Tables

 ### Conceitos

 - `SELECT`
- `LEFT JOIN`
- Relacionamento entre tabelas

 ### Ideia do problema

 Temos duas tabelas, por exemplo:

```
Person
+----------+----------+
| personId | lastName |
+----------+----------+

Address
+-----------+----------+
| addressId | personId |
+-----------+----------+
```

 Precisamos combinar as informações das duas tabelas.

 ### O que estudar

 Antes de tentar resolver, procure entender:

```
SELECT ...
FROM Person
LEFT JOIN Address
    ON ...
```

 O ponto principal deste exercício é entender **como duas tabelas podem ser relacionadas através de uma chave**.

---

 # 🟢 2. Employees Earning More Than Their Managers

 ### Conceitos

 - `JOIN`
- Comparação entre valores
- Relacionamento de uma tabela com ela mesma

 ### Ideia do problema

 Temos funcionários e seus respectivos gerentes.

 Queremos encontrar funcionários cujo salário seja maior que o salário do próprio gerente.

 Um conceito importante aqui é o **self join**.

 A ideia é relacionar a tabela `Employee` com ela mesma:

```
Employee
   │
   ├── funcionário
   │
   └── gerente
```

 ### O que estudar

 Entenda primeiro:

```
FROM Employee e
JOIN Employee m
    ON ...
```

 Aqui usamos aliases diferentes para representar duas funções diferentes da mesma tabela.

---

 # 🟢 3. Duplicate Emails

 ### Conceitos

 - `GROUP BY`
- `COUNT`
- `HAVING`

 ### Ideia do problema

 Precisamos descobrir quais e-mails aparecem mais de uma vez.

 Imagine:

```
id | email
---+----------------
1  | a@email.com
2  | b@email.com
3  | a@email.com
4  | c@email.com
```

 O resultado deve identificar:

```
a@email.com
```

 porque ele aparece duas vezes.

 ### Conceito principal

 Agrupamos os registros:

```
GROUP BY email
```

 Depois contamos:

```
COUNT(*)
```

 E filtramos os grupos:

```
HAVING COUNT(*) > 1
```

 ### Padrão importante

```
SELECT email
FROM Person
GROUP BY email
HAVING COUNT(*) > 1;
```

 Esse é um padrão muito importante para reconhecer **duplicidades em SQL**.

---

 # 🟢 4. Customers Who Never Order

 ### Conceitos

 - `LEFT JOIN`
- `NULL`
- `IS NULL`

 ### Ideia do problema

 Temos clientes e pedidos.

 Queremos encontrar clientes que **nunca fizeram um pedido**.

 Imagine:

```
Customers
+----+--------+
| id | name   |
+----+--------+
| 1  | João   |
| 2  | Maria  |
| 3  | Pedro  |
+----+--------+

Orders
+----+------------+
| id | customerId |
+----+------------+
| 1  | 1          |
| 2  | 1          |
+----+------------+
```

 João possui pedidos.

 Maria e Pedro não.

 O `LEFT JOIN` permite manter todos os clientes, mesmo quando não existe um pedido correspondente.

 Depois podemos verificar:

```
WHERE Orders.id IS NULL
```

 ### Conceito para guardar

 Quando você precisar descobrir:

 > "Quem não possui relacionamento?"

 pense em:

```
LEFT JOIN
+
IS NULL
```

---

 # 🟢 5. Delete Duplicate Emails

 ### Conceitos

 - `DELETE`
- Duplicidade
- Subqueries
- `GROUP BY`
- `ROW_NUMBER()`
- Funções de janela

 Este exercício é um pouco mais interessante porque agora não queremos apenas **encontrar** duplicados.

 Precisamos **remover** duplicados.

 Imagine:

```
id | email
---+----------------
1  | a@email.com
2  | b@email.com
3  | a@email.com
4  | c@email.com
```

 Queremos manter:

```
1 | a@email.com
2 | b@email.com
4 | c@email.com
```

 E remover:

```
3 | a@email.com
```

---

 ## Uma solução utilizando `ROW_NUMBER()`

```
DELETE FROM Person
WHERE id IN (
    SELECT id
    FROM (
        SELECT
            id,
            ROW_NUMBER() OVER (
                PARTITION BY email
                ORDER BY id
            ) AS rn
        FROM Person
    ) AS duplicates
    WHERE rn > 1
);
```

 ### Como funciona?

 Primeiro:

```
ROW_NUMBER() OVER (
    PARTITION BY email
    ORDER BY id
)
```

 numera os registros dentro de cada grupo de e-mail.

 Por exemplo:

```
id | email        | rn
---+--------------+---
1  | a@email.com  | 1
3  | a@email.com  | 2
2  | b@email.com  | 1
4  | c@email.com  | 1
```

 Depois:

```
WHERE rn > 1
```

 seleciona somente os registros que não são o primeiro de cada grupo.

 Neste caso:

```
id = 3
```

 Finalmente:

```
DELETE FROM Person
WHERE id IN (...);
```

 remove esses registros.

---

 # 🧠 O que estou aprendendo

 Durante os exercícios, estou tentando não decorar apenas consultas.

 Estou tentando identificar **padrões de problemas**.

 | Problema | Padrão que devo reconhecer |
| --- | --- |
| Encontrar duplicados | `GROUP BY + HAVING` |
| Relacionar tabelas | `JOIN` |
| Encontrar quem não possui relacionamento | `LEFT JOIN + IS NULL` |
| Comparar registros da mesma tabela | `SELF JOIN` |
| Numerar registros | `ROW_NUMBER()` |
| Encontrar duplicados para remover | `ROW_NUMBER() + DELETE` |

---

 # 📝 Como estudar cada exercício

 Para cada problema, recomendo seguir este processo:

 ### 1\. Ler o problema

 Não tente escrever SQL imediatamente.

 Primeiro descubra:

 - Quais são as tabelas?
- Quais são as colunas?
- Qual é o resultado esperado?
- Existe relacionamento entre tabelas?
- Existem duplicados?
- Precisamos filtrar grupos?

 ### 2\. Tentar sozinho

 Antes de procurar uma solução, tente escrever sua própria consulta.

 Mesmo que esteja errada.

 O erro também faz parte do aprendizado.

 ### 3\. Identificar o padrão

 Pergunte:

```
Preciso de JOIN?
Preciso de GROUP BY?
Preciso de HAVING?
Preciso de uma subquery?
Preciso de uma função de janela?
```

 ### 4\. Comparar soluções

 Depois de resolver, procure outras maneiras de solucionar o mesmo problema.

 Por exemplo:

```
Solução com JOIN
Solução com subquery
Solução com EXISTS
Solução com função de janela
```

 Isso ajuda a entender SQL de maneira mais profunda.

---

 # 📚 Conceitos que pretendo estudar

 ## Básico

 - `SELECT`
- `FROM`
- `WHERE`
- `ORDER BY`
- `LIMIT`
- `DISTINCT`
- `AND`
- `OR`
- `IN`
- `BETWEEN`
- `LIKE`
- `IS NULL`

 ## Agrupamento

 - `GROUP BY`
- `HAVING`
- `COUNT`
- `SUM`
- `AVG`
- `MIN`
- `MAX`

 ## Relacionamentos

 - `INNER JOIN`
- `LEFT JOIN`
- `RIGHT JOIN`
- `FULL JOIN`
- Self Join

 ## Subqueries

 - Subquery no `WHERE`
- Subquery no `FROM`
- Subquery correlacionada
- `EXISTS`
- `NOT EXISTS`

 ## Funções de janela

 - `ROW_NUMBER()`
- `RANK()`
- `DENSE_RANK()`
- `LAG()`
- `LEAD()`

 ## Outros conceitos

 - `CASE`
- `UNION`
- `UNION ALL`
- `DELETE`
- `UPDATE`
- `INSERT`
- CTE (`WITH`)
- Índices
- Chaves primárias
- Chaves estrangeiras
- Normalização
- Performance de consultas

---

 # 🤝 Contribuições

 Este projeto também pode ser utilizado por outros estudantes.

 Se você encontrar:

 - uma solução diferente;
- uma explicação mais simples;
- um erro;
- uma melhoria;
- uma abordagem mais eficiente;

 sinta-se à vontade para contribuir.

 A ideia é construir um material de estudo colaborativo.

---

 # 🌱 Progresso

 - [x] Exercício 175 — Combine Two Tables
- [x] Exercício 181 — Employees Earning More Than Their Managers
- [x] Exercício 182 — Duplicate Emails
- [x] Exercício 183 — Customers Who Never Order
- [x] Exercício 196 — Delete Duplicate Emails
- [ ] Próximos exercícios...

---

 ## 💭 Filosofia do projeto

 > **Não quero apenas aprender a resposta. Quero aprender a pensar para chegar à resposta.**

 Se este material ajudar outra pessoa a entender um conceito que antes parecia difícil, então o estudo já valeu a pena.

 **Bons estudos! 🚀**