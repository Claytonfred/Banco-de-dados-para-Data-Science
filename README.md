# Banco-de-dados-para-Data-Science

import mysql.connector
import pandas as pd
import matplotlib.pyplot as plt
# Configurações de acesso
db_config = {
    &#39;host&#39;: &#39;DSN109JMVNS04&#39;,
    &#39;user&#39;: &#39;Banco_DATASCIENCE&#39;,
    &#39;password&#39;: &#39;senaisp&#39;,
    &#39;database&#39;: &#39;analise_vendas_avancada&#39;
}
# Consulta(CTE)
query = &quot;&quot;&quot;
WITH stats AS (
    SELECT
        vendedor,
        SUM((quantidade * valor_unitario) - desconto) AS receita_total,
        SUM(quantidade * custo_unitario) AS custo_total
    FROM vendas_corporativas
    GROUP BY vendedor
)
SELECT
    vendedor,
    receita_total,
    custo_total,
    (receita_total - custo_total) AS lucro_total
FROM stats;
&quot;&quot;&quot;
try:
   
    conexao = mysql.connector.connect(**db_config)
    df = pd.read_sql_query(query, conexao)
   
    # Cálculo da Margem: (Lucro / Receita) * 100
    df[&#39;margem_lucro&#39;] = (df[&#39;lucro_total&#39;] / df[&#39;receita_total&#39;]) * 100
   
    # Ordenar decrescente
    df = df.sort_values(by=&#39;margem_lucro&#39;, ascending=False)
    print(df)
   

    df.plot()
plt.bar(kind=&#39;bar&#39;, x=&#39;vendedor&#39;, y=&#39;lucro_total&#39;, color=&#39;skyblue&#39;,
edgecolor=&#39;navy&#39;)
    plt.title(&#39;Lucro Total por Vendedor&#39;)
plt.xlabel(&#39;vendedor&#39;)
    plt.ylabel(&quot;Lucro (R$)&quot;)
plt.axhline(axis=&#39;y&#39;, linestyle=&#39;--&#39;, alpha=0.7) # Grafico horizontal
plt.grid(True)
    plt.tight_layout()
    plt.show()
finally:
    # Fechamento da conexão
    if &#39;conexao&#39; in locals() and conexao.is_connected():
        conexao.close()
        print(&quot;Conexão encerrada.&quot;)
