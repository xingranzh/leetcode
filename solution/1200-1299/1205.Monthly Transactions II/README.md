# [1205. 每月交易 II](https://leetcode.cn/problems/monthly-transactions-ii)

[English Version](/solution/1200-1299/1205.Monthly%20Transactions%20II/README_EN.md)

## 题目描述

<!-- 这里写题目描述 -->

<p><code>Transactions</code> 记录表</p>

<pre>
+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| id             | int     |
| country        | varchar |
| state          | enum    |
| amount         | int     |
| trans_date     | date    |
+----------------+---------+
id 是这个表的主键。
该表包含有关传入事务的信息。
状态列是类型为 [approved（已批准）、declined（已拒绝）] 的枚举。</pre>

<p><code>Chargebacks</code> 表</p>

<pre>
+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| trans_id       | int     |
| trans_date     | date    |
+----------------+---------+
退单包含有关放置在事务表中的某些事务的传入退单的基本信息。
trans_id 是 transactions 表的 id 列的外键。
每项退单都对应于之前进行的交易，即使未经批准。</pre>

<p>&nbsp;</p>

<p>编写一个 SQL&nbsp;查询，以查找每个月和每个国家/地区的信息：已批准交易的数量及其总金额、退单的数量及其总金额。</p>

<p><strong>注意：</strong>在您的查询中，只需显示给定月份和国家，忽略所有为零的行。</p>

<p>以 <strong>任意顺序</strong> 返回结果表。</p>

<p>查询结果格式如下所示。</p>

<p>&nbsp;</p>

<p><strong>示例 1:</strong></p>

<pre>
<strong>输入：</strong>
Transactions 表：
+-----+---------+----------+--------+------------+
| id  | country | state    | amount | trans_date |
+-----+---------+----------+--------+------------+
| 101 | US      | approved | 1000   | 2019-05-18 |
| 102 | US      | declined | 2000   | 2019-05-19 |
| 103 | US      | approved | 3000   | 2019-06-10 |
| 104 | US      | declined | 4000   | 2019-06-13 |
| 105 | US      | approved | 5000   | 2019-06-15 |
+-----+---------+----------+--------+------------+
Chargebacks 表：
+----------+------------+
| trans_id | trans_date |
+----------+------------+
| 102      | 2019-05-29 |
| 101      | 2019-06-30 |
| 105      | 2019-09-18 |
+----------+------------+
<strong>输出：</strong>
+---------+---------+----------------+-----------------+------------------+-------------------+
| month   | country | approved_count | approved_amount | chargeback_count | chargeback_amount |
+---------+---------+----------------+-----------------+------------------+-------------------+
| 2019-05 | US      | 1              | 1000            | 1                | 2000              |
| 2019-06 | US      | 2              | 8000            | 1                | 1000              |
| 2019-09 | US      | 0              | 0               | 1                | 5000              |
+---------+---------+----------------+-----------------+------------------+-------------------+</pre>

## 解法

<!-- 这里可写通用的实现逻辑 -->

<!-- tabs:start -->

### **SQL**

```sql
# Write your MySQL query statement below
WITH
    t AS (
        SELECT * FROM Transactions
        UNION ALL
        SELECT id, country, 'chargeback' AS state, amount, cb.trans_date
        FROM
            Chargebacks AS cb
            LEFT JOIN Transactions AS tx ON cb.trans_id = tx.id
    )
SELECT
    date_format(trans_date, '%Y-%m') AS month,
    country,
    sum(state = 'approved') AS approved_count,
    sum(if(state = 'approved', amount, 0)) AS approved_amount,
    sum(state = 'chargeback') AS chargeback_count,
    sum(if(state = 'chargeback', amount, 0)) AS chargeback_amount
FROM t
GROUP BY 1, 2
HAVING approved_amount OR chargeback_amount;
```

<!-- tabs:end -->
