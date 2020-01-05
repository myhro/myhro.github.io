---
date: 2012-11-05 20:08:34
layout: post
title: 'Dica do dia: dicionários nas consultas ao MySQL em Java'
...

Escrever sobre qualquer coisa relacionada ao Java não é necessariamente algo que alguém teria como objetivo em sua carreira, mas a técnica que descreverei logo mais facilita muito a vida de quem é obrigado (não acredito que alguém faria isso por vontade própria) a programar nesta linguagem. A inspiração partiu da forma como outra linguagem também não tão nobre porém largamente utilizada, o PHP, trabalha com consultas a bancos de dados.

Em PHP, podemos facilmente converter o objeto retornado de uma consulta com a função [mysql\_query()](http://php.net/manual/en/function.mysql-query.php) para um array associativo com a função [mysql\_fetch\_assoc()](http://php.net/manual/en/function.mysql-fetch-assoc.php). Um array associativo, também chamado de dicionário, nada mais é do que uma [tabela hash](http://en.wikipedia.org/wiki/Hash_table) onde as chaves normalmente são strings. Com isso, tem-se a velocidade do acesso direto obtida em um array - _O(1)_ - mas, ao mesmo tempo, aliada à praticidade de não haver a necessidade de se utilizar índices numéricos, uma vez que var["chave"] é bem mais descritivo que var[0].

Segue o código devidamente comentado:

{% highlight java %}
import java.sql.*;
import java.util.*;
import javax.swing.*;

public class CRUD {
    private Connection conexao;
    private Statement comando;
    private ResultSet rs;

    public CRUD(String usuario, String senha, String banco) {
        // Criação da conexão, com o devido tratamento de exceção:
        try {
            Class.forName("com.mysql.jdbc.Driver");
            conexao = DriverManager.getConnection("jdbc:mysql://localhost:3306/" + banco, usuario, senha);
            comando = conexao.createStatement();
        }
        catch (SQLException ex) {
            JOptionPane.showMessageDialog(null, "Erro na conexão com o banco de dados: " + ex);
        }
    }

    public ArrayList<HashMap> consulta(String sql) {
        ArrayList<HashMap> busca = null;

        try {
            // A variável "rs" recebe o resultado da consulta e a variável "rsmd" armazena as informações a respeito da mesma, como os nomes das colunas:
            rs = comando.executeQuery(sql);
            ResultSetMetaData rsmd = rs.getMetaData();

            // Enquanto houverem linhas no resultado...
            while(rs.next()) {
                if (busca == null) {
                    // Inicializa a lista caso isto ainda não tenha sido feito:
                    busca = new ArrayList<HashMap>();
                }
                // O dicionário é criado a cada iteração do loop, de forma que os dados da linha anterior sejam descartados:
                HashMap<String, String> dado = new HashMap<String, String>();
                // Aqui obtém-se o nome das colunas, uma a uma. Vale destacar que a contagem começa em "1" e não em "0".
                for (int i = 1; i <= rsmd.getColumnCount(); i++) {
                    // A chave adicionada ao dicionário é o nome da coluna e seu valor é o conteúdo desta coluna na linha atual:
                    dado.put(rsmd.getColumnName(i), rs.getString(rsmd.getColumnName(i)));
                }
                // O dicionário é adicionado na lista:
                busca.add(dado);
            }
        }
        catch (SQLException ex) {
            JOptionPane.showMessageDialog(null, "Erro na consulta ao banco de dados: " + ex);
        }

        return busca;
    }
}
{% endhighlight %}

Muito mais cômodo do que ficar lidando com o ResultSet a todo momento, verificando se ainda há linhas no resultado com o método next(), etc. Com esta classe, simplesmente tem-se uma lista de dicionários. Caso a consulta não tenha gerado resultado algum, o valor retornado nada mais é do que "[null](https://en.wikipedia.org/wiki/Pointer_%28computer_programming%29#Null_pointer)". Simples e prático, não?

Referências:  
1. [Put ResultSet into HashMap?](http://stackoverflow.com/questions/8392942/put-resultset-into-hashmap)  
2. [Retrieve column names from java.sql.ResultSet](http://stackoverflow.com/questions/696782/retrieve-column-names-from-java-sql-resultset)
