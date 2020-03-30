---
title: Migrera 1:N relationsdata till Azure Cosmos DB SQL API
description: Lär dig hur du hanterar komplex datamigrering för 1:äd få relationer till SQL API
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 467e9627a2623779bd808ca5aebdf76d8a5eda42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896641"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Migrera 1:n relationsdata till Azure Cosmos DB SQL API-konto

För att migrera från en relationsdatabas till Azure Cosmos DB SQL API kan det vara nödvändigt att göra ändringar i datamodellen för optimering.

En vanlig omvandling är att denormalisera data genom att bädda in relaterade underobjekt i ett JSON-dokument. Här tittar vi på några alternativ för detta med hjälp av Azure Data Factory eller Azure Databricks. Allmän vägledning om datamodellering för Cosmos DB finns i [Datamodellering i Azure Cosmos DB](modeling-data.md).  

## <a name="example-scenario"></a>Exempel på ett scenario

Anta att vi har följande två tabeller i vår SQL-databas, order och OrderDetails.


![Beställ information](./media/migrate-relational-to-cosmos-sql-api/orders.png)

Vi vill kombinera detta 1-till-fåtal relation till ett JSON-dokument under migreringen. För att göra detta kan vi skapa en T-SQL-fråga med "FOR JSON" enligt nedan:

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

Resultatet av den här frågan skulle se ut nedan: 

![Beställ information](./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png#lightbox)


Helst vill du använda en enda Azure Data Factory (ADF) kopieringsaktivitet för att fråga SQL-data som källa och skriva utdata direkt till Azure Cosmos DB-diskho som riktiga JSON-objekt. För närvarande är det inte möjligt att utföra den nödvändiga JSON-omvandlingen i en kopieringsaktivitet. Om vi försöker kopiera resultatet av ovanstående fråga till en Azure Cosmos DB SQL API-behållare ser vi fältet OrderDetails som en strängegenskap för vårt dokument, i stället för den förväntade JSON-matrisen.

Vi kan komma runt den här aktuella begränsningen på något av följande sätt:

* **Använd Azure Data Factory med två kopieringsaktiviteter:** 
  1. Hämta JSON-formaterade data från SQL till en textfil på en mellanliggande blob-lagringsplats och 
  2. Läs in data från JSON-textfilen till en behållare i Azure Cosmos DB.

* **Använd Azure Databricks för att läsa från SQL och skriva till Azure Cosmos DB** - vi kommer att presentera två alternativ här.


Låt oss titta närmare på dessa metoder:

## <a name="azure-data-factory"></a>Azure Data Factory

Även om vi inte kan bädda in OrderDetails som en JSON-array i målet Cosmos DB-dokument, kan vi undvika problemet med hjälp av två separata copy-aktiviteter.

### <a name="copy-activity-1-sqljsontoblobtext"></a>Kopiera aktivitet #1: SqlJsonToBlobText

För källdata använder vi en SQL-fråga för att få resultatuppsättningen som en enda kolumn med ett JSON-objekt (som representerar ordern) per rad med hjälp av SQL Server OPENJSON och FOR JSON PATH-funktionerna:

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

![ADF-kopia](./media/migrate-relational-to-cosmos-sql-api/adf1.png)


För diskhon för sqljsonToBlobText-kopieringsaktiviteten väljer vi "Avgränsad text" och pekar den på en specifik mapp i Azure@concatBlob Storage med ett dynamiskt genererat unikt filnamn (till exempel ' (pipeline(). RunId,'.json').
Eftersom vår textfil egentligen inte är "avgränsad" och vi inte vill att den ska tolkas i separata kolumner med kommatecken och vill bevara dubbelcitat ("), ställer vi in "Kolumndelimiter" till en flik ("\t") - eller ett annat tecken som inte förekommer i data - och "Citattecken" till "Inget citattecken".

![ADF-kopia](./media/migrate-relational-to-cosmos-sql-api/adf2.png)

### <a name="copy-activity-2-blobjsontocosmos"></a>Kopiera aktivitet #2: BlobJsonToCosmos

Därefter ändrar vi vår ADF-pipeline genom att lägga till den andra kopieringsaktiviteten som letar i Azure Blob Storage för textfilen som skapades av den första aktiviteten. Den bearbetar det som "JSON" källa för att infoga till Cosmos DB sjunka som ett dokument per JSON-rad finns i textfilen.

![ADF-kopia](./media/migrate-relational-to-cosmos-sql-api/adf3.png)

Alternativt lägger vi också till en "Ta bort" aktivitet i pipelinen så att alla tidigare filer som finns kvar i mappen /Orders/ före varje körning tas bort. Vår ADF pipeline ser nu ut ungefär så här:

![ADF-kopia](./media/migrate-relational-to-cosmos-sql-api/adf4.png)

När vi har utlöst pipelinen ovan ser vi en fil som skapats på vår mellanliggande Azure Blob Storage-plats som innehåller ett JSON-objekt per rad:

![ADF-kopia](./media/migrate-relational-to-cosmos-sql-api/adf5.png)

Vi ser också Order dokument med korrekt inbäddade OrderDetails införas i vår Cosmos DB samling:

![ADF-kopia](./media/migrate-relational-to-cosmos-sql-api/adf6.png)


## <a name="azure-databricks"></a>Azure Databricks

Vi kan också använda Spark i [Azure Databricks](https://azure.microsoft.com/services/databricks/) för att kopiera data från vår SQL-databaskälla till Azure Cosmos DB-målet utan att skapa mellanliggande text/JSON-filer i Azure Blob Storage. 

> [!NOTE]
> För tydlighet och enkelhet innehåller kodavsnitten nedan dummy databaslösenord uttryckligen infogade, men du bör alltid använda Azure Databricks hemligheter.
>

Först skapar och bifogar vi de [nödvändiga SQL-anslutnings-](https://docs.databricks.com/data/data-sources/sql-databases-azure.html) och [Azure Cosmos DB-anslutningsbiblioteken](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) till vårt Azure Databricks-kluster. Starta om klustret för att se till att biblioteken läses in.

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks1.png)

Därefter presenterar vi två prover, för Scala och Python. 

### <a name="scala"></a>Scala
Här får vi resultaten av SQL-frågan med "FOR JSON" utgång i en DataFrame:

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks2.png)

Därefter ansluter vi till vår Cosmos DB-databas och samling:

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

Slutligen definierar vi vårt schema och använder from_json för att tillämpa DataFrame innan vi sparar den i CosmosDB-samlingen.

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks3.png)


### <a name="python"></a>Python

Som en alternativ metod kan du behöva köra JSON-omvandlingar i Spark (om källdatabasen inte stöder "FOR JSON" eller en liknande åtgärd), eller så kanske du vill använda parallella åtgärder för en mycket stor datauppsättning. Här presenterar vi ett PySpark-prov. Börja med att konfigurera käll- och måldatabasanslutningarna i den första cellen:

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

Sedan kommer vi att fråga källdatabasen (i det här fallet SQL Server) för både order- och orderdetaljposter, vilket placerar resultaten i Spark-dataramar. Vi kommer också att skapa en lista som innehåller alla order-ID: er och en trådpool för parallella operationer:

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

Skapa sedan en funktion för att skriva order i mål-SQL API-samlingen. Den här funktionen filtrerar alla orderdetaljer för det angivna order-ID:t, konverterar dem till en JSON-matris och infogar matrisen i ett JSON-dokument som vi skriver i målet SQL API Collection för den ordningen:

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

Slutligen kommer vi att anropa ovanstående med hjälp av en kartfunktion på trådpoolen, för att köra parallellt, passerar i listan över order-ID:er som vi skapade tidigare:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
I båda tillvägagångssätten, i slutet, bör vi få korrekt sparade inbäddade OrderDetails inom varje Order dokument i Cosmos DB samling:

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks4.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [datamodellering i Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/modeling-data)
* Lär dig hur du [modellerar och partitionerar data på Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)
