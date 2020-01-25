---
title: Mata in JSON-formaterade data i Azure Datautforskaren
description: Lär dig mer om hur du matar in JSON-formaterade data i Azure Datautforskaren.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: ef5c7de782d833aad96516d3e5357a0ed575a781
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722881"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Hämta JSON-formaterade exempel data till Azure Datautforskaren

Den här artikeln visar hur du matar in JSON-formaterade data i en Azure Datautforskaren Database. Du börjar med enkla exempel på RAW och mappad JSON, fortsätter till multi-reported JSON och sedan kan du ta itu med mer komplexa JSON-scheman som innehåller matriser och ord listor. 

## <a name="prerequisites"></a>Krav

[Ett testkluster och en databas](create-cluster-database-portal.md)

## <a name="the-json-format"></a>JSON-format

Azure Datautforskaren stöder två JSON-fil format:
* `json`: separerad JSON-fil. Varje rad i indata innehåller exakt en JSON-post.
* `multijson`: multi-linjerat JSON. Parsern ignorerar rad avgränsare och läser en post från föregående position till slutet av en giltig JSON.

### <a name="ingest-and-map-json-formatted-data"></a>Mata in och mappa JSON-formaterade data

Inmatning av JSON-formaterade data kräver att du anger *formatet* med inmatnings [egenskapen](/azure/kusto/management/data-ingestion/index#ingestion-properties). Inmatning av JSON-data kräver [mappning](/azure/kusto/management/mappings), som mappar en JSON-datakälla till mål kolumnen. När du matar in data använder du den fördefinierade `jsonMappingReference`-inmatnings egenskapen eller anger egenskapen `jsonMapping`inmatning. Den här artikeln använder egenskapen `jsonMappingReference` inmatning, som är fördefinierad i tabellen som används för inmatning. I exemplen nedan börjar vi genom att mata in JSON-poster som rå data i en enda kolumn tabell. Sedan använder vi mappningen för att mata in varje egenskap till dess mappade kolumn. 

### <a name="simple-json-example"></a>Enkelt JSON-exempel

Följande exempel är en enkel JSON, med en platt struktur. Informationen innehåller temperatur-och fuktighets information som samlas in av flera enheter. Varje post markeras med ett ID och tidsstämpel.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Mata in rå JSON-poster 

I det här exemplet matar du in JSON-poster som rå data i en enda kolumn tabell. Data manipulationen, med hjälp av frågor och uppdaterings principen utförs efter att data har matats in.

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

Använd Kusto frågespråk för att mata in data i RAW JSON-format.

1. Logga in på [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Välj **Lägg till kluster**.

1. I dialog rutan **Lägg till kluster** anger du din kluster-URL i formuläret `https://<ClusterName>.<Region>.kusto.windows.net/`och väljer sedan **Lägg till**.

1. Klistra in följande kommando och välj **Kör** för att skapa tabellen.

    ```Kusto
    .create table RawEvents (Event: dynamic)
    ```

    Den här frågan skapar en tabell med en enda `Event` kolumn av en [dynamisk](/azure/kusto/query/scalar-data-types/dynamic) datatyp.

1. Skapa JSON-mappningen.

    ```Kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    Detta kommando skapar en mappning och mappar JSON-rotens sökväg `$` till kolumnen `Event`.

1. Mata in data i `RawEvents`s tabellen.

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

    > [!NOTE]
    > Detta visar de `ingest` kontroll kommandon som körs direkt till motor slut punkten. I produktions scenarier körs inmatningen till den Datahantering tjänsten med hjälp av klient bibliotek eller data anslutningar. Läs [in inmatnings data med hjälp av azure datautforskaren python-biblioteket](/azure/data-explorer/python-ingest-data) och mata [in data med hjälp av Azure DATAUTFORSKAREN .net standard SDK](/azure/data-explorer/net-standard-ingest-data) för att få en genom gång om att mata in data med dessa klient bibliotek.

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

Används C# för att mata in data i RAW JSON-format.

1. Skapa tabellen `RawEvents`.

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
    Detta kommando skapar en mappning och mappar JSON-rotens sökväg `$` till kolumnen `Event`.

1. Mata in data i `RawEvents`s tabellen.

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
> Data sammanställs enligt [batch-principen](/azure/kusto/concepts/batchingpolicy), vilket resulterar i en fördröjning på några minuter.

# <a name="pythontabpython"></a>[Python](#tab/python)

Använd python för att mata in data i RAW JSON-format.

1. Skapa tabellen `RawEvents`.

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

1. Mata in data i `RawEvents`s tabellen.

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
    > Data sammanställs enligt [batch-principen](/azure/kusto/concepts/batchingpolicy), vilket resulterar i en fördröjning på några minuter.

---

## <a name="ingest-mapped-json-records"></a>Intag av mappade JSON-poster

I det här exemplet tar du in data för JSON-poster. Varje JSON-egenskap mappas till en enda kolumn i tabellen. 

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Skapa en ny tabell med ett liknande schema för JSON-indata. Vi använder den här tabellen för alla följande exempel och inmatnings kommandon. 

    ```Kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Skapa JSON-mappningen.

    ```Kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    I den här mappningen, som definieras av tabell schemat, kommer `timestamp` poster att matas in i kolumnen `Time` som `datetime` data typer.

1. Mata in data i `Events`s tabellen.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    Filen "enkel. JSON" har några tabbavgränsade JSON-poster. Formatet är `json`och mappningen som används i kommandot intag är den `FlatEventMapping` du skapade.

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Skapa en ny tabell med ett liknande schema för JSON-indata. Vi använder den här tabellen för alla följande exempel och inmatnings kommandon. 

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

    I den här mappningen, som definieras av tabell schemat, kommer `timestamp` poster att matas in i kolumnen `Time` som `datetime` data typer.    

1. Mata in data i `Events`s tabellen.

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

    Filen "enkel. JSON" har några tabbavgränsade JSON-poster. Formatet är `json`och mappningen som används i kommandot intag är den `FlatEventMapping` du skapade.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Skapa en ny tabell med ett liknande schema för JSON-indata. Vi använder den här tabellen för alla följande exempel och inmatnings kommandon. 

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

1. Mata in data i `Events`s tabellen.

    ```Python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    Filen "enkel. JSON" har några rader åtskilda JSON-poster. Formatet är `json`och mappningen som används i kommandot intag är den `FlatEventMapping` du skapade.    
---

## <a name="ingest-multi-lined-json-records"></a>Mata in multi-linjerade JSON-poster

I det här exemplet matar du in multi-linjerade JSON-poster. Varje JSON-egenskap mappas till en enda kolumn i tabellen. Filen ' multilineed. JSON ' har några indragna JSON-poster. Formatet `multijson` talar om för motorn att läsa poster med JSON-strukturen.

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

Mata in data i `Events`s tabellen.

```Kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

Mata in data i `Events`s tabellen.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Mata in data i `Events`s tabellen.

```Python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>Mata in JSON-poster som innehåller matriser

Mat ris data typer är en ordnad samling av värden. Inmatningen av en JSON-matris görs av en [uppdaterings princip](/azure/kusto/management/update-policy). JSON matas in som-är i en mellanliggande tabell. En uppdaterings princip kör en fördefinierad funktion i `RawEvents` tabellen och matar in resultaten till mål tabellen. Vi kommer att mata in data med följande struktur:

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

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Skapa en `update policy`-funktion som utökar samlingen av `records` så att varje värde i samlingen får en separat rad med hjälp av `mv-expand`-operatorn. Vi använder tabell `RawEvents` som käll tabell och `Events` som mål tabell.

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

1. Schemat som tas emot av funktionen måste matcha mål tabellens schema. Använd `getschema`-operatorn för att granska schemat.

    ```Kusto
    EventRecordsExpand() | getschema
    ```

1. Lägg till uppdaterings principen i mål tabellen. Den här principen kör frågan automatiskt på alla nyligen inmatade data i tabellen `RawEvents` mellanliggande och matar in resultaten i `Events`s tabellen. Definiera en tom bevarande princip för att undvika att behålla den mellanliggande tabellen.

    ```Kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Mata in data i `RawEvents`s tabellen.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. Granska data i `Events`s tabellen.

    ```Kusto
    Events
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Skapa en Update-funktion som utökar samlingen av `records` så att varje värde i samlingen får en separat rad med hjälp av `mv-expand`-operatorn. Vi använder tabell `RawEvents` som käll tabell och `Events` som mål tabell.   

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
    > Schemat som tas emot av funktionen måste matcha mål tabellens schema.

1. Lägg till uppdaterings principen i mål tabellen. Den här principen kör frågan automatiskt på alla nyligen inmatade data i `RawEvents` mellanliggande tabell och matar in resultatet i `Events`s tabellen. Definiera en tom bevarande princip för att undvika att behålla den mellanliggande tabellen.

    ```C#
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Mata in data i `RawEvents`s tabellen.

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
    
1. Granska data i `Events`s tabellen.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Skapa en Update-funktion som utökar samlingen av `records` så att varje värde i samlingen får en separat rad med hjälp av `mv-expand`-operatorn. Vi använder tabell `RawEvents` som käll tabell och `Events` som mål tabell.   

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
    > Schemat som tas emot av funktionen måste matcha schemat för mål tabellen.

1. Lägg till uppdaterings principen i mål tabellen. Den här principen kör frågan automatiskt på alla nyligen inmatade data i `RawEvents` mellanliggande tabell och matar in resultatet i `Events`s tabellen. Definiera en tom bevarande princip för att undvika att behålla den mellanliggande tabellen.

    ```Python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Mata in data i `RawEvents`s tabellen.

    ```Python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Granska data i `Events`s tabellen.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>Mata in JSON-poster som innehåller ord listor

Dictionary Structured JSON innehåller nyckel/värde-par. JSON-poster genomgår inmatnings mappning med ett logiskt uttryck i `JsonPath`. Du kan mata in data med följande struktur:

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

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Skapa en JSON-mappning.

    ```Kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. Mata in data i `Events`s tabellen.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

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

1. Mata in data i `Events`s tabellen.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Skapa en JSON-mappning.

    ```Python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Mata in data i `Events`s tabellen.

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

* [Översikt över data inmatning](ingest-data-overview.md)
* [Skriv frågor](write-queries.md)