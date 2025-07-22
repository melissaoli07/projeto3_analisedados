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
WITH usuarios_salario_maior_2000 AS (
  SELECT
    user_id,
    age,
    last_month_salary,
    number_dependents
  FROM `laboratoria-projeto3.dados_projeto3.Tabela`
  WHERE CAST(last_month_salary AS FLOAT64) > 2000
),

usuarios_em_default AS (
  SELECT
    CAST(user_id AS STRING) AS user_id,
    default_flag
  FROM `laboratoria-projeto3.dados_projeto3.Tabela`
  WHERE default_flag = 1
)

SELECT
  u.user_id,
  u.age,
  u.last_month_salary,
  u.number_dependents,
  d.default_flag
FROM usuarios_salario_maior_2000 u
LEFT JOIN usuarios_em_default d
  ON u.user_id = d.user_id
WHERE d.default_flag = 1

```
```SQL
WITH faixa_dependentes AS (
  SELECT
    user_id,
    last_month_salary,
    number_dependents,
    CASE
      WHEN CAST(number_dependents AS INT64) = 0 THEN 'Sem dependentes'
      WHEN CAST(number_dependents AS INT64) BETWEEN 1 AND 2 THEN 'Poucos dependentes'
      WHEN CAST(number_dependents AS INT64) >= 3 THEN 'Muitos dependentes'
      ELSE 'Desconhecido'
    END AS grupo_dependentes
  FROM `laboratoria-projeto3.dados_projeto3.Tabela`
),

default_por_usuario AS (
  SELECT
    CAST(user_id AS STRING) AS user_id,
    default_flag
  FROM `laboratoria-projeto3.dados_projeto3.Tabela`
)

SELECT
  f.grupo_dependentes,
  COUNT(*) AS total_usuarios,
  COUNTIF(d.default_flag = 1) AS total_em_default,
  ROUND(COUNTIF(d.default_flag = 1) / COUNT(*) * 100, 2) AS percentual_default
FROM faixa_dependentes f
LEFT JOIN default_por_usuario d
  ON f.user_id = d.user_id
GROUP BY f.grupo_dependentes
ORDER BY percentual_default DESC

```
2.2.6 🟣 Calcular quartis, decis ou percentis
```SQL
WITH idade_quartis AS (
  SELECT
    user_id,
    age,
    default_flag,
    NTILE(4) OVER (ORDER BY age) AS faixa_idade
  FROM
    `laboratoria-projeto3.dados_projeto3.Tabela`
),
risco_por_quartil AS (
  SELECT
    faixa_idade,
    COUNT(*) AS total_pessoas,
    SUM(default_flag) AS total_inadimplentes,
    SAFE_DIVIDE(SUM(default_flag), COUNT(*)) AS risco_inadimplencia
  FROM
    idade_quartis
  GROUP BY
    faixa_idade
)

SELECT *
FROM risco_por_quartil
ORDER BY risco_inadimplencia DESC;

```
```SQL
WITH idade_quartis AS (
  SELECT
    user_id,
    SAFE_CAST(age AS INT64) AS age_num,
    default_flag,
    NTILE(4) OVER (ORDER BY SAFE_CAST(age AS INT64)) AS faixa_idade
  FROM
    `laboratoria-projeto3.dados_projeto3.Tabela`
  WHERE age IS NOT NULL
),

risco_por_quartil AS (
  SELECT
    faixa_idade,
    COUNT(*) AS total_pessoas,
    SUM(default_flag) AS total_inadimplentes,
    SAFE_DIVIDE(SUM(default_flag), COUNT(*)) AS risco_inadimplencia,
    MIN(age_num) AS idade_minima,
    MAX(age_num) AS idade_maxima
  FROM
    idade_quartis
  GROUP BY
    faixa_idade
)

SELECT *
FROM risco_por_quartil
ORDER BY risco_inadimplencia DESC;

```
```SQL
WITH salario_quartis AS (
  SELECT
    user_id,
    SAFE_CAST(last_month_salary AS FLOAT64) AS salario,
    default_flag,
    NTILE(4) OVER (ORDER BY SAFE_CAST(last_month_salary AS FLOAT64)) AS faixa_salario
  FROM
    `laboratoria-projeto3.dados_projeto3.Tabela`
  WHERE
    last_month_salary IS NOT NULL
),

risco_salario AS (
  SELECT
    faixa_salario AS faixa,
    'salario' AS variavel,
    COUNT(*) AS total,
    SUM(default_flag) AS inadimplentes,
    SAFE_DIVIDE(SUM(default_flag), COUNT(*)) AS risco,
    MIN(salario) AS salario_minimo,
    MAX(salario) AS salario_maximo
  FROM salario_quartis
  GROUP BY faixa_salario
)

SELECT *
FROM risco_salario
ORDER BY risco DESC;

```
```SQL
WITH
  debt_quartis AS (
    SELECT
      user_id,
      default_flag,
      SAFE_CAST(REPLACE(debt_ratio, ',', '.') AS FLOAT64) AS debt_ratio_num,
      NTILE(4) OVER (ORDER BY SAFE_CAST(REPLACE(debt_ratio, ',', '.') AS FLOAT64)) AS faixa_endividamento
    FROM
      `laboratoria-projeto3.dados_projeto3.Tabela`
    WHERE
      debt_ratio IS NOT NULL
  ),

  risco_debt AS (
    SELECT
      faixa_endividamento AS faixa,
      'debt_ratio' AS variavel,
      COUNT(*) AS total,
      SUM(default_flag) AS inadimplentes,
      SAFE_DIVIDE(SUM(default_flag), COUNT(*)) AS risco,
      MIN(debt_ratio_num) AS debt_ratio_minimo,
      MAX(debt_ratio_num) AS debt_ratio_maximo
    FROM debt_quartis
    GROUP BY faixa_endividamento
  )

SELECT *
FROM risco_debt
ORDER BY risco DESC;

```
```SQL
WITH
  debt_quartis AS (
    SELECT
      user_id,
      default_flag,
      SAFE_CAST(REPLACE(debt_ratio, ',', '.') AS FLOAT64) AS debt_ratio_num,
      NTILE(4) OVER (ORDER BY SAFE_CAST(REPLACE(debt_ratio, ',', '.') AS FLOAT64)) AS faixa_endividamento
    FROM
      `laboratoria-projeto3.dados_projeto3.Tabela`
    WHERE
      debt_ratio IS NOT NULL
      AND SAFE_CAST(REPLACE(debt_ratio, ',', '.') AS FLOAT64) IS NOT NULL
  ),

  risco_debt AS (
    SELECT
      faixa_endividamento AS faixa,
      'debt_ratio' AS variavel,
      COUNT(*) AS total,
      SUM(default_flag) AS inadimplentes,
      SAFE_DIVIDE(SUM(default_flag), COUNT(*)) AS risco,
      MIN(debt_ratio_num) AS debt_ratio_minimo,
      MAX(debt_ratio_num) AS debt_ratio_maximo
    FROM debt_quartis
    GROUP BY faixa_endividamento
  )

SELECT *
FROM risco_debt
ORDER BY risco DESC;

```
```SQL
WITH dependentes_quartis AS (
  SELECT
    user_id,
    default_flag,
    SAFE_CAST(number_dependents AS INT64) AS num_dependentes,
    NTILE(4) OVER (ORDER BY SAFE_CAST(number_dependents AS INT64)) AS faixa_dependentes
  FROM
    `laboratoria-projeto3.dados_projeto3.Tabela`
  WHERE
    number_dependents IS NOT NULL
    AND SAFE_CAST(number_dependents AS INT64) IS NOT NULL
),

risco_dependentes AS (
  SELECT
    faixa_dependentes AS faixa,
    'dependentes' AS variavel,
    COUNT(*) AS total,
    SUM(default_flag) AS inadimplentes,
    SAFE_DIVIDE(SUM(default_flag), COUNT(*)) AS risco,
    MIN(num_dependentes) AS dependentes_min,
    MAX(num_dependentes) AS dependentes_max
  FROM dependentes_quartis
  GROUP BY faixa_dependentes
)

SELECT *
FROM risco_dependentes
ORDER BY risco DESC;

```
2.2.7🟣 Calcular correlação entre variáveis numéricas
```SQL
SELECT
  CORR(CAST(age AS FLOAT64), CAST(default_flag AS FLOAT64)) AS corr_idade_default
FROM
  `laboratoria-projeto3.dados_projeto3.Tabela`
WHERE
  age IS NOT NULL AND default_flag IS NOT NULL;
-0.069563668817095428
```
```SQL
SELECT
  CORR(CAST(last_month_salary AS FLOAT64), CAST(default_flag AS FLOAT64)) AS corr_salario_default
FROM `laboratoria-projeto3.dados_projeto3.Tabela`
WHERE last_month_salary IS NOT NULL AND default_flag IS NOT NULL;
-0.015380516295827141
```
```SQL
SELECT
  CORR(SAFE_CAST(REPLACE(debt_ratio, ',', '.') AS FLOAT64), CAST(default_flag AS FLOAT64)) AS corr_debt_default
FROM `laboratoria-projeto3.dados_projeto3.Tabela`
WHERE debt_ratio IS NOT NULL AND default_flag IS NOT NULL;

0.00037328745328426627
```
```SQL
SELECT
  CORR(CAST(number_dependents AS FLOAT64), CAST(default_flag AS FLOAT64)) AS corr_dependentes_default
FROM `laboratoria-projeto3.dados_projeto3.Tabela`
WHERE number_dependents IS NOT NULL AND default_flag IS NOT NULL;
0.036806706787677064
```
2.3.1 🔴 Calcular risco relativo
```SQL
WITH base AS ( 
  SELECT
    *,
    SAFE_CAST(age AS FLOAT64) AS idade,
    SAFE_CAST(last_month_salary AS FLOAT64) AS salario,
    SAFE_CAST(REPLACE(debt_ratio, ',', '.') AS FLOAT64) AS endividamento,
    SAFE_CAST(number_dependents AS INT64) AS dependentes,
    SAFE_CAST(number_times_delayed_payment_loan_30_59_days AS INT64) AS atraso_30_59,
    SAFE_CAST(REPLACE(using_lines_not_secured_personal_assets, ',', '.') AS FLOAT64) AS linhas_credito_sem_garantia,
    SAFE_CAST(total_loan AS INT64) AS total_emprestimos
  FROM
    `laboratoria-projeto3.dados_projeto3.Tabela`
  WHERE
    age IS NOT NULL AND
    last_month_salary IS NOT NULL AND
    debt_ratio IS NOT NULL AND
    number_dependents IS NOT NULL AND
    number_times_delayed_payment_loan_30_59_days IS NOT NULL AND
    using_lines_not_secured_personal_assets IS NOT NULL AND
    total_loan IS NOT NULL
),

quartis AS (
  SELECT
    *,
    NTILE(4) OVER (ORDER BY idade) AS quartil_idade,
    NTILE(4) OVER (ORDER BY salario) AS quartil_salario,
    NTILE(4) OVER (ORDER BY endividamento) AS quartil_endividamento,
    NTILE(4) OVER (ORDER BY dependentes) AS quartil_dependentes,
    NTILE(4) OVER (ORDER BY atraso_30_59) AS quartil_atraso_30_59,
    NTILE(4) OVER (ORDER BY linhas_credito_sem_garantia) AS quartil_linhas_credito,
    NTILE(4) OVER (ORDER BY total_emprestimos) AS quartil_total_emprestimos
  FROM base
),

contagem AS (
  SELECT
    'idade' AS variavel,
    quartil_idade AS quartil,
    COUNTIF(default_flag = 1) AS inadimplentes,
    COUNTIF(default_flag = 0) AS adimplentes
  FROM quartis
  GROUP BY quartil_idade

  UNION ALL

  SELECT
    'salario',
    quartil_salario,
    COUNTIF(default_flag = 1),
    COUNTIF(default_flag = 0)
  FROM quartis
  GROUP BY quartil_salario

  UNION ALL

  SELECT
    'endividamento',
    quartil_endividamento,
    COUNTIF(default_flag = 1),
    COUNTIF(default_flag = 0)
  FROM quartis
  GROUP BY quartil_endividamento

  UNION ALL

  SELECT
    'dependentes',
    quartil_dependentes,
    COUNTIF(default_flag = 1),
    COUNTIF(default_flag = 0)
  FROM quartis
  GROUP BY quartil_dependentes

  UNION ALL

  SELECT
    'atraso_30_59',
    quartil_atraso_30_59,
    COUNTIF(default_flag = 1),
    COUNTIF(default_flag = 0)
  FROM quartis
  GROUP BY quartil_atraso_30_59

  UNION ALL

  SELECT
    'linhas_credito_sem_garantia',
    quartil_linhas_credito,
    COUNTIF(default_flag = 1),
    COUNTIF(default_flag = 0)
  FROM quartis
  GROUP BY quartil_linhas_credito

  UNION ALL

  SELECT
    'total_emprestimos',
    quartil_total_emprestimos,
    COUNTIF(default_flag = 1),
    COUNTIF(default_flag = 0)
  FROM quartis
  GROUP BY quartil_total_emprestimos
),

incidencia AS (
  SELECT
    variavel,
    quartil,
    inadimplentes,
    adimplentes,
    SAFE_DIVIDE(inadimplentes, inadimplentes + adimplentes) AS incidencia
  FROM contagem
),

rr_final AS (
  SELECT
    a.variavel,
    a.quartil,
    a.incidencia,
    SAFE_DIVIDE(a.incidencia, b.incidencia) AS risco_relativo
  FROM incidencia a
  JOIN incidencia b
  ON a.variavel = b.variavel AND b.quartil = 1
)

SELECT *
FROM rr_final
ORDER BY variavel, quartil;

```
2.3.2 🔴 Aplicar segmentação por Score
```SQL
WITH base AS (
  SELECT
    *,
    SAFE_CAST(age AS FLOAT64) AS idade,
    SAFE_CAST(last_month_salary AS FLOAT64) AS salario,
    SAFE_CAST(REPLACE(debt_ratio, ',', '.') AS FLOAT64) AS endividamento,
    SAFE_CAST(number_dependents AS INT64) AS dependentes,
    SAFE_CAST(number_times_delayed_payment_loan_30_59_days AS INT64) AS atraso_30_59,
    SAFE_CAST(REPLACE(using_lines_not_secured_personal_assets, ',', '.') AS FLOAT64) AS linhas_credito_sem_garantia
  FROM
    `laboratoria-projeto3.dados_projeto3.Tabela`
  WHERE
    age IS NOT NULL AND
    last_month_salary IS NOT NULL AND
    debt_ratio IS NOT NULL AND
    number_dependents IS NOT NULL AND
    number_times_delayed_payment_loan_30_59_days IS NOT NULL AND
    using_lines_not_secured_personal_assets IS NOT NULL
),

quartis AS (
  SELECT
    *,
    NTILE(4) OVER (ORDER BY idade) AS quartil_idade,
    NTILE(4) OVER (ORDER BY salario) AS quartil_salario,
    NTILE(4) OVER (ORDER BY endividamento) AS quartil_endividamento,
    NTILE(4) OVER (ORDER BY dependentes, user_id) AS quartil_dependentes,
    NTILE(4) OVER (ORDER BY atraso_30_59, user_id) AS quartil_atraso_30_59,
    NTILE(4) OVER (ORDER BY linhas_credito_sem_garantia) AS quartil_linhas_credito
  FROM base
)

SELECT
  user_id,
  default_flag,

  -- Quartis
  quartil_idade,
  quartil_salario,
  quartil_dependentes,
  quartil_atraso_30_59,

  -- Dummies
  IF(quartil_idade = 1, 1, 0) AS dummy_idade_risco,
  IF(quartil_salario = 1, 1, 0) AS dummy_salario_risco,
  IF(quartil_dependentes = 4, 1, 0) AS dummy_dependentes_risco,
  IF(quartil_atraso_30_59 = 4, 1, 0) AS dummy_atraso_risco,

  -- SCORE: soma das dummies de risco
  (
    IF(quartil_idade = 1, 1, 0) +
    IF(quartil_salario = 1, 1, 0) +
    IF(quartil_dependentes = 4, 1, 0) +
    IF(quartil_atraso_30_59 = 4, 1, 0)
  ) AS score_risco

FROM quartis;

```
```SQL
CREATE OR REPLACE TABLE `laboratoria-projeto3.dados_projeto3.Tabela_score` AS
WITH base AS ( 
  SELECT
    *,
    SAFE_CAST(age AS FLOAT64) AS idade,
    SAFE_CAST(last_month_salary AS FLOAT64) AS salario,
    SAFE_CAST(REPLACE(debt_ratio, ',', '.') AS FLOAT64) AS endividamento,
    SAFE_CAST(number_dependents AS INT64) AS dependentes,
    SAFE_CAST(number_times_delayed_payment_loan_30_59_days AS INT64) AS atraso_30_59,
    SAFE_CAST(REPLACE(using_lines_not_secured_personal_assets, ',', '.') AS FLOAT64) AS linhas_credito_sem_garantia
  FROM
    `laboratoria-projeto3.dados_projeto3.Tabela`
  WHERE
    age IS NOT NULL AND
    last_month_salary IS NOT NULL AND
    debt_ratio IS NOT NULL AND
    number_dependents IS NOT NULL AND
    number_times_delayed_payment_loan_30_59_days IS NOT NULL AND
    using_lines_not_secured_personal_assets IS NOT NULL
),

quartis AS (
  SELECT
    *,
    NTILE(4) OVER (ORDER BY idade) AS quartil_idade,
    NTILE(4) OVER (ORDER BY salario) AS quartil_salario,
    NTILE(4) OVER (ORDER BY endividamento) AS quartil_endividamento,
    NTILE(4) OVER (ORDER BY dependentes, user_id) AS quartil_dependentes,
    NTILE(4) OVER (ORDER BY atraso_30_59, user_id) AS quartil_atraso_30_59,
    NTILE(4) OVER (ORDER BY linhas_credito_sem_garantia) AS quartil_linhas_credito
  FROM base
)

SELECT
  user_id,
  default_flag,

  -- Quartis
  quartil_idade,
  quartil_salario,
  quartil_dependentes,
  quartil_atraso_30_59,

  -- Dummies
  IF(quartil_idade = 1, 1, 0) AS dummy_idade_risco,
  IF(quartil_salario = 1, 1, 0) AS dummy_salario_risco,
  IF(quartil_dependentes = 4, 1, 0) AS dummy_dependentes_risco,
  IF(quartil_atraso_30_59 = 4, 1, 0) AS dummy_atraso_risco,

  -- SCORE: soma das dummies de risco
  (
    IF(quartil_idade = 1, 1, 0) +
    IF(quartil_salario = 1, 1, 0) +
    IF(quartil_dependentes = 4, 1, 0) +
    IF(quartil_atraso_30_59 = 4, 1, 0)
  ) AS score_risco

FROM quartis;

SELECT * FROM `laboratoria-projeto3.dados_projeto3.Tabela_score`

```
```SQL
SELECT 
  default_flag,
  COUNT(*) AS qtd_clientes,
  AVG(score_risco) AS media_score,
  MIN(score_risco) AS score_min,
  MAX(score_risco) AS score_max
FROM
  `laboratoria-projeto3.dados_projeto3.Tabela_score`
GROUP BY
  default_flag;

```
```SQL
WITH classificacao AS (
  SELECT
    *,
    CASE 
      WHEN score_risco >= 3 THEN 1 -- previsto como inadimplente
      ELSE 0                       -- previsto como adimplente
    END AS previsao
  FROM `laboratoria-projeto3.dados_projeto3.Tabela_score`
)

SELECT
  COUNTIF(default_flag = 1 AND previsao = 1) AS verdadeiros_positivos,  -- acertou inadimplente
  COUNTIF(default_flag = 0 AND previsao = 0) AS verdadeiros_negativos,  -- acertou adimplente
  COUNTIF(default_flag = 0 AND previsao = 1) AS falsos_positivos,       -- errou: previu inadimplente, mas não era
  COUNTIF(default_flag = 1 AND previsao = 0) AS falsos_negativos        -- errou: previu bom, mas era caloteiro
FROM classificacao;

```
```SQL
WITH matriz_confusao AS (
  SELECT
    150 AS vp,
    33379 AS vn,
    1574 AS fp,
    472 AS fn
)

SELECT
  vp,
  vn,
  fp,
  fn,

  SAFE_DIVIDE(vp + vn, vp + vn + fp + fn) AS acuracia,
  SAFE_DIVIDE(vp, vp + fp) AS precisao,
  SAFE_DIVIDE(vp, vp + fn) AS recall,
  SAFE_DIVIDE(vn, vn + fp) AS especificidade,

  CASE
    WHEN (SAFE_DIVIDE(vp, vp + fp) + SAFE_DIVIDE(vp, vp + fn)) = 0 THEN 0
    ELSE
      2 * SAFE_DIVIDE(vp, vp + fp) * SAFE_DIVIDE(vp, vp + fn) /
      (SAFE_DIVIDE(vp, vp + fp) + SAFE_DIVIDE(vp, vp + fn))
  END AS f1_score

FROM matriz_confusao;

```
```SQL
WITH base AS (
  SELECT
    user_id,
    default_flag,
    score_risco
  FROM
    `laboratoria-projeto3.dados_projeto3.Tabela_score`
),

avaliacoes AS (
  SELECT
    corte,
    COUNTIF(default_flag = 1 AND score_risco >= corte) AS vp,
    COUNTIF(default_flag = 0 AND score_risco >= corte) AS fp,
    COUNTIF(default_flag = 1 AND score_risco < corte) AS fn,
    COUNTIF(default_flag = 0 AND score_risco < corte) AS vn
  FROM base,
  UNNEST([0, 1, 2, 3, 4, 5]) AS corte
  GROUP BY corte
),

roc_curve AS (
  SELECT
    corte,
    SAFE_DIVIDE(vp, vp + fn) AS tpr, -- Sensibilidade (eixo Y)
    SAFE_DIVIDE(fp, fp + vn) AS fpr  -- 1 - Especificidade (eixo X)
  FROM avaliacoes
)

SELECT * FROM roc_curve
ORDER BY corte;

```
```SQL
WITH base AS (
  SELECT
    user_id,
    default_flag,
    score_risco
  FROM
    `laboratoria-projeto3.dados_projeto3.Tabela_score`
),

avaliacoes AS (
  SELECT
    corte,
    COUNTIF(default_flag = 1 AND score_risco >= corte) AS vp,
    COUNTIF(default_flag = 0 AND score_risco >= corte) AS fp,
    COUNTIF(default_flag = 1 AND score_risco < corte) AS fn,
    COUNTIF(default_flag = 0 AND score_risco < corte) AS vn
  FROM base,
  UNNEST([0, 1, 2, 3, 4, 5]) AS corte
  GROUP BY corte
),

roc_curve AS (
  SELECT
    corte,
    SAFE_DIVIDE(vp, vp + fn) AS tpr, -- Sensibilidade (eixo Y)
    SAFE_DIVIDE(fp, fp + vn) AS fpr  -- 1 - Especificidade (eixo X)
  FROM avaliacoes
),

roc_ordem AS (
  SELECT
    *,
    LEAD(fpr) OVER (ORDER BY fpr) AS fpr_prox,
    LEAD(tpr) OVER (ORDER BY fpr) AS tpr_prox
  FROM roc_curve
),

auc_calc AS (
  SELECT
    fpr,
    tpr,
    fpr_prox,
    tpr_prox,
    -- Área do trapézio: (base maior + base menor) * altura / 2
    SAFE_MULTIPLY((tpr + tpr_prox) / 2, fpr_prox - fpr) AS area
  FROM roc_ordem
  WHERE fpr_prox IS NOT NULL
)

SELECT
  SUM(area) AS auc
FROM auc_calc;

```
```SQL
CREATE OR REPLACE TABLE `laboratoria-projeto3.dados_projeto3.Tabela_score` AS
SELECT
  *,
  CASE
    WHEN score_risco >= 3 THEN 'Alto risco'
    WHEN score_risco = 2 THEN 'Médio risco'
    ELSE 'Bom pagador'
  END AS classificacao_risco
FROM
  `laboratoria-projeto3.dados_projeto3.Tabela_score`;

```
2.4.4 🟠 Aplicar opções de filtros (controle de dados) para gerenciamento e interação
```SQL
CREATE OR REPLACE TABLE `laboratoria-projeto3.dados_projeto3.Tabela_score1` AS
SELECT
  *,
  CASE
    WHEN score_risco >= 3 THEN 'Alto risco'
    WHEN score_risco = 2 THEN 'Médio risco'
    ELSE 'Bom pagador'
  END AS classificacao_risco
FROM
  `laboratoria-projeto3.dados_projeto3.Tabela_score`;

```
```SQL
CREATE OR REPLACE TABLE laboratoria-projeto3.dados_projeto3.Tabela_final2 AS
SELECT
  risco.user_id,
  risco.default_flag,
  risco.quartil_idade,
  risco.quartil_salario,
  risco.quartil_dependentes,
  risco.quartil_atraso_30_59,
  risco.dummy_idade_risco,
  risco.dummy_salario_risco,
  risco.dummy_dependentes_risco,
  risco.dummy_atraso_risco,
  risco.score_risco,
  risco.classificacao_risco,
  dados.age,
  dados.last_month_salary,
  dados.number_dependents,
  dados.qtd_real_estate,
  dados.qtd_other,
  dados.total_loan,
  dados.using_lines_not_secured_personal_assets,
  dados.number_times_delayed_payment_loan_30_59_days,
  dados.debt_ratio
FROM
  `laboratoria-projeto3.dados_projeto3.Tabela_score1` AS risco
LEFT JOIN
  `laboratoria-projeto3.dados_projeto3.Tabela` AS dados
ON
  risco.user_id = dados.user_id;

```
3.1.1 🔵 Conectar/importar dados para outras ferramentas
```SQL
CREATE OR REPLACE TABLE `laboratoria-projeto3.dados_projeto3.Tabela_final` AS
SELECT 
  -- Colunas da tabela principal
  Tabela.user_id,
  Tabela.age,
  Tabela.last_month_salary,
  Tabela.number_dependents,
  Tabela.default_flag,
  Tabela.qtd_real_estate,
  Tabela.qtd_other,
  Tabela.total_loan,
  Tabela.using_lines_not_secured_personal_assets,
  Tabela.number_times_delayed_payment_loan_30_59_days,
  Tabela.debt_ratio,

  -- Colunas da tabela de score
  Tabela_score.quartil_idade,
  Tabela_score.quartil_salario,
  Tabela_score.quartil_dependentes,
  Tabela_score.quartil_atraso_30_59,
  Tabela_score.dummy_idade_risco,
  Tabela_score.dummy_salario_risco,
  Tabela_score.dummy_dependentes_risco,
  Tabela_score.dummy_atraso_risco,
  Tabela_score.score_risco

FROM 
  `laboratoria-projeto3.dados_projeto3.Tabela` AS Tabela
JOIN 
  `laboratoria-projeto3.dados_projeto3.Tabela_score` AS Tabela_score
ON 
  Tabela.user_id = Tabela_score.user_id

```
