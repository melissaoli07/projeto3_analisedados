# projeto3_analisedados
Projeto desenvolvido no Bootcamp de Análise de Dados da Laboratório

Ficha Técnica Projeto de Análise de Dados - Risco Relativo

Duração: 3-4 sprints (semanas) 

✅ Descrição do objetivo:

Durante o projeto, meu objetivo principal foi automatizar a análise de crédito da instituição “Super Caja”, identificando perfis de clientes com maior risco de inadimplência. A partir disso, trabalhei para calcular o risco relativo entre grupos de clientes (por idade, renda, número de dependentes, etc.) e aplicar um modelo de regressão logística para classificar os clientes em “bons” e “maus pagadores”. O projeto visa apoiar o banco na tomada de decisão estratégica na concessão de crédito, reduzindo inadimplência e acelerando o processo de análise.

🔧 Pré-processamento de dados:

Os dados foram carregados como tabelas no BigQuery, e, a partir disso, criei uma tabela unificada a nível de cliente. Tratei valores nulos sem exclusão direta, substituindo-os por valores representativos (como médias ou categorias “desconhecido”). Identifiquei e corrigi duplicações. Também criei novas variáveis, como faixa etária, score de dependentes, classificação do nível de endividamento e indicador de inadimplência (variável binária). Todas as transformações foram documentadas com views no BigQuery, facilitando o rastreamento e reprodutibilidade do processo.

📊 Métodos e técnicas:

Realizei uma análise exploratória completa, visualizando a distribuição de variáveis como idade, renda e número de atrasos. Calculei o risco relativo (RR) entre grupos para entender quais variáveis aumentam a chance de inadimplência (por exemplo: RR de clientes com mais de 3 dependentes vs. os que não têm). Em seguida, apliquei regressão logística para prever a inadimplência com base em múltiplas variáveis. Interpretei os coeficientes para entender a contribuição de cada fator no modelo. Por fim, conectei os dados ao Looker Studio para criar um dashboard interativo, exibindo o risco relativo, distribuições, segmentos de risco e resultados do modelo.

✅ Conclusões:

A análise confirmou que características como alto número de dependentes, idade jovem e histórico de atrasos estão fortemente associadas ao risco de inadimplência. O risco relativo ajudou a destacar grupos críticos, e a regressão logística comprovou a validade desses padrões com uma boa performance preditiva. A classificação dos clientes em perfis de risco permite ao banco aplicar políticas de crédito mais assertivas, mitigando perdas financeiras e otimizando a análise.


💬 Comentários e anotações:

Documentei todas as consultas SQL utilizadas no BigQuery para transformar os dados, o que facilita manutenções futuras. Notei que o uso de camadas de views organizadas por etapas (pré-processamento, visualização, risco relativo, modelo) torna o projeto mais legível. Durante a apresentação em vídeo, destaquei as principais descobertas com suporte visual do dashboard. 

🛠 Ferramentas e Tecnologias:

Google BigQuery (SQL, views)

Looker Studio (dashboard)

Apresentações Google (para apresentação do projeto)

Loom (gravação da apresentação em vídeo)

Google Colab

Links:

Looker Studio (dashboard): https://lookerstudio.google.com/reporting/02a1e198-d71b-4999-98df-93878924c167

Apresentação: https://docs.google.com/presentation/d/1hZwiuEhbOphSibuxOX_lk4jBR8YktqDMC3Q9dSsOPu8/edit?usp=sharing

Google Colab: https://colab.research.google.com/drive/1X1gKGUYTwB2bjCKOJ3Y8dzAPX0ssQF_2?usp=sharing

Vídeo Loom: https://www.loom.com/share/108c5f01a4c743e28bf7091036519a85?sid=3c0d5b8f-71f5-4c71-b5cf-91eb73d65e41
