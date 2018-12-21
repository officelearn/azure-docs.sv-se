---
title: 'Snabbstart: Mata in data med hjälp av Python-biblioteket i Azure Data Explorer'
description: I den här snabbstarten får du lära dig hur mata in (läsa in) data i Azure Data Explorer med hjälp av Python.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/16/2018
ms.openlocfilehash: 390cdddf09f6880368d4d199eef41be19b54d9f0
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339254"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-python-library"></a>Snabbstart: Mata in data med hjälp av Python-biblioteket i Azure Data Explorer

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure Data Explorer tillhandahåller två klientbibliotek för Python: ett [bibliotek för inmatning](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest) och [ett databibliotek](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). I biblioteken kan du mata in (läsa in) data i ett kluster och fråga data från din kod. I den här snabbstarten skapar du först en tabell och datamappning i ett testkluster. Sedan köar du inmatningen till klustret och verifierar resultaten.

Den här snabbstarten är också tillgänglig som en [Azure Notebook](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueuedIngestSingleBlob.ipynb).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Förutom en Azure-prenumeration behöver du följande för att slutföra den här snabbstarten:

* [Ett testkluster och en databas](create-cluster-database-portal.md)

* [Python](https://www.python.org/downloads/) installerat på utvecklingsdatorn

## <a name="install-the-data-and-ingest-libraries"></a>Installera data och mata in bibliotek

Installera *azure-kusto-data* och *azure-kusto-ingest*.

```
pip install azure-kusto-data
pip install azure-kusto-ingest
```

## <a name="add-import-statements-and-constants"></a>Lägg till importuttryck och konstanter

Importera klasser från biblioteket, och även *datetime* och *pandas*, ett dataanalysbibliotek.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
import datetime
```

Azure Data Explorer använder ditt AAD-klient-ID för att autentisera ett program. Du hittar ditt klient-ID genom att använda följande URL. Byt ut *YourDomain* mot din domän.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Om din domän till exempel är *contoso.com* blir URL:en: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Klicka på den här URL:en för att se resultatet. Den första raden ser ut så här. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Klient-ID är i det här fallet `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Ange värdena för AAD_TENANT_ID, KUSTO_URI, KUSTO_INGEST_URI och KUSTO_DATABASE innan du kör koden.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_DATABASE  = "<DatabaseName>"
```

Skapa nu anslutningssträngen. I det här exemplet används enhetsautentisering för åtkomst till klustret. Du kan också använda ett [AAD-programcertifikat](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24), [en AAD-programnyckel](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20) och [användare och lösenord för AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34).

Du kan skapa måltabellen och mappningen i ett senare steg.

```python
KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_INGEST_URI, AAD_TENANT_ID)

KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)

DESTINATION_TABLE = "StormEvents"
DESTINATION_TABLE_COLUMN_MAPPING = "StormEvents_CSV_Mapping"
```

## <a name="set-source-file-information"></a>Ange information om källfilen

Importera ytterligare klasser och ange konstanter för datakällan. Det här exemplet används en exempelfil som finns på Azure Blob Storage. I exempeldatauppsättningen **StormEvents** finns väderrelaterade data från [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```python
from azure.storage.blob import BlockBlobService
from azure.kusto.ingest import KustoIngestClient, IngestionProperties, FileDescriptor, BlobDescriptor, DataFormat, ReportLevel, ReportMethod

CONTAINER = "samplefiles"
ACCOUNT_NAME = "kustosamplefiles"
SAS_TOKEN = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D"
FILE_PATH = "StormEvents.csv"
FILE_SIZE = 64158321    # in bytes

BLOB_PATH = "https://" + ACCOUNT_NAME + ".blob.core.windows.net/" + CONTAINER + "/" + FILE_PATH + SAS_TOKEN
```

## <a name="create-a-table-on-your-test-cluster"></a>Skapa en tabell i ditt testkluster

Skapa en tabell som matchar schemat för data i filen StormEvents.csv. När den här koden körs returneras ett meddelande som liknar följande: *För att logga in använder du en webbläsare för att öppna sidan https://microsoft.com/devicelogin och anger sedan koden F3W4VWZDM för att autentisera dig*. Följ stegen för att logga in och gå sedan tillbaka för att köra nästa kodblock. Efterföljande kodblock som upprättar en anslutning kräver att du loggar in igen.

```python
KUSTO_CLIENT = KustoClient(KCSB_DATA)
CREATE_TABLE_COMMAND = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)"

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_TABLE_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="define-ingestion-mapping"></a>Definiera mappning av inmatning

Mappa inkommande CSV-data till kolumnnamnen och datatyperna som används när du skapade tabellen.

```python
CREATE_MAPPING_COMMAND = """.create table StormEvents ingestion csv mapping 'StormEvents_CSV_Mapping' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'"""

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_MAPPING_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="queue-a-message-for-ingestion"></a>Köa ett meddelande för inmatning

Köa ett meddelande för att hämta data från Blob Storage och mata in data i Azure Data Explorer.

```python
INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)

# All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties
INGESTION_PROPERTIES  = IngestionProperties(database=KUSTO_DATABASE, table=DESTINATION_TABLE, dataFormat=DataFormat.csv, mappingReference=DESTINATION_TABLE_COLUMN_MAPPING, additionalProperties={'ignoreFirstRecord': 'true'})
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)  # 10 is the raw size of the data in bytes
INGESTION_CLIENT.ingest_from_blob(BLOB_DESCRIPTOR,ingestion_properties=INGESTION_PROPERTIES)

print('Done queuing up ingestion with Azure Data Explorer')

```

## <a name="validate-that-data-was-ingested-into-the-table"></a>Verifiera att data har matats in i tabellen

Vänta fem till tio minuter medan den köade datainmatningen schemaläggs för inmatning och läser in data i Azure Data Explorer. Kör sedan följande kod för att hämta posterna i tabellen StormEvents.

```python
QUERY = "StormEvents | count"

RESPONSE = KUSTO_CLIENT.execute_query(KUSTO_DATABASE, QUERY)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="run-troubleshooting-queries"></a>Köra frågor för felsökning

Logga in på [https://dataexplorer.azure.com](https://dataexplorer.azure.com) och anslut till klustret. Kör följande kommando i din databas för att se om det fanns inmatningsfel under de senaste fyra timmarna. Ersätt namnet på databasen innan du kör.

```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Kör följande kommando för att visa status för alla åtgärder för inmatning under de sista fyra timmarna. Ersätt namnet på databasen innan du kör.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att följa våra andra snabbstarter och självstudier kan du spara alla resurser som du skapade. Om inte kör du följande kommando i din databas för att rensa tabellen StormEvents.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skriv frågor](write-queries.md)
