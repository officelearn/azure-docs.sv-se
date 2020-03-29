---
title: Ingest JSON-formaterade data i Azure Data Explorer
description: Lär dig mer om hur du intag av JSON-formaterade data i Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: d293b76e004d693813a074cb8551a86cb3c0bec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772338"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Ingest JSON-formaterade exempeldata i Azure Data Explorer

Den här artikeln visar hur du inbjuder JSON-formaterade data i en Azure Data Explorer-databas. Du börjar med enkla exempel på råa och mappade JSON, fortsätter till flera fodrade JSON och tar sedan itu med mer komplexa JSON-scheman som innehåller matriser och ordlistor.  Exemplen beskriver processen med att inta JSON-formaterade data med Kusto-frågespråk (KQL), C#eller Python. Kommandona för `ingest` Kusto-frågespråkkontroll körs direkt till motorns slutpunkt. I produktionsscenarier körs inmatning till datahanteringstjänsten med hjälp av klientbibliotek eller dataanslutningar. Läs [Ingest-data med Azure Data Explorer Python-biblioteket](/azure/data-explorer/python-ingest-data) och [Ingest-data med hjälp av Azure Data Explorer .NET Standard SDK](/azure/data-explorer/net-standard-ingest-data) för en genomgång om intag av data med dessa klientbibliotek.

## <a name="prerequisites"></a>Krav

[Ett testkluster och en databas](create-cluster-database-portal.md)

## <a name="the-json-format"></a>JSON-formatet

Azure Data Explorer stöder två JSON-filformat:
* `json`: Linje separerade JSON. Varje rad i indata har exakt en JSON-post.
* `multijson`: Multi-fodrad JSON. Tolken ignorerar radavgränsare och läser en post från föregående position till slutet av en giltig JSON.

### <a name="ingest-and-map-json-formatted-data"></a>Inta och mappa JSON-formaterade data

Inmatning av JSON-formaterade data kräver att du anger *formatet* med [inmatningsegenskapen](/azure/kusto/management/data-ingestion/index#ingestion-properties). Intag av JSON-data kräver [mappning](/azure/kusto/management/mappings), som mappar en JSON-källpost till målkolumnen. När du intag av data använder `jsonMappingReference` du egenskapen för `jsonMapping`fördefinierat inmatning eller anger egenskapen intag. Den här artikeln `jsonMappingReference` använder egenskapen intag, som är fördefinierad i tabellen som används för intag. I exemplen nedan börjar vi med att inta JSON-poster som rådata i en enda kolumntabell. Sedan använder vi mappningen för att inta varje egenskap i den mappade kolumnen. 

### <a name="simple-json-example"></a>Enkelt JSON-exempel

Följande exempel är en enkel JSON, med en platt struktur. Data har temperatur- och fuktighetsinformation, som samlas in av flera enheter. Varje post är markerad med ett ID och en tidsstämpel.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Inta råa JSON-poster 

I det här exemplet invänrer du JSON-poster som rådata i en enda kolumntabell. Datamanipulering, med hjälp av frågor och uppdateringsprincip görs när data har förtärs.

# <a name="kql"></a>[KQL (KQL)](#tab/kusto-query-language)

Använd Kusto-frågespråk för att få in data i ett rått JSON-format.

1. Logga in [https://dataexplorer.azure.com](https://dataexplorer.azure.com)på .

1. Välj **Lägg till kluster**.

1. I dialogrutan **Lägg till kluster** anger du `https://<ClusterName>.<Region>.kusto.windows.net/`kluster-URL:en i formuläret och väljer sedan **Lägg till**.

1. Klistra in följande kommando och välj **Kör** för att skapa tabellen.

    ```Kusto
    .create table RawEvents (Event: dynamic)
    ```

    Den här frågan skapar en `Event` tabell med en enda kolumn av en [dynamisk](/azure/kusto/query/scalar-data-types/dynamic) datatyp.

1. Skapa JSON-mappningen.

    ```Kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    Det här kommandot skapar en mappning och `$` mappar `Event` JSON-rotsökvägen till kolumnen.

1. Inta data i `RawEvents` tabellen.

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

Använd C# för att förtära data i råt JSON-format.

1. Skapa `RawEvents` tabellen.

    ```C#
    var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
    var kustoConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder);

    var table = "RawEvents";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Events", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Skapa JSON-mappningen.
    
    ```C#
    var tableMapping = "RawEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                new JsonColumnMapping {ColumnName = "Events", JsonPath = "$"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```
    Det här kommandot skapar en mappning och `$` mappar `Event` JSON-rotsökvägen till kolumnen.

1. Inta data i `RawEvents` tabellen.

    ```C#
    var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var ingestConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder);
    
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

> [!NOTE]
> Data aggregeras enligt [batchprincip](/azure/kusto/concepts/batchingpolicy), vilket resulterar i en svarstid på några minuter.

# <a name="python"></a>[Python](#tab/python)

Använd Python för att få in data i råt JSON-format.

1. Skapa `RawEvents` tabellen.

    ```Python
    KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)
    KUSTO_CLIENT = KustoClient(KCSB_DATA)
    TABLE = "RawEvents"

    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Events: dynamic)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Skapa JSON-mappningen.

    ```Python
    MAPPING = "RawEventMapping"
    CREATE_MAPPING_COMMAND = ".create table " + TABLE + " ingestion json mapping '" + MAPPING + """' '[{"column":"Event","path":"$"}]'"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Inta data i `RawEvents` tabellen.

    ```Python
    INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(INGEST_URI, AAD_TENANT_ID)
    INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    > [!NOTE]
    > Data aggregeras enligt [batchprincip](/azure/kusto/concepts/batchingpolicy), vilket resulterar i en svarstid på några minuter.

---

## <a name="ingest-mapped-json-records"></a>Inta mappade JSON-poster

I det här exemplet inväntar du JSON-poster data. Varje JSON-egenskap mappas till en enskild kolumn i tabellen. 

# <a name="kql"></a>[KQL (KQL)](#tab/kusto-query-language)

1. Skapa en ny tabell med ett liknande schema som JSON-indata. Vi använder den här tabellen för alla följande exempel och intag av kommandon. 

    ```Kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Skapa JSON-mappningen.

    ```Kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    I den här mappningen, som `timestamp` definieras av tabellschemat, intas `Time` `datetime` posterna till kolumnen som datatyper.

1. Inta data i `Events` tabellen.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    Filen "simple.json" har några radavgränsade JSON-poster. Formatet är `json`och den mappning som används i `FlatEventMapping` kommandot ingest är den du skapade.

# <a name="c"></a>[C#](#tab/c-sharp)

1. Skapa en ny tabell med ett liknande schema som JSON-indata. Vi använder den här tabellen för alla följande exempel och intag av kommandon. 

    ```C#
    var table = "Events";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Time", "System.DateTime"),
                Tuple.Create("Device", "System.String"),
                Tuple.Create("MessageId", "System.String"),
                Tuple.Create("Temperature", "System.Double"),
                Tuple.Create("Humidity", "System.Double"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Skapa JSON-mappningen.

    ```C#
    var tableMapping = "FlatEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.timestamp"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.deviceId"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.messageId"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.temperature"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.humidity"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

    I den här mappningen, som `timestamp` definieras av tabellschemat, intas `Time` `datetime` posterna till kolumnen som datatyper.    

1. Inta data i `Events` tabellen.

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

    Filen "simple.json" har några radavgränsade JSON-poster. Formatet är `json`och den mappning som används i `FlatEventMapping` kommandot ingest är den du skapade.

# <a name="python"></a>[Python](#tab/python)

1. Skapa en ny tabell med ett liknande schema som JSON-indata. Vi använder den här tabellen för alla följande exempel och intag av kommandon. 

    ```Python
    TABLE = "RawEvents"
    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Skapa JSON-mappningen.

    ```Python
    MAPPING = "FlatEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Inta data i `Events` tabellen.

    ```Python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    Filen "simple.json" har några rad separerade JSON poster. Formatet är `json`och den mappning som används i `FlatEventMapping` kommandot ingest är den du skapade.    
---

## <a name="ingest-multi-lined-json-records"></a>Inta flerkantade JSON-skivor

I det här exemplet inväntar du flerkantade JSON-poster. Varje JSON-egenskap mappas till en enskild kolumn i tabellen. Filen "multilined.json" har några indragna JSON-poster. Formatet `multijson` talar om för motorn att läsa poster av JSON-strukturen.

# <a name="kql"></a>[KQL (KQL)](#tab/kusto-query-language)

Inta data i `Events` tabellen.

```Kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="c"></a>[C#](#tab/c-sharp)

Inta data i `Events` tabellen.

```C#
var tableMapping = "FlatEventMapping";
var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
var properties =
    new KustoQueuedIngestionProperties(database, table)
    {
        Format = DataSourceFormat.multijson,
        IngestionMappingReference = tableMapping
    };

ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
```

# <a name="python"></a>[Python](#tab/python)

Inta data i `Events` tabellen.

```Python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>Ingest JSON-poster som innehåller matriser

Matrisdatatyper är en ordnad samling värden. Inmatning av en JSON-matris görs av en [uppdateringsprincip](/azure/kusto/management/update-policy). JSON:en förtärs som-är till en mellanliggande tabell. En uppdateringsprincip kör en fördefinierad funktion i `RawEvents` tabellen och definierar om resultaten till måltabellen. Vi kommer att inta data med följande struktur:

```json
{
    "records": 
    [
        {
            "timestamp": "2019-05-02 15:23:50.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "7f316225-839a-4593-92b5-1812949279b3",
            "temperature": 31.0301639051317,
            "humidity": 62.0791099602725
        },
        {
            "timestamp": "2019-05-02 15:23:51.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "57de2821-7581-40e4-861e-ea3bde102364",
            "temperature": 33.7529423105311,
            "humidity": 75.4787976739364
        }
    ]
}
```

# <a name="kql"></a>[KQL (KQL)](#tab/kusto-query-language)

1. Skapa `update policy` en funktion som utökar samlingen av `records` så att varje värde `mv-expand` i samlingen får en separat rad med hjälp av operatorn. Vi använder tabellen `RawEvents` som källtabell `Events` och måltabell.

    ```Kusto
    .create function EventRecordsExpand() {
        RawEvents
        | mv-expand records = Event
        | project
            Time = todatetime(records["timestamp"]),
            Device = tostring(records["deviceId"]),
            MessageId = tostring(records["messageId"]),
            Temperature = todouble(records["temperature"]),
            Humidity = todouble(records["humidity"])
    }
    ```

1. Schemat som tas emot av funktionen måste matcha schemat för måltabellen. Använd `getschema` operatorn för att granska schemat.

    ```Kusto
    EventRecordsExpand() | getschema
    ```

1. Lägg till uppdateringsprincipen i måltabellen. Den här principen kör automatiskt frågan på nya intvalda data i den `RawEvents` mellanliggande tabellen och intänder resultaten i `Events` tabellen. Definiera en princip för nolllagring för att undvika att mellanliggande tabell kvarstår.

    ```Kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Inta data i `RawEvents` tabellen.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. Granska data `Events` i tabellen.

    ```Kusto
    Events
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. Skapa en uppdateringsfunktion som `records` utökar samlingen av så att varje värde `mv-expand` i samlingen får en separat rad med hjälp av operatorn. Vi använder tabellen `RawEvents` som källtabell `Events` och måltabell.   

    ```C#
    var command =
        CslCommandGenerator.GenerateCreateFunctionCommand(
            "EventRecordsExpand",
            "UpdateFunctions",
            string.Empty,
            null,
            @"RawEvents
                | mv-expand records = Event
                | project
                    Time = todatetime(records['timestamp']),
                    Device = tostring(records['deviceId']),
                    MessageId = tostring(records['messageId']),
                    Temperature = todouble(records['temperature']),
                    Humidity = todouble(records['humidity'])",
            ifNotExists: false);

    kustoClient.ExecuteControlCommand(command);
    ```

    > [!NOTE]
    > Schemat som tas emot av funktionen måste matcha schemat för måltabellen.

1. Lägg till uppdateringsprincipen i måltabellen. Den här principen kör automatiskt frågan på nya intvalda data i den `RawEvents` mellanliggande tabellen och intänder resultaten i `Events` tabellen. Definiera en princip för nolllagring för att undvika att mellanliggande tabell kvarstår.

    ```C#
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Inta data i `RawEvents` tabellen.

    ```C#
    var table = "RawEvents";
    var tableMapping = "RawEventMapping";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```
    
1. Granska data `Events` i tabellen.

# <a name="python"></a>[Python](#tab/python)

1. Skapa en uppdateringsfunktion som `records` utökar samlingen av så att varje värde `mv-expand` i samlingen får en separat rad med hjälp av operatorn. Vi använder tabellen `RawEvents` som källtabell `Events` och måltabell.   

    ```Python
    CREATE_FUNCTION_COMMAND = 
        '''.create function EventRecordsExpand() { 
            RawEvents
            | mv-expand records = Event
            | project
                Time = todatetime(records["timestamp"]),
                Device = tostring(records["deviceId"]),
                MessageId = tostring(records["messageId"]),
                Temperature = todouble(records["temperature"]),
                Humidity = todouble(records["humidity"])
            }'''
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_FUNCTION_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

    > [!NOTE]
    > Schemat som tas emot av funktionen måste matcha schemat för måltabellen.

1. Lägg till uppdateringsprincipen i måltabellen. Den här principen kör automatiskt frågan på nya intvalda data i den `RawEvents` mellanliggande tabellen och intänder resultaten i `Events` tabellen. Definiera en princip för nolllagring för att undvika att mellanliggande tabell kvarstår.

    ```Python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Inta data i `RawEvents` tabellen.

    ```Python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Granska data `Events` i tabellen.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>Ingest JSON-poster som innehåller ordlistor

Ordlistestrukturerad JSON innehåller nyckelvärdespar. Json-poster genomgår inmatningsmappning `JsonPath`med logiskt uttryck i . Du kan få in data med följande struktur:

```json
{
    "event": 
    [
        {
            "Key": "timestamp",
            "Value": "2019-05-02 15:23:50.0000000"
        },
        {
            "Key": "deviceId",
            "Value": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71"
        },
        {
            "Key": "messageId",
            "Value": "7f316225-839a-4593-92b5-1812949279b3"
        },
        {
            "Key": "temperature",
            "Value": 31.0301639051317
        },
        {
            "Key": "humidity",
            "Value": 62.0791099602725
        }
    ]
}
```

# <a name="kql"></a>[KQL (KQL)](#tab/kusto-query-language)

1. Skapa en JSON-mappning.

    ```Kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. Inta data i `Events` tabellen.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. Skapa en JSON-mappning.

    ```C#
    var tableName = "Events";
    var tableMapping = "KeyValueEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.event[?(@.Key == 'timestamp')]"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.event[?(@.Key == 'deviceId')]"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.event[?(@.Key == 'messageId')]"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.event[?(@.Key == 'temperature')]"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.event[?(@.Key == 'humidity')]"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Inta data i `Events` tabellen.

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

# <a name="python"></a>[Python](#tab/python)

1. Skapa en JSON-mappning.

    ```Python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Inta data i `Events` tabellen.

     ```Python
    MAPPING = "KeyValueEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)u
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

---    

## <a name="next-steps"></a>Nästa steg

* [Översikt över datainmatning](ingest-data-overview.md)
* [Skriva frågor](write-queries.md)