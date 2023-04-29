---
jupyter:
  colab:
    name: Welcome to DataCamp Workspaces.ipynb
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
  language_info:
    codemirror_mode:
      name: ipython
      version: 3
    file_extension: .py
    mimetype: text/x-python
    name: python
    nbconvert_exporter: python
    pygments_lexer: ipython3
    version: 3.8.10
  nbformat: 4
  nbformat_minor: 5
---

::: {#69c27d48-0102-45ae-b60e-54804f29d194 .cell .markdown}
# Dog salon Promotion\'s week from 6/16/19 to 6/22/19

**Challenge :** Help the dog salon owner to answer follow these
questions

1.  To view all the data, we would like to join three tables together
    using \".pet_id\" as the primary and foreign key
2.  What size of dogs are visited more than 10 times?
3.  How many pet owners come to the salon to receive services?
4.  Which dog owner is the most frequent visitor to the shop?
5.  What is the most busy day?
6.  Which dog and its owner are the most frequent visitors to the salon
    for services?
7.  Who would come for haircut service or get service on June 17?
8.  The salon have a promotion to give a gift to those pets who have had
    their nails done at the salon
9.  The town where the dog salon is located had a street fair on June 17
    and 18. The business owner wants to know how many transactions took
    place on those days
10. What size dogs are there for service? and how many of each size?
:::

::: {#1251557a-b4a3-45d6-8710-01fbfd45ac55 .cell .markdown}
transactions *transaction_id (integer) date (date) pet_id (integer)
service (string)* owners *owner_id (integer) pet_id (integer) size
(string), size of pet* owners_2 *owner_id (integer) pet_id (integer)
size (string), size of pet* visits *pet_id (integer) visits_count
(integer)*
:::

::: {#41c6e89f-185a-44a3-bff2-ddd08fae94cb .cell .markdown}
**1. To view all the data, we would like to join three tables together
using \".pet_id\" as the primary and foreign key.**
:::

::: {#46e29f43-80f8-4f4d-89e4-56eb375c5a6b .cell .code customType="sql" dataFrameVariableName="df" executionTime="171" lastSuccessfullyExecutedCode="SELECT owner_id, tr.pet_id, transaction_id, visits_count 
FROM owners AS own
    LEFT OUTER JOIN transactions AS tr
    ON own.pet_id = tr.pet_id
    LEFT OUTER JOIN visits AS vi
    ON own.pet_id = vi.pet_id
WHERE transaction_id IS NOT NULL
ORDER BY transaction_id;" sqlCellMode="dataFrame" sqlSource="{\"type\":\"dataframesAndCsvs\"}"}
``` python
SELECT owner_id, tr.pet_id, transaction_id, visits_count 
FROM owners AS own
    LEFT OUTER JOIN transactions AS tr
    ON own.pet_id = tr.pet_id
    LEFT OUTER JOIN visits AS vi
    ON own.pet_id = vi.pet_id
WHERE transaction_id IS NOT NULL
ORDER BY transaction_id;
```

::: {.output .error ename="Error" evalue="Catalog Error: Table with name owners does not exist!
Did you mean \"temp.information_schema.tables\"?"}
:::
:::

::: {#b32cf9f4-6d4f-45a2-bf24-342bf6e71654 .cell .markdown}
**2.What size of dogs are visited more than 10 times?**

**ANSWER :** There were only four dogs that visited the salon more than
10 times, consisting of three large dogs and one small dog. The dog with
the highest number of visits was pet number 23, a large-sized dog that
visited the salon 15 times.
:::

::: {#12b573a4-9612-4141-a685-ba9823afbc0e .cell .code execution_count="15" customType="sql" dataFrameVariableName="df2" executionCancelledAt="1682731848910" sqlCellMode="dataFrame" sqlSource="{\"type\":\"dataframesAndCsvs\"}"}
``` python
SELECT ow.pet_id, size, visits_count AS num_visits
FROM owners.csv AS ow JOIN visits.csv AS v
      ON ow.pet_id = v.pet_id
WHERE visits_count >= 10
ORDER BY num_visits DESC
LIMIT 10;
```
:::

::: {#a07eee08-b5a3-4e8b-bcdf-88588d315a3f .cell .markdown}
**3.How many pet owners come to the salon to receive services?** We have
to different teams collected the pet\'s owner informations Let\'s begin
by looking at all pet\'s owner and what service did they have by combine
two tables by using UNION

**ANSWER :** 12 pet owners visited the salon during promotion week!
:::

::: {#3e5b6764-72f8-4c6b-ac38-0eacaad11cb5 .cell .code execution_count="12" chartConfig="{\"bar\":{\"hasRoundedCorners\":true,\"stacked\":false},\"type\":\"bar\",\"version\":\"v1\"}" customType="sql" dataFrameVariableName="df3" executionTime="67" lastSuccessfullyExecutedCode="WITH total_owners AS
(SELECT * FROM owners.csv
UNION
SELECT * FROM owners_2.csv
ORDER BY owner_id)

SELECT COUNT(DISTINCT owner_id)
FROM total_owners;" sqlCellMode="dataFrame" sqlSource="{\"type\":\"dataframesAndCsvs\"}" visualizeDataframe="false"}
``` python
WITH total_owners AS
(SELECT * FROM owners.csv
UNION
SELECT * FROM owners_2.csv
ORDER BY owner_id)

SELECT COUNT(DISTINCT owner_id)
FROM total_owners;
```

::: {.output .execute_result execution_count="12"}
``` json
{"table":{"data":[{"count(DISTINCT owner_id)":12,"index":0}],"schema":{"fields":[{"name":"index","type":"integer"},{"name":"count(DISTINCT owner_id)","type":"integer"}],"pandas_version":"1.4.0","primaryKey":["index"]}},"total_rows":1,"truncation_type":null}
```
:::
:::

::: {#5eb8325c-ac82-4238-b689-41e46263dcd1 .cell .markdown}
**4.Which dog owner is the most frequent visitor to the shop?**

ANSWER : The pet owner no 13 visited the salon 17 times!
:::

::: {#9fd29727-4dea-444d-8f03-d4cb8022f3f8 .cell .code execution_count="13" customType="sql" dataFrameVariableName="df4" executionTime="141" lastSuccessfullyExecutedCode="WITH new_owner_id AS
 (
    SELECT * FROM owners.csv
    UNION
    SELECT * FROM owners_2.csv
 )
 
SELECT owner_id, SUM(visits_count) AS num_visits
FROM new_owner_id AS own
      JOIN visits.csv AS vi 
      ON own.pet_id = vi.pet_id
GROUP BY owner_id
ORDER BY num_visits DESC;" sqlCellMode="dataFrame" sqlSource="{\"type\":\"dataframesAndCsvs\"}"}
``` python
WITH new_owner_id AS
 (
    SELECT * FROM owners.csv
    UNION
    SELECT * FROM owners_2.csv
 )
 
SELECT owner_id, SUM(visits_count) AS num_visits
FROM new_owner_id AS own
      JOIN visits.csv AS vi 
      ON own.pet_id = vi.pet_id
GROUP BY owner_id
ORDER BY num_visits DESC;
```

::: {.output .execute_result execution_count="13"}
``` json
{"table":{"data":[{"index":0,"num_visits":17,"owner_id":13},{"index":1,"num_visits":15,"owner_id":65},{"index":2,"num_visits":11,"owner_id":19},{"index":3,"num_visits":11,"owner_id":99},{"index":4,"num_visits":10,"owner_id":11},{"index":5,"num_visits":8,"owner_id":87},{"index":6,"num_visits":6,"owner_id":34},{"index":7,"num_visits":5,"owner_id":52}],"schema":{"fields":[{"name":"index","type":"integer"},{"name":"owner_id","type":"integer"},{"name":"num_visits","type":"number"}],"pandas_version":"1.4.0","primaryKey":["index"]}},"total_rows":8,"truncation_type":null}
```
:::
:::

::: {#58f76494-dfa0-447a-a308-5c4c1b280e45 .cell .markdown}
**5. What is the most busy day?**

**ANSWER :** The busiest day is June 18. There was 4 transactions
:::

::: {#a6235b4a-4601-42e5-80d3-a9ef3a543594 .cell .code execution_count="14" customType="sql" dataFrameVariableName="df5" executionTime="120" lastSuccessfullyExecutedCode="WITH new_owner_id AS
 (
    SELECT * FROM owners.csv
    UNION
    SELECT * FROM owners_2.csv
 )

SELECT date, COUNT(date)
FROM transactions.csv AS tr
      JOIN new_owner_id
      ON tr.pet_id = new_owner_id.pet_id
WHERE transaction_id IS NOT NULL
GROUP BY date
ORDER BY COUNT(date) DESC;
" sqlCellMode="dataFrame" sqlSource="{\"type\":\"dataframesAndCsvs\"}"}
``` python
WITH new_owner_id AS
 (
    SELECT * FROM owners.csv
    UNION
    SELECT * FROM owners_2.csv
 )

SELECT date, COUNT(date)
FROM transactions.csv AS tr
      JOIN new_owner_id
      ON tr.pet_id = new_owner_id.pet_id
WHERE transaction_id IS NOT NULL
GROUP BY date
ORDER BY COUNT(date) DESC;
```

::: {.output .execute_result execution_count="14"}
``` json
{"table":{"data":[{"count(date)":4,"date":"2019-06-18T00:00:00.000","index":0},{"count(date)":3,"date":"2019-06-22T00:00:00.000","index":1},{"count(date)":3,"date":"2019-06-17T00:00:00.000","index":2},{"count(date)":2,"date":"2019-06-20T00:00:00.000","index":3},{"count(date)":2,"date":"2019-06-21T00:00:00.000","index":4},{"count(date)":2,"date":"2019-06-16T00:00:00.000","index":5},{"count(date)":2,"date":"2019-06-19T00:00:00.000","index":6}],"schema":{"fields":[{"name":"index","type":"integer"},{"name":"date","type":"datetime"},{"name":"count(date)","type":"integer"}],"pandas_version":"1.4.0","primaryKey":["index"]}},"total_rows":7,"truncation_type":null}
```
:::
:::

::: {#33f4a1af-e023-4650-adfb-5455b3327eb7 .cell .markdown}
**6.Which dog and its owner are the most frequent visitors to the salon
for services?**

**ANSWER :** The most visited is 15 times from the owner id no. 65 with
their pet no. 23
:::

::: {#cdf08df1-1556-4d9f-b66a-934427fe0a18 .cell .code execution_count="15" customType="sql" dataFrameVariableName="df6" executionTime="72" lastSuccessfullyExecutedCode="WITH new_owners AS
(
  SELECT * FROM owners.csv
  UNION
  SELECT * FROM owners_2.csv
)

SELECT CONCAT(new_owners.owner_id, ', ', new_owners.pet_id) AS owner_pet, visits_count
FROM new_owners JOIN visits.csv AS vi
    ON new_owners.pet_id = vi.pet_id
WHERE visits_count >= 3
ORDER BY visits_count DESC, owner_pet;" sqlCellMode="dataFrame" sqlSource="{\"type\":\"dataframesAndCsvs\"}"}
``` python
WITH new_owners AS
(
  SELECT * FROM owners.csv
  UNION
  SELECT * FROM owners_2.csv
)

SELECT CONCAT(new_owners.owner_id, ', ', new_owners.pet_id) AS owner_pet, visits_count
FROM new_owners JOIN visits.csv AS vi
    ON new_owners.pet_id = vi.pet_id
WHERE visits_count >= 3
ORDER BY visits_count DESC, owner_pet;
```

::: {.output .execute_result execution_count="15"}
``` json
{"table":{"data":[{"index":0,"owner_pet":"65, 23","visits_count":15},{"index":1,"owner_pet":"13, 87","visits_count":13},{"index":2,"owner_pet":"19, 32","visits_count":11},{"index":3,"owner_pet":"99, 32","visits_count":11},{"index":4,"owner_pet":"11, 67","visits_count":10},{"index":5,"owner_pet":"87, 79","visits_count":8},{"index":6,"owner_pet":"34, 13","visits_count":6},{"index":7,"owner_pet":"52, 43","visits_count":4}],"schema":{"fields":[{"name":"index","type":"integer"},{"name":"owner_pet","type":"string"},{"name":"visits_count","type":"integer"}],"pandas_version":"1.4.0","primaryKey":["index"]}},"total_rows":8,"truncation_type":null}
```
:::
:::

::: {#42e0194d-c246-4467-a349-c4608e1a0c46 .cell .markdown}
**7. Who would come for haircut service or get service on June 17?**
:::

::: {#cbafc0c0-42a7-4a32-b389-06e15ee81e18 .cell .code execution_count="16" customType="sql" dataFrameVariableName="df8" executionTime="179" lastSuccessfullyExecutedCode="SELECT owner_id, tr.pet_id, date, service
FROM transactions.csv AS tr JOIN owners.csv AS own
      ON tr.pet_id = own.pet_id
WHERE date = '2019-06-17'
      OR
      service = 'haircut'
ORDER BY date;" sqlCellMode="dataFrame" sqlSource="{\"type\":\"dataframesAndCsvs\"}"}
``` python
SELECT owner_id, tr.pet_id, date, service
FROM transactions.csv AS tr JOIN owners.csv AS own
      ON tr.pet_id = own.pet_id
WHERE date = '2019-06-17'
      OR
      service = 'haircut'
ORDER BY date;
```

::: {.output .execute_result execution_count="16"}
``` json
{"table":{"data":[{"date":"2019-06-16T00:00:00.000","index":0,"owner_id":11,"pet_id":67,"service":"haircut"},{"date":"2019-06-17T00:00:00.000","index":1,"owner_id":13,"pet_id":86,"service":"nails"},{"date":"2019-06-17T00:00:00.000","index":2,"owner_id":34,"pet_id":13,"service":"color"},{"date":"2019-06-17T00:00:00.000","index":3,"owner_id":87,"pet_id":79,"service":"nails"},{"date":"2019-06-18T00:00:00.000","index":4,"owner_id":11,"pet_id":67,"service":"haircut"},{"date":"2019-06-18T00:00:00.000","index":5,"owner_id":87,"pet_id":79,"service":"haircut"},{"date":"2019-06-19T00:00:00.000","index":6,"owner_id":65,"pet_id":23,"service":"haircut"},{"date":"2019-06-22T00:00:00.000","index":7,"owner_id":52,"pet_id":43,"service":"haircut"}],"schema":{"fields":[{"name":"index","type":"integer"},{"name":"owner_id","type":"integer"},{"name":"pet_id","type":"integer"},{"name":"date","type":"datetime"},{"name":"service","type":"string"}],"pandas_version":"1.4.0","primaryKey":["index"]}},"total_rows":8,"truncation_type":null}
```
:::
:::

::: {#7afcf012-5295-46b7-b8bc-2933a26d1387 .cell .markdown}
**8.The salon have a promotion to give a gift to those pets who have had
their nails done at the salon.**

**ANSWER :** only 5 dogs will get the gift from get the nail service.
:::

::: {#c44a0357-99c0-4123-96d6-df1a9945417f .cell .code execution_count="17" customType="sql" dataFrameVariableName="df9" executionTime="87" lastSuccessfullyExecutedCode="SELECT pet_id, service,
CASE
  WHEN service = 'nails' THEN 'gift'
  ELSE 'no gift'
END AS get_gift
FROM transactions.csv
ORDER BY get_gift;" sqlCellMode="dataFrame" sqlSource="{\"type\":\"dataframesAndCsvs\"}"}
``` python
SELECT pet_id, service,
CASE
  WHEN service = 'nails' THEN 'gift'
  ELSE 'no gift'
END AS get_gift
FROM transactions.csv
ORDER BY get_gift;
```

::: {.output .execute_result execution_count="17"}
``` json
{"table":{"data":[{"get_gift":"gift","index":0,"pet_id":86,"service":"nails"},{"get_gift":"gift","index":1,"pet_id":79,"service":"nails"},{"get_gift":"gift","index":2,"pet_id":86,"service":"nails"},{"get_gift":"gift","index":3,"pet_id":43,"service":"nails"},{"get_gift":"gift","index":4,"pet_id":23,"service":"nails"},{"get_gift":"no gift","index":5,"pet_id":67,"service":"shampoo"},{"get_gift":"no gift","index":6,"pet_id":43,"service":"haircut"},{"get_gift":"no gift","index":7,"pet_id":32,"service":"shampoo"},{"get_gift":"no gift","index":8,"pet_id":67,"service":"haircut"},{"get_gift":"no gift","index":9,"pet_id":13,"service":"color"},{"get_gift":"no gift","index":10,"pet_id":23,"service":"haircut"},{"get_gift":"no gift","index":11,"pet_id":79,"service":"haircut"},{"get_gift":"no gift","index":12,"pet_id":13,"service":"color"},{"get_gift":"no gift","index":13,"pet_id":66,"service":"shampoo"},{"get_gift":"no gift","index":14,"pet_id":13,"service":"color"},{"get_gift":"no gift","index":15,"pet_id":67,"service":"haircut"},{"get_gift":"no gift","index":16,"pet_id":79,"service":"shampoo"}],"schema":{"fields":[{"name":"index","type":"integer"},{"name":"pet_id","type":"integer"},{"name":"service","type":"string"},{"name":"get_gift","type":"string"}],"pandas_version":"1.4.0","primaryKey":["index"]}},"total_rows":17,"truncation_type":null}
```
:::
:::

::: {#37163834-d610-40bb-9aea-20d657677454 .cell .markdown}
**9.The town where the dog salon is located had a street fair on June 17
and 18. The business owner wants to know how many transactions took
place on those days.**

**ANSWER :** On June 17 was 3 transactions and June 18 was 4
transactions.
:::

::: {#9aaa1914-ff24-411e-815f-7a405f7f8ef2 .cell .code execution_count="18" customType="sql" dataFrameVariableName="df10" executionTime="56" lastSuccessfullyExecutedCode="SELECT date, COUNT(transaction_id)
FROM transactions.csv
WHERE date BETWEEN '2019-06-17' AND '2019-06-18'
GROUP BY date;" sqlCellMode="dataFrame" sqlSource="{\"type\":\"dataframesAndCsvs\"}"}
``` python
SELECT date, COUNT(transaction_id)
FROM transactions.csv
WHERE date BETWEEN '2019-06-17' AND '2019-06-18'
GROUP BY date;
```

::: {.output .execute_result execution_count="18"}
``` json
{"table":{"data":[{"count(transaction_id)":3,"date":"2019-06-17T00:00:00.000","index":0},{"count(transaction_id)":4,"date":"2019-06-18T00:00:00.000","index":1}],"schema":{"fields":[{"name":"index","type":"integer"},{"name":"date","type":"datetime"},{"name":"count(transaction_id)","type":"integer"}],"pandas_version":"1.4.0","primaryKey":["index"]}},"total_rows":2,"truncation_type":null}
```
:::
:::

::: {#78c4551a-656b-4947-995c-a7942684678a .cell .markdown}
**10.What size dogs are there for service? and how many of each size?**

**ANSWER :** The total number of dogs that received services is 17.
There were 7 small size dogs, 4 medium size dogs, and 6 large size dogs.
:::

::: {#00ae472d-a616-4e89-89ae-a44fd9d632b7 .cell .code execution_count="19" customType="sql" dataFrameVariableName="df11" executionTime="51" lastSuccessfullyExecutedCode="WITH all_owners AS
(
  SELECT * FROM owners.csv
  UNION
  SELECT * FROM owners_2.csv
)

SELECT size, COUNT(size) AS size_count
FROM all_owners
GROUP BY size
ORDER BY size DESC;" sqlCellMode="dataFrame" sqlSource="{\"type\":\"dataframesAndCsvs\"}"}
``` python
WITH all_owners AS
(
  SELECT * FROM owners.csv
  UNION
  SELECT * FROM owners_2.csv
)

SELECT size, COUNT(size) AS size_count
FROM all_owners
GROUP BY size
ORDER BY size DESC;
```

::: {.output .execute_result execution_count="19"}
``` json
{"table":{"data":[{"index":0,"size":"small","size_count":7},{"index":1,"size":"medium","size_count":4},{"index":2,"size":"large","size_count":6}],"schema":{"fields":[{"name":"index","type":"integer"},{"name":"size","type":"string"},{"name":"size_count","type":"integer"}],"pandas_version":"1.4.0","primaryKey":["index"]}},"total_rows":3,"truncation_type":null}
```
:::
:::
