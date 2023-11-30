---
title: "dbt の persist_docs を source にも反映させたい！"
emoji: "📌"
type: "tech"
topics: []
published: false
---

[dbt Advent Calendar 2023](https://qiita.com/advent-calendar/2023/dbt) の 4 日目の記事です。

dbt Docs の description を DWH にも反映させたいとき、`dbt_project.yml` などの設定ファイルに `persist_docs` プロパティを仕込むと、これを実現することができます。

```yml:dbt_project.yml
models:
  <resource-path>:
    +persist_docs:
      relation: true
      columns: true
```

```yml:employee_salary.yml
version: 2

models:
  - name: employee_salary
    description: "従業員テーブル"
    columns:
      - name: emp_name
        description: "名前"

      - name: boss_emp_name
        description: "上司"

      - name: salary_amt
        description: "給与"

      - name: subordinates
        description: "部下"
```

![](https://storage.googleapis.com/zenn-user-upload/05d06de42afc-20231124.png)

ですが、この `persist_docs` プロパティは、記事公開時点の 2023 年 12 月 4 日では、残念ながら source に対応していません。

```sql
create or replace table staging.raw_employee_salary as
select *
from unnest (
    array<struct<
        emp_name string,
        boss_emp_name string,
        salary_amt int64
    >> [
        ('Albert', null, 1000),
        ('Bert', 'Albert', 900),
        ('Chuck', 'Albert', 900),
        ('Donna', 'Chuck', 800),
        ('Eddie', 'Chuck', 700),
        ('Fred', 'Chuck', 600)
    ]
)
```

[GitHub Actions の課金について](https://docs.github.com/ja/billing/managing-billing-for-github-actions/about-billing-for-github-actions)
