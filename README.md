# Estudo com Python

Criado o arquivo analise.ipynb, extensão do jupyter notebook, para carregar os arquivos em csv e estudá-los.

O arquivo pode ser aberto no https://colab.research.google.com/ caso não o Jupyter Notebook não esteja instalado em sua máquina.

## Estudos básicos em cada 1 dos arquivos .csv

### Efetuando carga dos arquivos csv num data frame da biblioteca Pandas
Verificando colunas, campos interessantes e estatisticas prontas:
![image](https://user-images.githubusercontent.com/11432754/112760029-38a90c00-8ffe-11eb-8533-75d19a243623.png)
#### 
#### 
![image](https://user-images.githubusercontent.com/11432754/112760040-4199dd80-8ffe-11eb-8b81-3f9fa23433ae.png)

*O mesmo estudo foi feito com todos os arquivos de dados

# Armazenamento e Ingestão dos dados
## Armazenamento: Bucket "ROX" - Amazon S3

Após estudo prévio, os arquivos .csv foram adicionados à um bucket do Amazon S3 denomidado "ROX", conforme imagem abaixo:

![image](https://user-images.githubusercontent.com/11432754/112759672-c3890700-8ffc-11eb-8408-e535bc7ae466.png)

Ao abrir o bucket "ROX", seu detalhe é mostrado a seguir:

![image](https://user-images.githubusercontent.com/11432754/112759718-f4693c00-8ffc-11eb-950c-d10cb3fc5917.png)

### ARN do bucket "ROX"
  arn:aws:s3:::rox
 
## Resultado de queries: Bucket "ATHENARES" - Amazon S3
Para armazenamento dos resultados de query do Athena, foi criado o bucket "ATHENARES".
![image](https://user-images.githubusercontent.com/11432754/112760370-65a9ee80-8fff-11eb-9fbe-c123660e1e0c.png)

# Ingestão dos dados
## AWS Glue
Para ingestão dos dados, foi criado o crawler mainCrawler com o AWS Athena. 

O crawler foi setado para ser executado manualmente e criar o catálogo das tabelas e seus registros em um banco de dados:
![image](https://user-images.githubusercontent.com/11432754/112760517-e9fc7180-8fff-11eb-9963-e4fdc7a39458.png)

## Banco de Dados
Após a execução do crawler, as tabelas são criadas no banco de dados "ROXBIKES"

![image](https://user-images.githubusercontent.com/11432754/112760606-4cee0880-9000-11eb-9a17-6f0e8d75177c.png)

## Tabelas para consulta
Conforme mencionado anteriormente, as tabelas foram criadas no banco "ROXBIKES" cujo nome são o próprio nome do arquivo .CSV:
![image](https://user-images.githubusercontent.com/11432754/112760650-7870f300-9000-11eb-8d60-1c93119b72ec.png)

# Utilizando o AWS Athena para consultas SQL
## Exercícios
1.	Escreva uma query que retorna a quantidade de linhas na tabela Sales.SalesOrderDetail pelo campo SalesOrderID, desde que tenham pelo menos três linhas de detalhes.

Query utilizada:
select SalesOrderID, count(1) as qd from salesorderdetail group by SalesOrderID having count(1) >= 3 order by 2 asc;

Resultado:
![image](https://user-images.githubusercontent.com/11432754/112760836-09e06500-9001-11eb-8ce1-3371929a9a26.png)

2.	Escreva uma query que ligue as tabelas Sales.SalesOrderDetail, Sales.SpecialOfferProduct e Production.Product e retorne os 3 produtos (Name) mais vendidos (pela soma de OrderQty), agrupados pelo número de dias para manufatura (DaysToManufacture).

Query utilizada:
select a.name, a.DaysToManufacture, sum(c.orderqty) Soma from productionproduct a inner join specialofferproduct b on b.productId = a.productId inner join salesorderdetail c on c.productId = a.productId group by a.name, a.DaysToManufacture order by 3 desc;

Resultado: 
![image](https://user-images.githubusercontent.com/11432754/112760885-3f854e00-9001-11eb-89d7-85fc30670fcf.png)

3.	Escreva uma query ligando as tabelas Person.Person, Sales.Customer e Sales.SalesOrderHeader de forma a obter uma lista de nomes de clientes e uma contagem de pedidos efetuados.

Query utilizada:
select c.firstname, c.middlename, c.lastname, count(a.salesorderid) from salesorderheader a inner join salescustomer b on b.customerid = a.customerid left join person c on c.businessentityid = a.salespersonid where c.businessentityid is not null group by c.firstname, c.middlename, c.lastname order by 4 desc;

Resultado:
![image](https://user-images.githubusercontent.com/11432754/112760935-6e9bbf80-9001-11eb-9fc1-0a3579a00647.png)

4.	Escreva uma query usando as tabelas Sales.SalesOrderHeader, Sales.SalesOrderDetail e Production.Product, de forma a obter a soma total de produtos (OrderQty) por ProductID e OrderDate.

Query utilizada:
select b.productID, a.orderdate, sum(b.orderqty) SOMA from salesorderheader a inner join salesorderdetail b on b.salesorderid = a.salesorderid inner join productionproduct c on c.productid = b.productid group by b.productID, a.orderdate order by 3 desc;

Resultado:
![image](https://user-images.githubusercontent.com/11432754/112760969-925f0580-9001-11eb-81e4-c56f596cd547.png)

5.	Escreva uma query mostrando os campos SalesOrderID, OrderDate e TotalDue da tabela Sales.SalesOrderHeader. Obtenha apenas as linhas onde a ordem tenha sido feita durante o mês de setembro/2011 e o total devido esteja acima de 1.000. Ordene pelo total devido decrescente.

Query utilizada:
select salesorderid, orderdate, totaldue from salesorderheader where orderdate between '2011-09-01' and '2011-09-30' and cast(replace(totaldue,',','.') as decimal(20,6)) > 1000 order by totaldue desc;

Resultado:
![image](https://user-images.githubusercontent.com/11432754/112761007-bde1f000-9001-11eb-9d13-6d43015a1585.png)
