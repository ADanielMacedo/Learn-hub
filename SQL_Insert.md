# Inserir dados numa tabela

## Descrição
Este script SQL insere dados na tabela `FactSales` a partir dos dados da tabela `StageSales`. 

## Principais Comandos 

1. **INSERT INTO**: Este comando é usado para inserir dados em uma tabela.

2. **MAX()**: Retorna o maior valor de uma coluna. Neste caso, é utilizado para obter a chave primária correspondente com base em valores específicos nas tabelas de dimensão.

## Script Anotado
[Referência](https://learn.microsoft.com/en-us/training/modules/load-optimize-data-into-relational-data-warehouse/6-load-fact-tables){:target="_blank"}

```sql


INSERT INTO dbo.FactSales
SELECT
    
    (SELECT MAX(DateKey)
     FROM dbo.DimDate
     WHERE FullDateAlternateKey = stg.OrderDate) AS OrderDateKey,
        
    (SELECT MAX(CustomerKey)
     FROM dbo.DimCustomer
     WHERE CustomerAlternateKey = stg.CustNo) AS CustomerKey,
        
    (SELECT MAX(ProductKey)
     FROM dbo.DimProduct
     WHERE ProductAlternateKey = stg.ProductID) AS ProductKey,
        
    (SELECT MAX(StoreKey)
     FROM dbo.DimStore
     WHERE StoreAlternateKey = stg.StoreID) AS StoreKey,
        
    OrderNumber,
    OrderLineItem,
    OrderQuantity,
    UnitPrice,
    Discount,
    Tax,
    SalesAmount
FROM dbo.StageSales AS stg;

```

# Combine INSERT and UPDATE statements to load a slowly changing dimension table

## Descrição
The DimCustomer table supports type 1 and type 2 slowly changing dimensions (SCDs), where type 1 changes result in an in-place update to an existing row, and type 2 changes result in a new row to indicate the latest version of a particular dimension entity instance. Loading this table requires a combination of INSERT statements (to load new customers) and UPDATE statements (to apply type 1 or type 2 changes).

In the query pane, replace the existing SQL code with the following code:

```sql
 INSERT INTO dbo.DimCustomer ([GeographyKey],[CustomerAlternateKey],[Title],[FirstName],[MiddleName],[LastName],[NameStyle],[BirthDate],[MaritalStatus],
 [Suffix],[Gender],[EmailAddress],[YearlyIncome],[TotalChildren],[NumberChildrenAtHome],[EnglishEducation],[SpanishEducation],[FrenchEducation],
 [EnglishOccupation],[SpanishOccupation],[FrenchOccupation],[HouseOwnerFlag],[NumberCarsOwned],[AddressLine1],[AddressLine2],[Phone],
 [DateFirstPurchase],[CommuteDistance])
 SELECT *
 FROM dbo.StageCustomer AS stg
 WHERE NOT EXISTS
     (SELECT * FROM dbo.DimCustomer AS dim
     WHERE dim.CustomerAlternateKey = stg.CustomerAlternateKey);

 -- Type 1 updates (change name, email, or phone in place)
 UPDATE dbo.DimCustomer
 SET LastName = stg.LastName,
     EmailAddress = stg.EmailAddress,
     Phone = stg.Phone
 FROM DimCustomer dim inner join StageCustomer stg
 ON dim.CustomerAlternateKey = stg.CustomerAlternateKey
 WHERE dim.LastName <> stg.LastName OR dim.EmailAddress <> stg.EmailAddress OR dim.Phone <> stg.Phone

 -- Type 2 updates (address changes triggers new entry)
 INSERT INTO dbo.DimCustomer
 SELECT stg.GeographyKey,stg.CustomerAlternateKey,stg.Title,stg.FirstName,stg.MiddleName,stg.LastName,stg.NameStyle,stg.BirthDate,stg.MaritalStatus,
 stg.Suffix,stg.Gender,stg.EmailAddress,stg.YearlyIncome,stg.TotalChildren,stg.NumberChildrenAtHome,stg.EnglishEducation,stg.SpanishEducation,stg.FrenchEducation,
 stg.EnglishOccupation,stg.SpanishOccupation,stg.FrenchOccupation,stg.HouseOwnerFlag,stg.NumberCarsOwned,stg.AddressLine1,stg.AddressLine2,stg.Phone,
 stg.DateFirstPurchase,stg.CommuteDistance
 FROM dbo.StageCustomer AS stg
 JOIN dbo.DimCustomer AS dim
 ON stg.CustomerAlternateKey = dim.CustomerAlternateKey
 AND stg.AddressLine1 <> dim.AddressLine1;
```
[Referência](https://microsoftlearning.github.io/dp-203-azure-data-engineer/Instructions/Labs/09-Load-Data-into-Data-Warehouse.html){:target="_blank"}

