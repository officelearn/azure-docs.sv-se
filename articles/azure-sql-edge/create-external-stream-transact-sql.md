---
title: Skapa extern ström (Transact-SQL) – Azure SQL Edge (för hands version)
description: Lär dig mer om CREATE EXTERNAL STREAM-instruktionen i Azure SQL Edge (för hands version)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: d4ad446d43c90eee1c48ee2ba94585242805fa7d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597935"
---
# <a name="create-external-stream-transact-sql"></a>Skapa extern ström (Transact-SQL)

Det externa STREAM-objektet har ett dubbelt syfte av både indata och utdata. Det kan användas som indata för att fråga strömmande data från händelse inmatnings tjänster som Azure-evenemang eller IoT-hubbar eller används som utdata för att ange var och hur resultaten ska lagras från en strömmande fråga.

En extern data ström kan också anges och skapas som både utdata och indata för tjänster som Event Hub eller Blob Storage. Detta är för länknings scenarier där en strömmande fråga behåller resultat till den externa data strömmen som utdata och en annan strömmande fråga som läser från samma externa ström som indata. 


## <a name="syntax"></a>Syntax

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
(column definition [,column definitions ] * ) --Used for input - optional 
WITH  
(  
  DATA_SOURCE = <data_source_name>, 
  LOCATION = <location_name>, 
  EXTERNAL_FILE_FORMAT = <external_file_format_name>, --Used for input - optional 
   
  INPUT_OPTIONS =  
    ‘CONSUMER_GROUP=<consumer_group_name>; 
    ‘TIME_POLICY=<time_policy>; 
    LATE_EVENT_TOLERANCE=<late_event_tolerance_value>; 
    OUT_OF_ORDER_EVENT_TOLERANCE=<out_of_order_tolerance_value> 
     
    /* Edge options */ 
  , 
  OUTPUT_OPTIONS =  
    ‘REJECT_POLICY=<reject_policy>; 
    MINIMUM_ROWS=<row_value>; 
    MAXIMUM_TIME=<time_value_minutes>; 
    PARTITION_KEY_COLUMN=<partition_key_column_name>; 
    PROPERTY_COLUMNS=(); 
    SYSTEM_PROPERTY_COLUMNS=(); 
    PARTITION_KEY=<partition_key_name>; 
    ROW_KEY=<row_key_name>; 
    BATCH_SIZE=<batch_size_value>; 
    MAXIMUM_BATCH_COUNT=<batch_value>;  
    STAGING_AREA=<blob_data_source>’ 
     
    /* Edge options */ TAGS=<tag_column_value> 
); 
```


## <a name="arguments"></a>Argument


- [EXTERN DATA KÄLLA](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [EXTERNT FIL FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **Plats**: anger namnet på faktiska data eller plats i data källan. Om det rör sig om ett Edge Hub-eller Kafka Stream-objekt anger platsen namnet på det Edge Hub-eller Kafka-ämne som ska läsas från eller skrivas till.
- **INPUT_OPTIONS**: Ange alternativ som nyckel/värde-par för tjänster som händelse-och IoT-hubbar som är indata till strömnings frågor
    - CONSUMER_GROUP: händelse-och IoT-hubbar begränsar antalet läsare i en konsument grupp (till 5). Om du lämnar det här fältet tomt används konsument gruppen $Default.
      - Gäller händelse-och IOT-hubbar
    - TIME_POLICY: beskriver om du vill släppa händelser eller justera händelse tiden när sena händelser eller händelser som inte är i ordning överför sina tolerans värden.
      - Gäller händelse-och IOT-hubbar
    - LATE_EVENT_TOLERANCE: den maximala acceptabla tids fördröjningen. Fördröjningen representerar skillnaden mellan händelsens tidsstämpel och system klockan.
      - Gäller händelse-och IOT-hubbar
    - OUT_OF_ORDER_EVENT_TOLERANCE: händelser kan tas ur ordning efter att de har gjort resan från indata till strömnings frågan. Dessa händelser kan godkännas som de är, eller så kan du välja att pausa under en angiven period för att ändra ordning på dem.
      - Gäller händelse-och IOT-hubbar
- **OUTPUT_OPTIONS**: Ange alternativ som nyckel/värde-par för tjänster som stöds och som är utdata till strömmande frågor 
  - REJECT_POLICY: DROP | FÖRSÖK igen med de data fel som hanterar principer när data konverterings fel inträffar. 
    - Gäller alla utdata som stöds 
  - MINIMUM_ROWS:  
    Minsta antal rader som krävs per batch som skrivs till utdata. För Parquet skapar varje batch en ny fil. 
    - Gäller alla utdata som stöds 
  - MAXIMUM_TIME:  
    Maximal vänte tid per batch. Efter den här tiden kommer batchen att skrivas till utdata även om minimi kravet på rader är uppfyllt. 
    - Gäller alla utdata som stöds 
  - PARTITION_KEY_COLUMN:  
    Den kolumn som används för partitionsnyckel. 
    - Gäller Event Hub och Service Bus avsnittet 
  - PROPERTY_COLUMNS:  
    En kommaavgränsad lista över namnen på utdatakolumner som ska bifogas till meddelanden som anpassade egenskaper om de anges.  
    - Gäller Event Hub och Service Bus ämne och kö 
  - SYSTEM_PROPERTY_COLUMNS:  
    En JSON-formaterad samling med namn/värde-par av namn-/värdepar för system egenskaps namn och utdatakolumner som ska fyllas i Service Bus meddelanden. t. ex. {"MessageId": "column1", "PartitionKey": "column2"} 
    - Gäller för Service Bus ämne och kö 
  - PARTITION_KEY:  
    Namnet på kolumnen utdata som innehåller partitionsnyckel. Partitionsnyckel är en unik identifierare för partitionen i en specifik tabell som utgör den första delen av en entitets primära nyckel. Det är ett sträng värde som kan vara upp till 1 KB stort. 
    - Gäller för Table Storage och Azure Function 
  - ROW_KEY:  
    Namnet på kolumnen utdata som innehåller rad nyckeln. Rad nyckeln är en unik identifierare för en entitet inom en specifik partition. Den utgör den andra delen av en entitets primära nyckel. Rad nyckeln är ett sträng värde som kan vara upp till 1 KB stort. 
    - Gäller för Table Storage och Azure Function 
  - BATCH_SIZE:  
    Detta representerar antalet transaktioner för Table Storage där det maximala antalet kan gå upp till 100 poster. För Azure Functions representerar detta batchstorleken i byte som skickas till funktionen per anrop-standard är 256 kB. 
    - Gäller för Table Storage och Azure Function 
  - MAXIMUM_BATCH_COUNT:  
    Maximalt antal händelser som skickats till funktionen per anrop för Azure Function-standard är 100. För SQL Database representerar detta det maximala antalet poster som skickas med varje Mass infognings transaktion – standardvärdet är 10 000. 
    - Gäller för SQL Database och Azure Function 
  - STAGING_AREA: objektet för extern datakälla till Blob Storage mellanlagringsområdet för data inmatning med stora data flöden i SQL Data Warehouse 
    - Gäller för SQL Data Warehouse


## <a name="examples"></a>Exempel

### <a name="example-1---edgehub"></a>Exempel 1 – EdgeHub

Typ: indata eller utdata<br>
Parametrar:
- Indata eller utdata
  - Alias 
  - Händelseserialiseringsformat 
  - Kodning 
- Endast indatatyper: 
  - Händelse komprimerings typ 

Syntax:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = 'JSON', 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   EXTERNAL_FILE_FORMAT = myFileFormat, 
   LOCATION = ‘<mytopicname>’, 
   OUTPUT_OPTIONS =   
     ‘REJECT_TYPE: Drop’ 
);
```


### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Exempel 2 – Azure SQL Database, Azure SQL Edge, SQL Server

Typ: utdata<br>
Parametrar:
- Utdataalias  
- Databas (krävs för SQL Database) 
- Server (krävs för SQL Database) 
- Användar namn (krävs för SQL Database) 
- Lösen ord (krävs för SQL Database) 
- Tabell 
- Slå samman alla inmatade partitioner till ett enda Skriv-eller Ärv partition schema med föregående frågefel eller indatatyper (krävs för SQL Database) 
- Max antal batchar 

Syntax:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Database/Edge 
CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = ‘<DatabaseName>.<SchemaName>.<TableName>’ 
   --Note: If table is container in the database, <TableName> should be sufficient 
   --Note: Do not need external file format in this case 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop 
); 
```

### <a name="example-3---kafka"></a>Exempel 3 – Kafka

Typ: inmatat<br>
Parametrar:

- Kafka Start Server 
- Ämnes namn för Kafka 
- Antal käll partitioner 

Syntax:

```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = ' kafka://<kafkaserver>:<ipaddress>’, 
  CREDENTIAL = kafkaCredName 
 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = ‘<KafkaTopicName>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 5’ 
); 
```

### <a name="example-4---blob-storage"></a>Exempel 4 – Blob Storage

Typ: indata eller utdata<br>
Parametrar:
- Indata eller utdata:
  - Alias 
  - Lagringskonto 
  - Lagrings konto nyckel 
  - Container 
  - Sökvägsmönster 
  - Datum format 
  - Tids format 
  - Händelseserialiseringsformat 
  - Kodning 
- Endast indatatyper: 
  - Partitioner (inmatade) 
  - Händelse komprimerings typ (Indatatyp) 
- Endast utdata: 
  - Lägsta antal rader (utdata) 
  - Maximal tid (utdata) 
  - Autentiseringsläge (utdata) 

Syntax:

```sql
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = 'AccountKey'; 
 
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredNameMSI 
WITH IDENTITY = 'Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyBlobStorage_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATE_FORMAT = 'YYYY/MM/DD HH', --Both Date and Time format 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyBlobStorage_tweets, 
    LOCATION = ‘<path_pattern>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 1’, 
  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: (), 
      MINUMUM_ROWS: 100000, 
      MAXIMUM_TIME: 60’ 
); 
```

### <a name="example-5---event-hub"></a>Exempel 5 – Händelsehubben

Typ: indata eller utdata<br>
Parametrar:
- Indata eller utdata:
  - Alias 
  - Service Bus namnrymd 
  - Namn på händelsehubb 
  - Principnamn för Event Hub 
  - Princip nyckel för Event Hub 
  - Händelseserialiseringsformat 
  - Kodning 
- Endast indatatyper: 
  - Konsumentgrupp för händelsehubb 
  - Händelse komprimerings typ 
- Endast utdata: 
  - Nyckel kolumn för partition 
  - Egenskaps kolumner 

Syntax:

```sql
CREATE DATABASE SCOPED CREDENTIAL eventHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyEventHub_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyEventHub_tweets, 
    LOCATION = ‘<topicname>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
 
    INPUT_OPTIONS =  
      ‘CONSUMER_GROUP: FirstConsumerGroup’, 
          
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: ()’ 
);
```

### <a name="example-6---iot-hub"></a>Exempel 6 – IOT Hub

Typ: inmatat<br>
Parametrar:

- Inmatat alias 
- IoT Hub 
- Slutpunkt 
- Namn på princip för delad åtkomst 
- Nyckel för delad åtkomst princip 
- Konsumentgrupp 
- Händelseserialiseringsformat 
- Kodning 
- Händelse komprimerings typ 

Syntax:

```sql
CREATE DATABASE SCOPED CREDENTIAL IoTHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyIoTHub_tweets 
WITH 
(     
  LOCATION = ' iot://iot_hub_name.azure-devices.net’, 
  CREDENTIAL = IoTHubCredName 
);  

CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyIoTHub_tweets, 
    LOCATION = ‘<name>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘ENDPOINT: Messaging, 
      CONSUMER_GROUP: ‘FirstConsumerGroup’ 
); 
```

### <a name="example-7---azure-synapse-analytics-formerly-sql-data-warehouse"></a>Exempel 7 – Azure Synapse Analytics (tidigare SQL Data Warehouse)

Typ: utdata<br>
Parametrar:
- Utdataalias 
- Databas 
- Server 
- Användarnamn 
- lösenordsinställning 
- Tabell 
- Mellanlagringsområde (för kopiering) 

Syntax:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTable 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
CREATE EXTERNAL STREAM MySQLTableOutput 
WITH ( 
   DATA_SOURCE = MyTargetSQLTable, 
   LOCATION = ‘<TableName>’ 
   --Note: Do not need external file format in this case 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     STAGING_AREA: staging_area_data_source’, 
); 
```


### <a name="example-8---table-storage"></a>Exempel 8 – tabell lagring

Typ: utdata<br>
Parametrar:
- Utdataalias 
- Lagringskonto 
- Lagrings konto nyckel 
- Tabellnamn 
- Partitionsnyckeln 
- Rad nyckel 
- Batchstorlek 

Syntax:

```sql
CREATE DATABASE SCOPED CREDENTIAL TableStorageCredName 
WITH IDENTITY = ‘Storage account Key’, 
SECRET = '<storage_account_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY: <column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


### <a name="example-9---service-bus-topic-same-properties-as-queue"></a>Exempel 9 – Service Bus ämne (samma egenskaper som kön)

Typ: utdata<br>
Parametrar:
- Utdataalias 
- Service Bus namnrymd 
- Ämnes namn 
- Ämnes princip namn 
- Ämnes princip nyckel 
- Egenskaps kolumner 
- Kolumner i system egenskap 
- Händelseserialiseringsformat 
- Kodning 

Syntax:

```sql
CREATE DATABASE SCOPED CREDENTIAL serviceBusCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyServiceBus_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = serviceBusCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM MyServiceBusOutput 
WITH ( 
   DATA_SOURCE = MyServiceBus_tweets, 
   LOCATION = ‘<topic_name>’, 
   EXTERNAL_FILE_FORMAT = myFileFormat 
       OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY_COLUMN: , 
     PROPERTY_COLUMNS: (), 
     SYSTEM_PROPERTY_COLUMNS: ()’ 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
           
); 
```


### <a name="example-10---cosmos-db"></a>Exempel 10 – Cosmos DB

Typ: utdata<br>
Parametrar:
- Utdataalias 
- Konto-ID 
- Kontonyckel 
- Databas 
- Containerns namn 
- Dokument-ID 


Syntax:

```sql
CREATE DATABASE SCOPED CREDENTIAL cosmosDBCredName 
WITH IDENTITY = ‘Storage Account Key’, 
SECRET = '<accountKey>'; 
 
CREATE EXTERNAL DATA SOURCE MyCosmosDB_tweets 
WITH 
(     
  LOCATION = ' cosmosdb://accountid.documents.azure.com:443/ database’, 
  CREDENTIAL = cosmosDBCredName 
); 
 
CREATE EXTERNAL STREAM MyCosmosDBOutput 
WITH ( 
   DATA_SOURCE = MyCosmosDB_tweets, 
   LOCATION = ‘<container/documentID>’ 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop', 
     --Note: Do not need external file format in this case 
          
);
```



### <a name="example-11---power-bi"></a>Exempel 11 – Power BI

Typ: utdata<br>
Parametrar:
- Utdataalias 
- Namn på datauppsättning 
- Tabellnamn 


Syntax:

```sql
CREATE DATABASE SCOPED CREDENTIAL PBIDBCredName 
WITH IDENTITY = ‘Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyPbi_tweets 
WITH 
( 
  LOCATION = 'pbi://dataset/’, 
  CREDENTIAL = PBIDBCredName 
 
); 
 
CREATE EXTERNAL STREAM MyPbiOutput 
WITH ( 
   DATA_SOURCE = MyPbi_tweets, 
   LOCATION = 'tableName', 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop' 
        
);
```

### <a name="example-12---azure-function"></a>Exempel 12 – Azure Function

Typ: utdata<br>
Parametrar:
- Funktionsapp 
- Funktion 
- Nyckel 
- Max storlek på batch 
- Max antal batchar 

Syntax:

```sql
CREATE DATABASE SCOPED CREDENTIAL AzureFunctionCredName 
WITH IDENTITY = ‘Function Key’, 
SECRET = '<function_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: 'Drop'      
     PARTITION_KEY: ‘<column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


## <a name="see-also"></a>Se även

- [ÄNDRA extern ström (Transact-SQL)](alter-external-stream-transact-sql.md) 
- [SLÄPP extern ström (Transact-SQL)](drop-external-stream-transact-sql.md) 

