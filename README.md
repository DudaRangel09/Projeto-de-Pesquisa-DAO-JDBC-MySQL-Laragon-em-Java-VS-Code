# Projeto de pesquisa – DAO + JDBC + MySQL em Java/ VS Code 

 

## Fase 1 – Alinhamento e pesquisa 

## Explicação do padrão DAO (com exemplos) 

 

### O que é DAO? 

O padrão de projeto DAO (Data Access Object) surgiu da necessidade de separar a lógica de negócio da lógica de persistência de dados. Ele centraliza e encapsula todas as operações de acesso ao banco, evitando que a camada de negócio manipule diretamente os comandos SQL e garantindo maior organização e clareza estrutural. 

As classes DAO são responsáveis por realizar operações CRUD e consultas ao SGBD, convertendo dados do banco em objetos ou listas de objetos, bem como transformando objetos em instruções SQL. Toda interação com a base de dados ocorre exclusivamente por meio dessas classes, preservando a integridade da arquitetura. 

Quando aplicado corretamente, o padrão DAO abstrai o processo de armazenamento e recuperação de informações, facilitando manutenção, permitindo migrações de banco de dados e promovendo um design orientado a objetos mais consistentes. 

### O que é JDBC? 

JDBC (Java Database Connectivity) é uma API da linguagem Java que permite fazer conexão e comunicação com bancos de dados relacionais. Ela foi introduzida pela Sun Microsystems para que aplicações Java pudessem executar operações SQL (como consultar, inserir, atualizar e excluir dados) sem que a lógica da aplicação precise lidar diretamente com os detalhes do banco de dados. O JDBC funciona por meio de drives específicos do banco, que implementam as interfaces dessa API e permitem que sua aplicação se conecte e troque dados com qualquer banco que suporte a um driver JDBC. 

 

### Como funciona a conexão? 

O Java conecta-se a bancos de dados através da API JDBC utilizando um driver apropriado para o SGBD. A conexão é estabelecida pelo método “DriverManager.getConnection()”, que recebe a URL JDBC, usuário e senha, retornando um objeto “Connection”. A partir dessa conexão, a aplicação cria “Statement” ou “PreparedStatement” para enviar comandos SQL ao banco e, quando necessário, recebe resultados por meio de “ResultSet”. Após o uso, todos os recursos devem ser fechados, preferencialmente com try-with-resources, garantindo segurança e eficiência no aceso aos dados. 

 

### O que é PreparedStatement? 

É uma interface do JDBC usada para executar comandos SQL parametrizados. Ele permite definir valores por meio de marcadores “?”, oferecendo mais segurança contra SQL Injection, melhor desempenho e uma estrutura mais clara e organizada para operações de banco de dados. 

 

### Por que evitar SQL Injection? 

O SQL Injection é uma técnica de ataque em que um invasor insere ou altera os comandos SQL dentro de entradas fornecidas ao sistema (como campos de formulários). Quando a aplicação constrói a query de forma insegura, o banco de dados interpreta esses comandos maliciosos como parte legítima da instrução SQL. Isso permite que o atacante acesse, manipule ou exclua dados, mesmo sem autorização. 

Evitar o SQL Injection é importante porque essa vulnerabilidade pode permitir o acesso não autorizado a informações confidenciais, a alteração ou exclusão de dados, obtenção de privilégios administrativos, prejuízo financeiro e perda de confiança na empresa. Esses riscos existem porque o banco de dados executa cegamente os comandos SQL recebidos da aplicação quando eles não são filtrados ou parametrizados. 


### Como fechar recursos com try-with-resources? 

O try-with-resources permite que objetos que implementam a interface “AutoCloseable” sejam fechados automaticamente ao final do bloco “try”, independentemente de ocorrerem exceções. No contexto de JDBC, isso inclui “Connection, PreparedStatemente e ResultSet”. 

Ao declarar esses recursos entre parênteses no “try”, o Java garante que todos serão fechados de forma segura e ordenada ao término da execução do bloco. Esse recurso evita vazamento de conexões, substitui a necessidade de blocos “Finally” extensos e promove um código mais limpo e confiável. 

 

### Por que encapsular SQLException? 

Porque ele consiste em converter essa execução específica do JDBC em uma execução própria da aplicação, geralmente uma RuntimeException, como “DbException”. Esse procedimento é adorado para tornar o tratamento de erros mais organizado e adequado à arquitetura. 

 

### Qual é o papel de cada camada (model, dao, dao.impl, db, app)? 

Cada camada tem uma função específica que garante a organização, separação de responsabilidades e facilidade de manutenção. A camada model concentra as classes que representam as entidades do domínio, funcionando como a estrutura de dados da aplicação. São classes que refletem as tabelas do banco e contêm apenas atributos, construtores e métodos de acesso. 

O dao reúne as interfaces que definem os contatos de acesso a dados. Nelas ficam apenas as assinaturas dos métodos que serão usados pela aplicação, como inserção, atualização, remoção e consultas, sem expor qualquer detalhe de implementação. 

O dao.impl contém as implementações concretas dessas interfaces, utilizando JDBC. É nessa camada que são escritas as instruções SQL, criados e executados os “PreparedStatement”, realizados os mapeamentos de “ResultSet” para objetos e tratado o encapsulamento de exceções relacionadas ao banco de dados. 

A camada db centraliza tudo que diz respeito à conexão com o banco. Ela inclui classes como “ConnectionFactory”, que cria e gerencia conexões, a exceção personalizada “DbException” e o arquivo de configuração “db.properties”, que define parâmetros como URL, usuário e senha. 

O app contém a lógica de execução da aplicação, como menus e rotinas de interação com o usuário. Ela coordena o fluxo da aplicação, chama os métodos dos DAOs e apresenta os resultados, lidar diretamente com SQL ou com detalhes internos da persistência. 




# Mapa conceitual



## 1. DAO (Data Access Object)
* Separa lógica de negócio da lógica de persistência.
* Centraliza operações de acesso ao banco (CRUD e consultas).
* Garante baixo acoplamento e facilidade de manutenção.

## 2. Arquitetura em Camadas
### 2.1 model
* Representa as entidades do domínio.
* Cada classe reflete uma tabela do banco.
* Armazena atributos e métodos de acesso.

### 2.2 dao
* Contém as interfaces que definem contratos de acesso a dados.
* Declara métodos como insert, update, delete, findById, findAll.
* Não implementa lógica, apenas define o que deve ser feito.

### 2.3 dao.impl
* Implementa as interfaces DAO usando JDBC.
* Contém SQL, PreparedStatement, ResultSet e mapeamento para objetos.
* Realiza o tratamento e encapsulamento de exceções.

### 2.4 db
* Centraliza a infraestrutura de conexão com o banco.
* Inclui ConnectionFactory, DbException e db.properties.
* Fornece conexões configuradas e padronizadas.

### 2.5 app
* Contém o fluxo principal da aplicação.
* Executa menus, navegação e interação do usuário.
* Usa os DAOs sem lidar com SQL.

## 3. JDBC (Java Database Connectivity)
* API usada para conectar aplicações Java a bancos relacionais.
* Usa drivers específicos para cada SGBD.
* Permite executar SQL e manipular resultados.

## 4. Funcionamento da Conexão JDBC
* Criação da conexão via DriverManager.getConnection().
* Requer URL JDBC, usuário e senha.
* Gera objetos para executar SQL: Statement e PreparedStatement.
* Retorna dados através de ResultSet.
* Recursos devem ser fechados com try-with-resources.

## 5. PreparedStatement
* Instrui SQL com parâmetros usando ?.
* Previne SQL Injection.
* Melhora desempenho com pré-compilação.
* Organiza o código de persistência.

## 6. SQL Injection
* Ataque que injeta SQL malicioso em entradas inseguras.
* Pode permitir acesso, alteração ou exclusão de dados.
* É prevenido com PreparedStatement.

## 7. Mapeamento de ResultSet
* O resultado do banco vem em forma de ResultSet.
* Cada linha é convertida em objetos do model.
* Processo conhecido como object mapping.

## 8. Exceções (SQLException → DbException)
* SQLException representa erros do JDBC.
* Encapsular em DbException padroniza o tratamento.
* Evita propagação de exceções checadas para outras camadas.

## 9. Transações (conceito)
* Agrupam múltiplas operações em uma única unidade lógica.
* Usam: setAutoCommit(false), commit(), rollback().
* Garantem consistência de dados.

# Fontes 

https://www.dio.me/articles/o-que-e-dao-ba9c73921265 

https://www.devmedia.com.br/dao-pattern-persistencia-de-dados-utilizando-o-padrao-dao/30999 

https://www.devmedia.com.br/jdbc-tutorial/6638 

https://www.kaspersky.com.br/resource-center/definitions/sql-injection 

https://www.locaweb.com.br/blog/temas/codigo-aberto/o-que-e-encapsulamento/#:~:text=O%20encapsulamento%20permite%20que%20os,com%20os%20detalhes%20de%20implementa%C3%A7%C3%A3o. 

https://www.devmedia.com.br/padrao-mvc-java-magazine/21995 

 

 

 

 

 

 

 

 

 
