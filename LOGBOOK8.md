# LOGBOOK5 - SQL Injection
## Task 1 - Get Familiar with SQL Statements
<div <div align="justify">
<p>
Para esta tarefa é nos pedido que imprimamos as informações da tabela users, especificamente as informações da Alice. Para tal, basta executar o seguinte comando:
</p>

```sql
SELECT * FROM credential WHERE Name ='Alice';
```
![](uploads/logbook8P1.png)

![](uploads/logbook8P2.png)

## Task 2: SQL Injection Attack on SELECT Statement
### Task 2.1 - SQL Injection Attack from webpage
<p>
Nesta tarefa é nos pedido que entremos no sistema como um utilizador Admin. Considerando que sabemos que o Username de um Admin é 'Admin', basta completar as credenciais da seguinte forma:
</p>

![](uploads/logbook8P3.png)

<p>
Neste caso, só preenchemos o campo da password, pois surge um alerta caso este campo esteja vazio. Portanto preenchemos este com qualquer tipo de caracter. Além disso, percebe-se que se utilizou o caracter '#' após 'Admin' para que tudo o que venha aós 'Admin' seja comentado, o que seria a password neste caso. Assim, é possível entrar no sistema como Admin.
</p>

![](uploads/logbook8P4.png)

### Task 2.2 - SQL Injection Attack from command line
<p>
Como no guião nos dizia para utilizar o comando 'curl' para fazer o pedido e que certos caracteres especiais, como o 'single quote', o espaço e o '#' devem ser codificados, então utilizamos o seguinte comando:
</p>

```bash
curl 'www.seed-server.com/unsafe_home.php?username=Admin%27%23&Password=randomcharacters'
```

<p>
Como sabiamos que bastava inserir as mesmas credenciais utilizadas na task anterior, então bastava inserir essas mas na linha de comando. Assim, de forma a colocarmos que o username = Admin'#, bastava saber que o 'single quote' é codificado como %27 e o '#' como %23 e colocar uma password qualquer. Desta forma, ao executar o comando, conseguimos entrar no sistema como Admin: 
</p>

![](uploads/logbook8P5.png)

![](uploads/logbook8P6.png)

<p> Como se pode observar, o resultado é o mesmo que o obtido na task anterior, mas ao executar o comando na linha decomando as informaçoes aparecem em syntax tabular de HTML </p>

### Task 2.3 - Append a new SQL statement

</div>