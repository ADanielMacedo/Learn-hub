# Inserção de Dados na Tabela FactSales

## Descrição
Este script SQL realiza a inserção de dados na tabela `FactSales` a partir dos dados da tabela `StageSales`. Ele mapeia chaves estrangeiras para chaves primárias de tabelas de dimensão correspondentes.

## Principais Comandos Utilizados

1. **INSERT INTO**: Este comando é usado para inserir dados em uma tabela.

2. **SELECT**: É usado para recuperar dados de uma ou mais tabelas.

3. **MAX()**: Retorna o maior valor de uma coluna. Neste caso, é utilizado para obter a chave primária correspondente com base em valores específicos nas tabelas de dimensão.

4. **FROM**: Especifica a tabela da qual os dados devem ser recuperados.

## Script Anotado

```sql
-- Inserção de Dados na Tabela FactSales

INSERT INTO dbo.FactSales
SELECT
    -- Mapeamento da chave de data
    (SELECT MAX(DateKey)
     FROM dbo.DimDate
     WHERE FullDateAlternateKey = stg.OrderDate) AS OrderDateKey,
    
    -- Mapeamento da chave do cliente
    (SELECT MAX(CustomerKey)
     FROM dbo.DimCustomer
     WHERE CustomerAlternateKey = stg.CustNo) AS CustomerKey,
    
    -- Mapeamento da chave do produto
    (SELECT MAX(ProductKey)
     FROM dbo.DimProduct
     WHERE ProductAlternateKey = stg.ProductID) AS ProductKey,
    
    -- Mapeamento da chave da loja
    (SELECT MAX(StoreKey)
     FROM dbo.DimStore
     WHERE StoreAlternateKey = stg.StoreID) AS StoreKey,
    
    -- Demais campos
    OrderNumber,
    OrderLineItem,
    OrderQuantity,
    UnitPrice,
    Discount,
    Tax,
    SalesAmount
FROM dbo.StageSales AS stg;
