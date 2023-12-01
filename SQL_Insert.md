# Inserir dados numa tabela

## Descrição
Este script SQL realiza a inserção de dados na tabela `FactSales` a partir dos dados da tabela `StageSales`. 

## Principais Comandos 

1. **INSERT INTO**: Este comando é usado para inserir dados em uma tabela.

2. **MAX()**: Retorna o maior valor de uma coluna. Neste caso, é utilizado para obter a chave primária correspondente com base em valores específicos nas tabelas de dimensão.

## Script Anotado

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
