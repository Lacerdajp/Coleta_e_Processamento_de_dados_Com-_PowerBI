# Coleta e Processamento de dados Com PowerBI

Criação de um dashboard corporativo, com processamento de dados integrado ao Banco de dados SQL Server na Azure

## Passo a Passo:

### Criação de uma instância na Azure para SQL Server

Foi criado um Banco de dados SQL Server na azure utilizando os Scripts disponibilizados no Repositorio. O banco é relacionado a gerencia de funcionarios, com informações, sobre o funcionario, seus dependentes, quais projetos alocados e etc. Teremos que Realizar o Processamento de Dados afim de criar um relatorio para análise.

### Integração do Power BI com SQL Server no Azure

Para isso inicialmente fizemos as integração do nosso banco com o PowerBI, inicialmente importamos diretamente todos as tabelas,sem realizar consultas.  Para isso utilizamos o metodo **IMPORT** pois assim  nós teremos mais flexibilidade do que com **DirectQuery**, e isso não seria um problema já que não estamos trabalhando com Big Data.

### Verificar problemas na base a fim de realizar a transformação dos dados

Não encontrei probelamas antes de Transformar.

### Verificar os cabeçalhos e tipos de dados

Em primeiro momento todos os cabeçalhos estava de forma correta, apenas o tipo  da coluna **Employee -> Salary** estava de forma erronea como inteira, então modifiquei para double contabel.

### Verifique a existência dos nulos e analise a remoção

Não foram encontrado valores nulos , a não ser por um valor na coluna **employee->  Super_ssn** que contém valor nulo. Isso ocorre pois o funcionario não tem ninguém como Gestor dele,sendo ele um gerente, para não ficar um buraco no DataSet utilizei o valor "11" para atribuir a pessoas que não tem ninguém acima dela.

### Separar colunas complexas

Separei o valor da coluna **employee -> Address** para  **Address.Number, Address.Street, Address.City, Adress.State**

### Mesclar consultas

Mesclei as consultas das tabelas **employee** e  **department**, achei necessario para agrupar as tabelas, para associar facilitar a associação de departamento ao usuario, removi todas as colunas de departament além do nome.

### Realizar a junção dos colaboradores e respectivos nomes dos gerentes .

Para isso achei mais facil utilizar a consulta SQL, alterando então a forma d eimportação inicial , a fim de associar quanto antes o Gerente ao funcionario.
utilizei a seguinte consulta SQL:
```sql
SELECT
e.\*,
Case WHEN e.Super_ssn is null then 'Sem Gerente' else e2.Fname end as GerenteFName,
Case WHEN e.Super_ssn is null then null else e2.Lname end as GerenteLName
FROM dbo.employee e
LEFT JOIN dbo.employee e2 on e2.Ssn = e.Super_ssn
```

### Mesclar as colunas de Nome e Sobrenome.

Afim de diminuir o numero de colunas mesclei as colunas de primeiro e segundo nome, tanto do Gerente quanto dos funcionarios.

### Mesclar departament e localização.

Meclei as tabelas de ** departament** e **dpt_locations** em "dp_locations" para aque no futuro a modelagem nao tenha relacionamento de muitos para muitos.Portante mesclei as tabelas e adicionei coluna com o nome 'Store' concatenando a localizaçãoe  o nome do departamento do, e exclui a coluna duplicada.
    Após fazer isso gerei um problema na mescla Employee e departament, portanto eliminei as duplicatas de employee.

### Porque mescla e nao atribuição?

Pois a mescla são semelhante a Joins enquanto a atribuição é semelhante a union, isto é:
    --Mescla combina tabelas com base em chaves comuns
    --Atribuir adiciona registros de uma tabela à outra, empilhando os dados

### FInalizando processamento

Não achei necessario excluir nenhuma coluna.

### Complicações na modelagem

Ao salvar me deparei com um problema relacionado a modelagem dos dados pelo fato de Dnumber de departament não ser mais unico por conta da tabela. Portanto exclui as ligações que tinha com dep_local, pois não seria mais necessaria essa tabela. Depois exclui todas as realações com  departament, devido ao Dnum nao ser mais unico, sendoa ssim criei um token concatenando a cidade e ao  Dnum, tanto  em project quanto em departamente, e assim relacionei esses tokens da forma correta. Não vi necessidade mais de associar departament e employee, por isso exclui a ligação entre eles.

### Dashboard de Analise de Funcionarios

O dashboard foi criado com o objetivo de análisar os funcionários.
