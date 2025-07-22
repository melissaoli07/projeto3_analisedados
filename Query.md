Documentação Query - BigQuery

Melissa de Oliveira Pecoraro - Jornada de Dados - Projeto 03

🔍QUERY:

2.1.2🔵 Identificar e tratar valores nulos
```SQL
SELECT
  SUM(CASE WHEN user_id IS NULL THEN 1 ELSE 0 END) AS user_id_nulls,
  SUM(CASE WHEN default_flag IS NULL THEN 1 ELSE 0 END) AS default_flag_nulls,
FROM
  `laboratoria-projeto3.dados_projeto3.Default`
```

```SQL
SELECT
  SUM(CASE WHEN loan_id IS NULL THEN 1 ELSE 0 END) AS loan_id_nulls,
  SUM(CASE WHEN user_id IS NULL THEN 1 ELSE 0 END) AS user_id_nulls,
  SUM(CASE WHEN loan_type IS NULL THEN 1 ELSE 0 END) AS loan_type_nulls,
FROM
  `laboratoria-projeto3.dados_projeto3.Empréstimo`
```

```SQL
SELECT
  SUM(CASE WHEN user_id IS NULL THEN 1 ELSE 0 END) AS user_id_nulls,
  SUM(CASE WHEN more_90_days_overdue IS NULL THEN 1 ELSE 0 END) AS more_90_days_overdue_nulls,
  SUM(CASE WHEN using_lines_not_secured_personal_assets IS NULL THEN 1 ELSE 0 END) AS using_lines_not_secured_personal_assets_nulls,
  SUM(CASE WHEN number_times_delayed_payment_loan_30_59_days IS NULL THEN 1 ELSE 0 END) AS number_times_delayed_payment_loan_30_59_days_nulls,
  SUM(CASE WHEN debt_ratio IS NULL THEN 1 ELSE 0 END) AS debt_ratio_nulls,
  SUM(CASE WHEN number_times_delayed_payment_loan_60_89_days IS NULL THEN 1 ELSE 0 END) AS number_times_delayed_payment_loan_60_89_days_nulls,
FROM
  `laboratoria-projeto3.dados_projeto3.Loans_detail`
```

```SQL
SELECT
  SUM(CASE WHEN user_id IS NULL THEN 1 ELSE 0 END) AS user_id_nulls,
  SUM(CASE WHEN age IS NULL THEN 1 ELSE 0 END) AS age_nulls,
  SUM(CASE WHEN sex IS NULL THEN 1 ELSE 0 END) AS sex_nulls,
  SUM(CASE WHEN last_month_salary IS NULL THEN 1 ELSE 0 END) AS last_month_salary_nulls,
  SUM(CASE WHEN number_dependents IS NULL THEN 1 ELSE 0 END) AS number_dependents_nulls,
FROM
  `laboratoria-projeto3.dados_projeto3.User`
```

```SQL
SELECT
  SUM(CASE WHEN user_id IS NULL THEN 1 ELSE 0 END) AS user_id_nulls,
  SUM(CASE WHEN age IS NULL THEN 1 ELSE 0 END) AS age_nulls,
  SUM(CASE WHEN sex IS NULL THEN 1 ELSE 0 END) AS sex_nulls,
  SUM(CASE WHEN last_month_salary IS NULL THEN 1 ELSE 0 END) AS last_month_salary_nulls,
  SUM(CASE WHEN number_dependents IS NULL THEN 1 ELSE 0 END) AS number_dependents_nulls,
FROM
  `laboratoria-projeto3.dados_projeto3.User`
```

```SQL
WITH users_nulos AS (
  SELECT *
  FROM `laboratoria-projeto3.dados_projeto3.User`
  WHERE age IS NULL
     OR sex IS NULL
     OR last_month_salary IS NULL
     OR number_dependents IS NULL
),
joined AS (
  SELECT
    u.user_id,
    t.default_flag
  FROM users_nulos u
  LEFT JOIN `laboratoria-projeto3.dados_projeto3.Default` t
  ON u.user_id = CAST(t.user_id AS STRING)
)

SELECT
  COUNTIF(default_flag = 0) AS qtd_default_0,
  COUNTIF(default_flag = 1) AS qtd_default_1,
  COUNTIF(default_flag IS NULL) AS qtd_sem_info_default
FROM joined;

```
```SQL
CREATE OR REPLACE VIEW `laboratoria-projeto3.dados_projeto3.User_sem_nulos` AS
SELECT
  user_id,
  age,
  sex,
  COALESCE(last_month_salary, '0') AS last_month_salary,
  COALESCE(number_dependents, '0') AS number_dependents
FROM
  `laboratoria-projeto3.dados_projeto3.User`;



SELECT
  SUM(CASE WHEN user_id IS NULL THEN 1 ELSE 0 END) AS user_id_nulls,
  SUM(CASE WHEN age IS NULL THEN 1 ELSE 0 END) AS age_nulls,
  SUM(CASE WHEN sex IS NULL THEN 1 ELSE 0 END) AS sex_nulls,
  SUM(CASE WHEN last_month_salary IS NULL THEN 1 ELSE 0 END) AS last_month_salary_nulls,
  SUM(CASE WHEN number_dependents IS NULL THEN 1 ELSE 0 END) AS number_dependents_nulls,
FROM
  `laboratoria-projeto3.dados_projeto3.User_sem_nulos`
```
2.1.3 🔵 Identificar e tratar valores duplicados
```SQL
SELECT
  user_id,
  COUNT(*) AS quantidade
FROM
  `laboratoria-projeto3.dados_projeto3.Default`
GROUP BY
  user_id
HAVING
  COUNT(*) > 1
ORDER BY
  quantidade DESC;

```
```SQL
SELECT
  loan_id,
  COUNT(*) AS quantidade
FROM
  `laboratoria-projeto3.dados_projeto3.Empréstimo`
GROUP BY
  loan_id
HAVING
  COUNT(*) > 1
ORDER BY
  quantidade DESC;

```
```SQL
SELECT
  user_id,
  COUNT(*) AS quantidade
FROM
  `laboratoria-projeto3.dados_projeto3.Loans_detail`
GROUP BY
  user_id
HAVING
  COUNT(*) > 1
ORDER BY
  quantidade DESC;

```
```SQL
SELECT
  user_id,
  COUNT(*) AS quantidade
FROM
  `laboratoria-projeto3.dados_projeto3.User_sem_nulos`
GROUP BY
  user_id
HAVING
  COUNT(*) > 1
ORDER BY
  quantidade DESC;

```
2.1.4🔵 Identificar e gerenciar dados fora do escopo da análise
```SQL
SELECT
  CORR(CAST(age AS FLOAT64),
       CASE 
         WHEN LOWER(sex) = 'f' THEN 0
         WHEN LOWER(sex) = 'm' THEN 1
         ELSE NULL
       END) AS correlacao
FROM
  `laboratoria-projeto3.dados_projeto3.User_sem_nulos`;

```
```SQL
SELECT
  CORR(CAST(number_dependents AS FLOAT64), CAST(last_month_salary AS FLOAT64)) AS correlacao
FROM
  `laboratoria-projeto3.dados_projeto3.User_sem_nulos`;

```
```SQL
SELECT
  CORR(more_90_days_overdue, number_times_delayed_payment_loan_30_59_days) AS correlacao
FROM
  `laboratoria-projeto3.dados_projeto3.Loans_detail`;

```
```SQL
SELECT
  CORR(more_90_days_overdue, number_times_delayed_payment_loan_60_89_days) AS correlacao
FROM
  `laboratoria-projeto3.dados_projeto3.Loans_detail`;

```
```SQL
SELECT
  CORR(number_times_delayed_payment_loan_30_59_days, number_times_delayed_payment_loan_60_89_days) AS correlacao
FROM
  `laboratoria-projeto3.dados_projeto3.Loans_detail`;

```
```SQL
-- Não precisa de Cast porque a variavel é integer 
SELECT
  STDDEV_SAMP(CAST(more_90_days_overdue AS FLOAT64)) AS desvio_padrao
FROM
  `laboratoria-projeto3.dados_projeto3.Loans_detail`;
4.12136466842673
```
```SQL
SELECT
  STDDEV_SAMP(number_times_delayed_payment_loan_30_59_days) AS desvio_padrao
FROM
  `laboratoria-projeto3.dados_projeto3.Loans_detail`;
4.1440204382258843
```
```SQL
SELECT
  STDDEV_SAMP(number_times_delayed_payment_loan_60_89_days) AS desvio_padrao
FROM
  `laboratoria-projeto3.dados_projeto3.Loans_detail`;
4.1055147551019751
```
```SQL
CREATE OR REPLACE VIEW `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo` AS
SELECT
  user_id,
  using_lines_not_secured_personal_assets,
  number_times_delayed_payment_loan_30_59_days,
  debt_ratio
FROM
  `laboratoria-projeto3.dados_projeto3.Loans_detail`;
```
```SQL
CREATE OR REPLACE VIEW `laboratoria-projeto3.dados_projeto3.User_semSex` AS
SELECT
  user_id,
  age,
  last_month_salary,
  number_dependents
FROM
  `laboratoria-projeto3.dados_projeto3.User_sem_nulos`;
```
2.1.5 🔵 Identificar e tratar dados inconsistentes em variáveis categóricas
```SQL
CREATE OR REPLACE VIEW `laboratoria-projeto3.dados_projeto3.Empréstimo_categoricas` AS
SELECT
  loan_id,
  user_id,
  CASE
    WHEN LOWER(loan_type) = 'others' THEN 'OTHER'
    ELSE UPPER(loan_type)
  END AS loan_type
FROM
  `laboratoria-projeto3.dados_projeto3.Empréstimo`



SELECT DISTINCT loan_type
FROM `laboratoria-projeto3.dados_projeto3.Empréstimo_categoricas`
WHERE loan_type != UPPER(loan_type)
   OR LOWER(loan_type) = 'others';

```
2.1.6 🔵 Identificar e tratar dados discrepantes em variáveis numéricas
```SQL
WITH stats AS (
  SELECT
    AVG(SAFE_CAST(using_lines_not_secured_personal_assets AS FLOAT64)) AS media,
    STDDEV(SAFE_CAST(using_lines_not_secured_personal_assets AS FLOAT64)) AS desvio_padrao
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`
),
outliers AS (
  SELECT
    *,
    (SAFE_CAST(using_lines_not_secured_personal_assets AS FLOAT64) - stats.media) / stats.desvio_padrao AS z_score
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`, stats
)
SELECT *
FROM outliers
WHERE ABS(z_score) > 3

```
```SQL
WITH stats AS (
  SELECT
    AVG(SAFE_CAST(number_times_delayed_payment_loan_30_59_days AS FLOAT64)) AS media,
    STDDEV(SAFE_CAST(number_times_delayed_payment_loan_30_59_days AS FLOAT64)) AS desvio_padrao
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`
),
outliers AS (
  SELECT
    *,
    (SAFE_CAST(number_times_delayed_payment_loan_30_59_days AS FLOAT64) - stats.media) / stats.desvio_padrao AS z_score
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`, stats
)
SELECT *
FROM outliers
WHERE ABS(z_score) > 3

```
```SQL
WITH stats AS (
  SELECT
    AVG(SAFE_CAST(debt_ratio AS FLOAT64)) AS media,
    STDDEV(SAFE_CAST(debt_ratio AS FLOAT64)) AS desvio_padrao
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`
),
outliers AS (
  SELECT
    *,
    (SAFE_CAST(debt_ratio AS FLOAT64) - stats.media) / stats.desvio_padrao AS z_score
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`, stats
)
SELECT *
FROM outliers
WHERE ABS(z_score) > 3

```
```SQL
WITH stats AS (
  SELECT
    AVG(SAFE_CAST(age AS FLOAT64)) AS media,
    STDDEV(SAFE_CAST(age AS FLOAT64)) AS desvio_padrao
  FROM
    `laboratoria-projeto3.dados_projeto3.User_semSex`
),
outliers AS (
  SELECT
    *,
    (SAFE_CAST(age AS FLOAT64) - stats.media) / stats.desvio_padrao AS z_score
  FROM
    `laboratoria-projeto3.dados_projeto3.User_semSex`, stats
)
SELECT *
FROM outliers
WHERE ABS(z_score) > 3

```
```SQL
WITH stats AS (
  SELECT
    AVG(SAFE_CAST(last_month_salary AS FLOAT64)) AS media,
    STDDEV(SAFE_CAST(last_month_salary AS FLOAT64)) AS desvio_padrao
  FROM
    `laboratoria-projeto3.dados_projeto3.User_semSex`
),
outliers AS (
  SELECT
    *,
    (SAFE_CAST(last_month_salary AS FLOAT64) - stats.media) / stats.desvio_padrao AS z_score
  FROM
    `laboratoria-projeto3.dados_projeto3.User_semSex`, stats
)
SELECT *
FROM outliers
WHERE ABS(z_score) > 3

```
```SQL
WITH quartis AS (
  SELECT
    APPROX_QUANTILES(SAFE_CAST(using_lines_not_secured_personal_assets AS FLOAT64), 4) AS q
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`
),
valores_com_iqr AS (
  SELECT
    *,
    q[OFFSET(1)] AS Q1,
    q[OFFSET(3)] AS Q3,
    q[OFFSET(3)] - q[OFFSET(1)] AS IQR
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`,
    quartis
)
SELECT *
FROM valores_com_iqr
WHERE SAFE_CAST(using_lines_not_secured_personal_assets AS FLOAT64) < Q1 - 1.5 * IQR OR SAFE_CAST(using_lines_not_secured_personal_assets AS FLOAT64) > Q3 + 1.5 * IQR

```
```SQL
WITH quartis AS (
  SELECT
    APPROX_QUANTILES(valor, 4) AS q
  FROM
    `projeto.dataset.tabela`
),
valores_com_iqr AS (
  SELECT
    *,
    q[OFFSET(1)] AS Q1,
    q[OFFSET(3)] AS Q3,
    q[OFFSET(3)] - q[OFFSET(1)] AS IQR
  FROM
    `projeto.dataset.tabela`, quartis
)
SELECT
  *,
  -- Cálculo dos limites
  Q1 - 1.5 * IQR AS limite_inferior,
  Q3 + 1.5 * IQR AS limite_superior,
  -- Coluna booleana indicando se é outlier
  CASE
    WHEN valor < Q1 - 1.5 * IQR OR valor > Q3 + 1.5 * IQR THEN TRUE
    ELSE FALSE
  END AS is_outlier
FROM valores_com_iqr

```
```SQL
WITH quartis AS (
  SELECT
    APPROX_QUANTILES(SAFE_CAST(using_lines_not_secured_personal_assets AS FLOAT64), 4) AS q
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`
),
valores_com_iqr AS (
  SELECT
    *,
    q[OFFSET(1)] AS Q1,
    q[OFFSET(3)] AS Q3,
    q[OFFSET(3)] - q[OFFSET(1)] AS IQR
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`, quartis
),
marcado AS (
  SELECT
    *,
    Q1 - 1.5 * IQR AS limite_inferior,
    Q3 + 1.5 * IQR AS limite_superior,
    CASE
      WHEN SAFE_CAST(using_lines_not_secured_personal_assets AS FLOAT64) < Q1 - 1.5 * IQR
        OR SAFE_CAST(using_lines_not_secured_personal_assets AS FLOAT64) > Q3 + 1.5 * IQR
      THEN TRUE
      ELSE FALSE
    END AS is_outlier
  FROM valores_com_iqr
)
SELECT *
FROM marcado
WHERE is_outlier = TRUE

```
```SQL
WITH quartis AS (
  SELECT
    APPROX_QUANTILES(SAFE_CAST(number_times_delayed_payment_loan_30_59_days AS FLOAT64), 4) AS q
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`
),
valores_com_iqr AS (
  SELECT
    *,
    q[OFFSET(1)] AS Q1,
    q[OFFSET(3)] AS Q3,
    q[OFFSET(3)] - q[OFFSET(1)] AS IQR
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`,
    quartis
)
SELECT *
FROM valores_com_iqr
WHERE SAFE_CAST(number_times_delayed_payment_loan_30_59_days AS FLOAT64) < Q1 - 1.5 * IQR OR SAFE_CAST(number_times_delayed_payment_loan_30_59_days AS FLOAT64) > Q3 + 1.5 * IQR

```
```SQL
WITH quartis AS (
  SELECT
    APPROX_QUANTILES(SAFE_CAST(number_times_delayed_payment_loan_30_59_days AS FLOAT64), 4) AS q
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`
),
valores_com_iqr AS (
  SELECT
    *,
    q[OFFSET(1)] AS Q1,
    q[OFFSET(3)] AS Q3,
    q[OFFSET(3)] - q[OFFSET(1)] AS IQR
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`, quartis
),
marcado AS (
  SELECT
    *,
    Q1 - 1.5 * IQR AS limite_inferior,
    Q3 + 1.5 * IQR AS limite_superior,
    CASE
      WHEN SAFE_CAST(number_times_delayed_payment_loan_30_59_days AS FLOAT64) < Q1 - 1.5 * IQR
        OR SAFE_CAST(number_times_delayed_payment_loan_30_59_days AS FLOAT64) > Q3 + 1.5 * IQR
      THEN TRUE
      ELSE FALSE
    END AS is_outlier
  FROM valores_com_iqr
)
SELECT *
FROM marcado
WHERE is_outlier = TRUE

```
```SQL
WITH quartis AS (
  SELECT
    APPROX_QUANTILES(SAFE_CAST(debt_ratio AS FLOAT64), 4) AS q
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`
),
valores_com_iqr AS (
  SELECT
    *,
    q[OFFSET(1)] AS Q1,
    q[OFFSET(3)] AS Q3,
    q[OFFSET(3)] - q[OFFSET(1)] AS IQR
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`,
    quartis
)
SELECT *
FROM valores_com_iqr
WHERE SAFE_CAST(debt_ratio AS FLOAT64) < Q1 - 1.5 * IQR OR SAFE_CAST(debt_ratio AS FLOAT64) > Q3 + 1.5 * IQR

```
```SQL
WITH base AS (
  SELECT
    SAFE_CAST(REGEXP_REPLACE(using_lines_not_secured_personal_assets, r'\.', '') AS FLOAT64) AS valor
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`
  WHERE SAFE_CAST(REGEXP_REPLACE(using_lines_not_secured_personal_assets, r'\.', '') AS FLOAT64) IS NOT NULL
),

-- Estatísticas para Z-score
stats AS (
  SELECT
    AVG(valor) AS media,
    STDDEV(valor) AS desvio_padrao
  FROM base
),

-- Estatísticas para IQR
quartis AS (
  SELECT
    APPROX_QUANTILES(valor, 4) AS q
  FROM base
),

-- Junta tudo
completo AS (
  SELECT
    b.valor,
    s.media,
    s.desvio_padrao,
    q[OFFSET(1)] AS Q1,
    q[OFFSET(3)] AS Q3,
    q[OFFSET(3)] - q[OFFSET(1)] AS IQR,
    
    -- Z-score
    (b.valor - s.media) / s.desvio_padrao AS z_score,

    -- Outlier pelo Z-score
    CASE 
      WHEN ABS((b.valor - s.media) / s.desvio_padrao) > 3 THEN TRUE
      ELSE FALSE
    END AS outlier_zscore,

    -- Outlier pelo IQR
    CASE 
      WHEN b.valor < q[OFFSET(1)] - 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
        OR b.valor > q[OFFSET(3)] + 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
      THEN TRUE
      ELSE FALSE
    END AS outlier_iqr
  FROM base b, stats s, quartis
)

-- Resultado final com marcação geral
SELECT
  *,
  CASE
    WHEN outlier_zscore = TRUE OR outlier_iqr = TRUE THEN 'OUTLIER'
    ELSE 'NORMAL'
  END AS classificado_como
FROM completo
ORDER BY valor

```
```SQL
WITH base AS (
  SELECT
    SAFE_CAST(using_lines_not_secured_personal_assets AS FLOAT64) AS valor
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`
  WHERE SAFE_CAST(using_lines_not_secured_personal_assets AS FLOAT64) IS NOT NULL
),


-- Estatísticas para Z-score
stats AS (
  SELECT
    AVG(valor) AS media,
    STDDEV(valor) AS desvio_padrao
  FROM base
),

-- Estatísticas para IQR
quartis AS (
  SELECT
    APPROX_QUANTILES(valor, 4) AS q
  FROM base
),

-- Junta tudo
completo AS (
  SELECT
    b.valor,
    s.media,
    s.desvio_padrao,
    q[OFFSET(1)] AS Q1,
    q[OFFSET(3)] AS Q3,
    q[OFFSET(3)] - q[OFFSET(1)] AS IQR,
    
    -- Z-score
    (b.valor - s.media) / s.desvio_padrao AS z_score,

    -- Outlier pelo Z-score
    CASE 
      WHEN ABS((b.valor - s.media) / s.desvio_padrao) > 3 THEN TRUE
      ELSE FALSE
    END AS outlier_zscore,

    -- Outlier pelo IQR
    CASE 
      WHEN b.valor < q[OFFSET(1)] - 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
        OR b.valor > q[OFFSET(3)] + 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
      THEN TRUE
      ELSE FALSE
    END AS outlier_iqr
  FROM base b, stats s, quartis
)

-- Contagem dos outliers
SELECT
  COUNTIF(outlier_zscore) AS outliers_zscore,
  COUNTIF(outlier_iqr) AS outliers_iqr,
  COUNTIF(outlier_zscore AND outlier_iqr) AS outliers_ambos,
  COUNT(*) AS total
FROM completo

```
```SQL
WITH base AS ( 
  SELECT
    SAFE_CAST(REGEXP_REPLACE(using_lines_not_secured_personal_assets, r'\.', '') AS FLOAT64) AS valor
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`
  WHERE SAFE_CAST(REGEXP_REPLACE(using_lines_not_secured_personal_assets, r'\.', '') AS FLOAT64) IS NOT NULL
),


-- Estatísticas para Z-score
stats AS (
  SELECT
    AVG(valor) AS media,
    STDDEV(valor) AS desvio_padrao
  FROM base
),

-- Estatísticas para IQR
quartis AS (
  SELECT
    APPROX_QUANTILES(valor, 4) AS q
  FROM base
),

-- Junta tudo
completo AS (
  SELECT
    b.valor,
    s.media,
    s.desvio_padrao,
    q[OFFSET(1)] AS Q1,
    q[OFFSET(3)] AS Q3,
    q[OFFSET(3)] - q[OFFSET(1)] AS IQR,
    
    -- Z-score
    (b.valor - s.media) / s.desvio_padrao AS z_score,

    -- Outlier pelo Z-score
    CASE 
      WHEN ABS((b.valor - s.media) / s.desvio_padrao) > 3 THEN TRUE
      ELSE FALSE
    END AS outlier_zscore,

    -- Outlier pelo IQR
    CASE 
      WHEN b.valor < q[OFFSET(1)] - 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
        OR b.valor > q[OFFSET(3)] + 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
      THEN TRUE
      ELSE FALSE
    END AS outlier_iqr
  FROM base b, stats s, quartis
)

-- Contagem dos outliers
SELECT
  COUNTIF(outlier_zscore) AS outliers_zscore,
  COUNTIF(outlier_iqr) AS outliers_iqr,
  COUNTIF(outlier_zscore AND outlier_iqr) AS outliers_ambos,
  COUNT(*) AS total
FROM completo

```
```SQL
WITH base AS (
  SELECT
    SAFE_CAST(number_times_delayed_payment_loan_30_59_days AS FLOAT64) AS valor
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`
  WHERE SAFE_CAST(number_times_delayed_payment_loan_30_59_days AS FLOAT64) IS NOT NULL
),


-- Estatísticas para Z-score
stats AS (
  SELECT
    AVG(valor) AS media,
    STDDEV(valor) AS desvio_padrao
  FROM base
),

-- Estatísticas para IQR
quartis AS (
  SELECT
    APPROX_QUANTILES(valor, 4) AS q
  FROM base
),

-- Junta tudo
completo AS (
  SELECT
    b.valor,
    s.media,
    s.desvio_padrao,
    q[OFFSET(1)] AS Q1,
    q[OFFSET(3)] AS Q3,
    q[OFFSET(3)] - q[OFFSET(1)] AS IQR,
    
    -- Z-score
    (b.valor - s.media) / s.desvio_padrao AS z_score,

    -- Outlier pelo Z-score
    CASE 
      WHEN ABS((b.valor - s.media) / s.desvio_padrao) > 3 THEN TRUE
      ELSE FALSE
    END AS outlier_zscore,

    -- Outlier pelo IQR
    CASE 
      WHEN b.valor < q[OFFSET(1)] - 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
        OR b.valor > q[OFFSET(3)] + 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
      THEN TRUE
      ELSE FALSE
    END AS outlier_iqr
  FROM base b, stats s, quartis
)

-- Contagem dos outliers
SELECT
  COUNTIF(outlier_zscore) AS outliers_zscore,
  COUNTIF(outlier_iqr) AS outliers_iqr,
  COUNTIF(outlier_zscore AND outlier_iqr) AS outliers_ambos,
  COUNT(*) AS total
FROM completo

```
```SQL
WITH base AS ( 
  SELECT
    SAFE_CAST(REGEXP_REPLACE(debt_ratio, r'\.', '') AS FLOAT64) AS valor
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`
  WHERE SAFE_CAST(REGEXP_REPLACE(debt_ratio, r'\.', '') AS FLOAT64) IS NOT NULL
),


-- Estatísticas para Z-score
stats AS (
  SELECT
    AVG(valor) AS media,
    STDDEV(valor) AS desvio_padrao
  FROM base
),

-- Estatísticas para IQR
quartis AS (
  SELECT
    APPROX_QUANTILES(valor, 4) AS q
  FROM base
),

-- Junta tudo
completo AS (
  SELECT
    b.valor,
    s.media,
    s.desvio_padrao,
    q[OFFSET(1)] AS Q1,
    q[OFFSET(3)] AS Q3,
    q[OFFSET(3)] - q[OFFSET(1)] AS IQR,
    
    -- Z-score
    (b.valor - s.media) / s.desvio_padrao AS z_score,

    -- Outlier pelo Z-score
    CASE 
      WHEN ABS((b.valor - s.media) / s.desvio_padrao) > 3 THEN TRUE
      ELSE FALSE
    END AS outlier_zscore,

    -- Outlier pelo IQR
    CASE 
      WHEN b.valor < q[OFFSET(1)] - 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
        OR b.valor > q[OFFSET(3)] + 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
      THEN TRUE
      ELSE FALSE
    END AS outlier_iqr
  FROM base b, stats s, quartis
)

-- Contagem dos outliers
SELECT
  COUNTIF(outlier_zscore) AS outliers_zscore,
  COUNTIF(outlier_iqr) AS outliers_iqr,
  COUNTIF(outlier_zscore AND outlier_iqr) AS outliers_ambos,
  COUNT(*) AS total
FROM completo

```
```SQL
WITH base AS (
  SELECT
    SAFE_CAST(REGEXP_REPLACE(using_lines_not_secured_personal_assets, r'\.', '') AS FLOAT64) AS valor
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`
  WHERE SAFE_CAST(REGEXP_REPLACE(using_lines_not_secured_personal_assets, r'\.', '') AS FLOAT64) IS NOT NULL
),

-- Estatísticas para Z-score
stats AS (
  SELECT
    AVG(valor) AS media,
    STDDEV(valor) AS desvio_padrao
  FROM base
),

-- Estatísticas para IQR
quartis AS (
  SELECT
    APPROX_QUANTILES(valor, 4) AS q
  FROM base
),

-- Junta tudo
completo AS (
  SELECT
    b.valor,
    s.media,
    s.desvio_padrao,
    q[OFFSET(1)] AS Q1,
    q[OFFSET(3)] AS Q3,
    q[OFFSET(3)] - q[OFFSET(1)] AS IQR,
    
    -- Z-score
    (b.valor - s.media) / s.desvio_padrao AS z_score,

    -- Outlier pelo Z-score
    CASE 
      WHEN ABS((b.valor - s.media) / s.desvio_padrao) > 3 THEN TRUE
      ELSE FALSE
    END AS outlier_zscore,

    -- Outlier pelo IQR
    CASE 
      WHEN b.valor < q[OFFSET(1)] - 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
        OR b.valor > q[OFFSET(3)] + 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
      THEN TRUE
      ELSE FALSE
    END AS outlier_iqr
  FROM base b, stats s, quartis
)

-- Resultado final com classificação refinada
SELECT
  *,
  CASE
    WHEN outlier_zscore AND outlier_iqr THEN 'FORTE OUTLIER'
    WHEN outlier_zscore OR outlier_iqr THEN 'MODERADO'
    ELSE 'NORMAL'
  END AS categoria_outlier
FROM completo
ORDER BY valor



```
```SQL
WITH base AS (
  SELECT
    SAFE_CAST(REGEXP_REPLACE(debt_ratio, r'\.', '') AS FLOAT64) AS valor
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`
  WHERE SAFE_CAST(REGEXP_REPLACE(debt_ratio, r'\.', '') AS FLOAT64) IS NOT NULL
),

-- Estatísticas para Z-score
stats AS (
  SELECT
    AVG(valor) AS media,
    STDDEV(valor) AS desvio_padrao
  FROM base
),

-- Estatísticas para IQR
quartis AS (
  SELECT
    APPROX_QUANTILES(valor, 4) AS q
  FROM base
),

-- Junta tudo
completo AS (
  SELECT
    b.valor,
    s.media,
    s.desvio_padrao,
    q[OFFSET(1)] AS Q1,
    q[OFFSET(3)] AS Q3,
    q[OFFSET(3)] - q[OFFSET(1)] AS IQR,
    
    -- Z-score
    (b.valor - s.media) / s.desvio_padrao AS z_score,

    -- Outlier pelo Z-score
    CASE 
      WHEN ABS((b.valor - s.media) / s.desvio_padrao) > 3 THEN TRUE
      ELSE FALSE
    END AS outlier_zscore,

    -- Outlier pelo IQR
    CASE 
      WHEN b.valor < q[OFFSET(1)] - 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
        OR b.valor > q[OFFSET(3)] + 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
      THEN TRUE
      ELSE FALSE
    END AS outlier_iqr
  FROM base b, stats s, quartis
)

-- Resultado final com classificação refinada
SELECT
  *,
  CASE
    WHEN outlier_zscore AND outlier_iqr THEN 'FORTE OUTLIER'
    WHEN outlier_zscore OR outlier_iqr THEN 'MODERADO'
    ELSE 'NORMAL'
  END AS categoria_outlier
FROM completo
ORDER BY valor



```
```SQL
WITH base AS (
  SELECT
    SAFE_CAST(REGEXP_REPLACE(using_lines_not_secured_personal_assets, r'\.', '') AS FLOAT64) AS valor
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`
  WHERE SAFE_CAST(REGEXP_REPLACE(using_lines_not_secured_personal_assets, r'\.', '') AS FLOAT64) IS NOT NULL
),

stats AS (
  SELECT
    AVG(valor) AS media,
    STDDEV(valor) AS desvio_padrao
  FROM base
),

quartis AS (
  SELECT
    APPROX_QUANTILES(valor, 4) AS q
  FROM base
),

completo AS (
  SELECT
    b.valor,
    s.media,
    s.desvio_padrao,
    q[OFFSET(1)] AS Q1,
    q[OFFSET(3)] AS Q3,
    q[OFFSET(3)] - q[OFFSET(1)] AS IQR,
    
    (b.valor - s.media) / s.desvio_padrao AS z_score,

    CASE 
      WHEN ABS((b.valor - s.media) / s.desvio_padrao) > 3 THEN TRUE
      ELSE FALSE
    END AS outlier_zscore,

    CASE 
      WHEN b.valor < q[OFFSET(1)] - 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
        OR b.valor > q[OFFSET(3)] + 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
      THEN TRUE
      ELSE FALSE
    END AS outlier_iqr
  FROM base b, stats s, quartis
),

classificados AS (
  SELECT
    *,
    CASE
      WHEN outlier_zscore AND outlier_iqr THEN 'FORTE OUTLIER'
      WHEN outlier_zscore OR outlier_iqr THEN 'MODERADO'
      ELSE 'NORMAL'
    END AS categoria_outlier
  FROM completo
)

-- Aqui você conta só os "FORTE OUTLIER"
SELECT COUNT(*) AS total_forte_outliers
FROM classificados
WHERE categoria_outlier = 'FORTE OUTLIER'

```
```SQL
WITH base AS (
  SELECT
    SAFE_CAST(number_times_delayed_payment_loan_30_59_days AS FLOAT64) AS valor
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`
  WHERE SAFE_CAST(number_times_delayed_payment_loan_30_59_days AS FLOAT64) IS NOT NULL
),


stats AS (
  SELECT
    AVG(valor) AS media,
    STDDEV(valor) AS desvio_padrao
  FROM base
),

quartis AS (
  SELECT
    APPROX_QUANTILES(valor, 4) AS q
  FROM base
),

completo AS (
  SELECT
    b.valor,
    s.media,
    s.desvio_padrao,
    q[OFFSET(1)] AS Q1,
    q[OFFSET(3)] AS Q3,
    q[OFFSET(3)] - q[OFFSET(1)] AS IQR,
    
    (b.valor - s.media) / s.desvio_padrao AS z_score,

    CASE 
      WHEN ABS((b.valor - s.media) / s.desvio_padrao) > 3 THEN TRUE
      ELSE FALSE
    END AS outlier_zscore,

    CASE 
      WHEN b.valor < q[OFFSET(1)] - 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
        OR b.valor > q[OFFSET(3)] + 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
      THEN TRUE
      ELSE FALSE
    END AS outlier_iqr
  FROM base b, stats s, quartis
),

classificados AS (
  SELECT
    *,
    CASE
      WHEN outlier_zscore AND outlier_iqr THEN 'FORTE OUTLIER'
      WHEN outlier_zscore OR outlier_iqr THEN 'MODERADO'
      ELSE 'NORMAL'
    END AS categoria_outlier
  FROM completo
)

-- Aqui você conta só os "FORTE OUTLIER"
SELECT COUNT(*) AS total_forte_outliers
FROM classificados
WHERE categoria_outlier = 'FORTE OUTLIER'

```
```SQL
WITH base AS (
  SELECT
    SAFE_CAST(REGEXP_REPLACE(debt_ratio, r'\.', '') AS FLOAT64) AS valor
  FROM
    `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`
  WHERE SAFE_CAST(REGEXP_REPLACE(debt_ratio, r'\.', '') AS FLOAT64) IS NOT NULL
),

stats AS (
  SELECT
    AVG(valor) AS media,
    STDDEV(valor) AS desvio_padrao
  FROM base
),

quartis AS (
  SELECT
    APPROX_QUANTILES(valor, 4) AS q
  FROM base
),

completo AS (
  SELECT
    b.valor,
    s.media,
    s.desvio_padrao,
    q[OFFSET(1)] AS Q1,
    q[OFFSET(3)] AS Q3,
    q[OFFSET(3)] - q[OFFSET(1)] AS IQR,
    
    (b.valor - s.media) / s.desvio_padrao AS z_score,

    CASE 
      WHEN ABS((b.valor - s.media) / s.desvio_padrao) > 3 THEN TRUE
      ELSE FALSE
    END AS outlier_zscore,

    CASE 
      WHEN b.valor < q[OFFSET(1)] - 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
        OR b.valor > q[OFFSET(3)] + 1.5 * (q[OFFSET(3)] - q[OFFSET(1)])
      THEN TRUE
      ELSE FALSE
    END AS outlier_iqr
  FROM base b, stats s, quartis
),

classificados AS (
  SELECT
    *,
    CASE
      WHEN outlier_zscore AND outlier_iqr THEN 'FORTE OUTLIER'
      WHEN outlier_zscore OR outlier_iqr THEN 'MODERADO'
      ELSE 'NORMAL'
    END AS categoria_outlier
  FROM completo
)

-- Aqui você conta só os "FORTE OUTLIER"
SELECT COUNT(*) AS total_forte_outliers
FROM classificados
WHERE categoria_outlier = 'FORTE OUTLIER'

```
2.1.8 🔵Criar novas variáveis
```SQL
SELECT DISTINCT loan_type
FROM `laboratoria-projeto3.dados_projeto3.Empréstimo_categoricas`;
```
```SQL
CREATE OR REPLACE VIEW `laboratoria-projeto3.dados_projeto3.Empréstimo_por_usuario` AS
SELECT
  user_id,
  COUNTIF(loan_type = 'REAL ESTATE') AS qtd_real_estate,
  COUNTIF(loan_type = 'OTHER') AS qtd_other,
  COUNT(*) AS total_loan
FROM
  `laboratoria-projeto3.dados_projeto3.Empréstimo_categoricas`
GROUP BY
  user_id
ORDER BY
  user_id;

```
2.1.9 🔵 Unir tabelas
```SQL
CREATE OR REPLACE TABLE `laboratoria-projeto3.dados_projeto3.Default_T` AS
SELECT  
  user_id,
  default_flag,
FROM 
  `laboratoria-projeto3.dados_projeto3.Default`


SELECT * FROM`laboratoria-projeto3.dados_projeto3.Default_T`
```
```SQL
CREATE OR REPLACE TABLE `laboratoria-projeto3.dados_projeto3.Empréstimo_T` AS
SELECT  
  user_id,
  qtd_real_estate,
  qtd_other,
  total_loan
FROM 
  `laboratoria-projeto3.dados_projeto3.Empréstimo_por_usuario`


SELECT * FROM `laboratoria-projeto3.dados_projeto3.Empréstimo_T`
```
```SQL
CREATE OR REPLACE TABLE `laboratoria-projeto3.dados_projeto3.Loans_detail_T` AS
SELECT  
  user_id,
  using_lines_not_secured_personal_assets,
  number_times_delayed_payment_loan_30_59_days,
  debt_ratio,
FROM 
  `laboratoria-projeto3.dados_projeto3.Loans_detail_dados_fora_escopo`


SELECT * FROM `laboratoria-projeto3.dados_projeto3.Loans_detail_T`
```
```SQL
CREATE OR REPLACE TABLE `laboratoria-projeto3.dados_projeto3.User_T` AS
SELECT  
  user_id,
  age,
  last_month_salary,
  number_dependents
FROM 
  `laboratoria-projeto3.dados_projeto3.User_semSex`

SELECT * FROM `laboratoria-projeto3.dados_projeto3.User_T`
```
```SQL
CREATE OR REPLACE TABLE `laboratoria-projeto3.dados_projeto3.User_T` AS 
SELECT
  t1.*,
  t2.default_flag,
FROM
  `laboratoria-projeto3.dados_projeto3.User_T` AS t1
LEFT JOIN `laboratoria-projeto3.dados_projeto3.Default_T` AS t2
  ON t1.user_id = CAST(t2.user_id AS STRING)

SELECT * FROM `laboratoria-projeto3.dados_projeto3.User_T`


SELECT COUNT(*) AS total_sem_correspondencia
FROM `laboratoria-projeto3.dados_projeto3.User_T` AS t1
LEFT JOIN `laboratoria-projeto3.dados_projeto3.Default_T` AS t2
  ON t1.user_id = CAST(t2.user_id AS STRING)
WHERE t2.user_id IS NULL
```
```SQL
CREATE OR REPLACE TABLE `laboratoria-projeto3.dados_projeto3.User_T` AS 
SELECT
  t1.*,
  t3.qtd_real_estate,
  t3.qtd_other,
  t3.total_loan
FROM
  `laboratoria-projeto3.dados_projeto3.User_T` AS t1
LEFT JOIN `laboratoria-projeto3.dados_projeto3.Empréstimo_T` AS t3
  ON t1.user_id = CAST(t3.user_id AS STRING)

SELECT * FROM `laboratoria-projeto3.dados_projeto3.User_T`


SELECT COUNT(*) AS total_sem_correspondencia
FROM `laboratoria-projeto3.dados_projeto3.User_T` AS t1
LEFT JOIN `laboratoria-projeto3.dados_projeto3.Empréstimo_T` AS t3
  ON t1.user_id = CAST(t3.user_id AS STRING)
WHERE t3.user_id IS NULL


SELECT t1.user_id
FROM `laboratoria-projeto3.dados_projeto3.User_T` AS t1
LEFT JOIN `laboratoria-projeto3.dados_projeto3.Empréstimo_T` AS t3
  ON t1.user_id = CAST(t3.user_id AS STRING)
WHERE t3.user_id IS NULL
```
```SQL
CREATE OR REPLACE TABLE `laboratoria-projeto3.dados_projeto3.User_T` AS 
SELECT
  t1.*,
  t4.using_lines_not_secured_personal_assets,
  t4.number_times_delayed_payment_loan_30_59_days,
  t4.debt_ratio
FROM
  `laboratoria-projeto3.dados_projeto3.User_T` AS t1
LEFT JOIN `laboratoria-projeto3.dados_projeto3.Loans_detail_T` AS t4
  ON t1.user_id = CAST(t4.user_id AS STRING)
  
SELECT * FROM `laboratoria-projeto3.dados_projeto3.User_T`



SELECT COUNT(*) AS total_sem_correspondencia
FROM `laboratoria-projeto3.dados_projeto3.User_T` AS t1
LEFT JOIN `laboratoria-projeto3.dados_projeto3.Loans_detail_T` AS t4
  ON t1.user_id = CAST(t4.user_id AS STRING)
WHERE t4.user_id IS NULL
```
```SQL
CREATE OR REPLACE TABLE `laboratoria-projeto3.dados_projeto3.Tabela` AS
SELECT
  t1.user_id,
  t1.age,
  t1.last_month_salary,
  t1.number_dependents,
  t2.default_flag,
  t3.qtd_real_estate,
  t3.qtd_other,
  t3.total_loan,
  t4.using_lines_not_secured_personal_assets,
  t4.number_times_delayed_payment_loan_30_59_days,
  t4.debt_ratio
FROM
  `laboratoria-projeto3.dados_projeto3.User_T` AS t1
LEFT JOIN `laboratoria-projeto3.dados_projeto3.Default_T` AS t2
  ON t1.user_id = CAST(t2.user_id AS STRING)
LEFT JOIN `laboratoria-projeto3.dados_projeto3.Empréstimo_T` AS t3
  ON t1.user_id = CAST(t3.user_id AS STRING)
LEFT JOIN `laboratoria-projeto3.dados_projeto3.Loans_detail_T` AS t4
  ON t1.user_id = CAST(t4.user_id AS STRING) 


select * from `laboratoria-projeto3.dados_projeto3.Tabela`

-- ver se tem nulos na tabela - qtd
SELECT COUNT(*) AS total_com_nulos
FROM `laboratoria-projeto3.dados_projeto3.Tabela`
WHERE
  user_id IS NULL OR
  age IS NULL OR
  last_month_salary IS NULL OR
  number_dependents IS NULL OR
  default_flag IS NULL OR
  qtd_real_estate IS NULL OR
  qtd_other IS NULL OR
  total_loan IS NULL OR
  using_lines_not_secured_personal_assets IS NULL OR
  number_times_delayed_payment_loan_30_59_days IS NULL OR
  debt_ratio IS NULL;

--retorna a qtd de nulos por coluna
SELECT
  COUNTIF(user_id IS NULL) AS user_id_nulo,
  COUNTIF(age IS NULL) AS age_nulo,
  COUNTIF(last_month_salary IS NULL) AS last_month_salary_nulo,
  COUNTIF(number_dependents IS NULL) AS number_dependents_nulo,
  COUNTIF(default_flag IS NULL) AS default_flag_nulo,
  COUNTIF(qtd_real_estate IS NULL) AS qtd_real_estate_nulo,
  COUNTIF(qtd_other IS NULL) AS qtd_other_nulo,
  COUNTIF(total_loan IS NULL) AS total_loan_nulo,
  COUNTIF(using_lines_not_secured_personal_assets IS NULL) AS usando_linhas_nulo,
  COUNTIF(number_times_delayed_payment_loan_30_59_days IS NULL) AS pagamentos_atraso_nulo,
  COUNTIF(debt_ratio IS NULL) AS debt_ratio_nulo
FROM `laboratoria-projeto3.dados_projeto3.Tabela`;


```
```SQL
CREATE OR REPLACE TABLE `laboratoria-projeto3.dados_projeto3.Tabela` AS
SELECT
  t1.user_id,
  t1.age,
  t1.last_month_salary,
  t1.number_dependents,
  t2.default_flag,
  t3.qtd_real_estate,
  t3.qtd_other,
  t3.total_loan,
  t4.using_lines_not_secured_personal_assets,
  t4.number_times_delayed_payment_loan_30_59_days,
  t4.debt_ratio
FROM
  `laboratoria-projeto3.dados_projeto3.User_T` AS t1
LEFT JOIN `laboratoria-projeto3.dados_projeto3.Default_T` AS t2
  ON t1.user_id = CAST(t2.user_id AS STRING)
INNER JOIN `laboratoria-projeto3.dados_projeto3.Empréstimo_T` AS t3
  ON t1.user_id = CAST(t3.user_id AS STRING)
LEFT JOIN `laboratoria-projeto3.dados_projeto3.Loans_detail_T` AS t4
  ON t1.user_id = CAST(t4.user_id AS STRING) 


select * from `laboratoria-projeto3.dados_projeto3.Tabela`

-- ver se tem nulos na tabela - qtd
SELECT COUNT(*) AS total_com_nulos
FROM `laboratoria-projeto3.dados_projeto3.Tabela`
WHERE
  user_id IS NULL OR
  age IS NULL OR
  last_month_salary IS NULL OR
  number_dependents IS NULL OR
  default_flag IS NULL OR
  qtd_real_estate IS NULL OR
  qtd_other IS NULL OR
  total_loan IS NULL OR
  using_lines_not_secured_personal_assets IS NULL OR
  number_times_delayed_payment_loan_30_59_days IS NULL OR
  debt_ratio IS NULL;

--retorna a qtd de nulos por coluna
SELECT
  COUNTIF(user_id IS NULL) AS user_id_nulo,
  COUNTIF(age IS NULL) AS age_nulo,
  COUNTIF(last_month_salary IS NULL) AS last_month_salary_nulo,
  COUNTIF(number_dependents IS NULL) AS number_dependents_nulo,
  COUNTIF(default_flag IS NULL) AS default_flag_nulo,
  COUNTIF(qtd_real_estate IS NULL) AS qtd_real_estate_nulo,
  COUNTIF(qtd_other IS NULL) AS qtd_other_nulo,
  COUNTIF(total_loan IS NULL) AS total_loan_nulo,
  COUNTIF(using_lines_not_secured_personal_assets IS NULL) AS usando_linhas_nulo,
  COUNTIF(number_times_delayed_payment_loan_30_59_days IS NULL) AS pagamentos_atraso_nulo,
  COUNTIF(debt_ratio IS NULL) AS debt_ratio_nulo
FROM `laboratoria-projeto3.dados_projeto3.Tabela`;


```
```SQL
SELECT
  SUM(CASE WHEN user_id IS NULL THEN 1 ELSE 0 END) AS user_id_nulls,
  SUM(CASE WHEN age IS NULL THEN 1 ELSE 0 END) AS age_nulls,
  SUM(CASE WHEN last_month_salary IS NULL THEN 1 ELSE 0 END) AS last_month_salary_nulls,
  SUM(CASE WHEN number_dependents IS NULL THEN 1 ELSE 0 END) AS number_dependents_nulls,
FROM
  `laboratoria-projeto3.dados_projeto3.Tabela`

  
```
```SQL
SELECT
  SUM(CASE WHEN CAST(user_id AS INT64) = 0 THEN 1 ELSE 0 END) AS user_id_nulls,
  SUM(CASE WHEN CAST(age AS INT64)  = 0 THEN 1 ELSE 0 END) AS age_nulls,
  SUM(CASE WHEN CAST(last_month_salary AS FLOAT64) = 0 THEN 1 ELSE 0 END) AS last_month_salary_nulls,
  SUM(CASE WHEN CAST(number_dependents AS FLOAT64) = 0 THEN 1 ELSE 0 END) AS number_dependents_nulls,
FROM
  `laboratoria-projeto3.dados_projeto3.Tabela`
```
2.1.10🔵 Construir tabelas auxiliares
```SQL
```
```SQL
```
```SQL
```
```SQL
```
```SQL
```
```SQL
```


  


  


  
