---
title: 'Snabbstart: Mata in data med hjälp av Node-biblioteket i Azure Data Explorer'
description: I den här snabbstarten lär du dig att mata in (läsa in) data i Azure-datautforskaren med hjälp av Node.js.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/25/2018
ms.openlocfilehash: a7ebceff18016a5aa527a032a644d2723aceee7a
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558574"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-node-library"></a>Snabbstart: Mata in data med hjälp av Node-biblioteket i Azure Data Explorer

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure-datautforskaren tillhandahåller två klientbibliotek för Node: ett [bibliotek för inmatning](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest) och [ett databibliotek](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data). I biblioteken kan du mata in (läsa in) data i ett kluster och fråga data från din kod. I den här snabbstarten skapar du först en tabell och datamappning i ett testkluster. Sedan köar du inmatningen till klustret och verifierar resultaten.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Förutom en Azure-prenumeration behöver du följande för att slutföra den här snabbstarten:

* [Ett testkluster och en databas](create-cluster-database-portal.md)

* [Node.js](https://nodejs.org/en/download/) installerat på utvecklingsdatorn

## <a name="install-the-data-and-ingest-libraries"></a>Installera data och mata in bibliotek

Installera *azure-kusto-ingest* och *azure-kusto-data*

```bash
npm i --save azure-kusto-ingest azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Lägg till importuttryck och konstanter

Importera klasser från biblioteken

```javascript

const KustoConnectionStringBuilder = require("azure-kusto-data").KustoConnectionStringBuilder;
const KustoClient = require("azure-kusto-data").Client;
const KustoIngestClient = require("azure-kusto-ingest").IngestClient;
const IngestionProperties = require("azure-kusto-ingest").IngestionProperties;
const { DataFormat } = require("azure-kusto-ingest").IngestionPropertiesEnums;
const { BlobDescriptor } = require("azure-kusto-ingest").IngestionDescriptors;

```
För att autentisera ett program använder Azure-datautforskaren ditt Azure Active Directory-klientorganisations-ID. Du hittar ditt klientorganisations-ID genom att följa avsnittet om att [hitta ditt Office 365-klientorganisations-ID](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id).

Ange värdena för `authorityId`, `kustoUri`, `kustoIngestUri` och `kustoDatabase` innan du kör den här koden.

```javascript
const authorityId = "<TenantId>";
const kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443";
const kustoIngestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443";
const kustoDatabase  = "<DatabaseName>";
```

Nu kan du skapa anslutningssträngen. I det här exemplet används enhetsautentisering för åtkomst till klustret. Du kan även använda Azure Active Directory-programcertifikat, programnyckel samt användare och lösenord.

Du kan skapa måltabellen och mappningen i ett senare steg.

```javascript
const kcsbIngest = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoIngestUri, authorityId);
const kcsbData = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoUri, authorityId);
const destTable = "StormEvents";
const destTableMapping = "StormEvents_CSV_Mapping";
```

## <a name="set-source-file-information"></a>Ange information om källfilen

Importera ytterligare klasser och ange konstanter för datakällan. Det här exemplet används en exempelfil som finns på Azure Blob Storage. I exempeldatauppsättningen **StormEvents** finns väderrelaterade data från [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```javascript
const container = "samplefiles";
const account = "kustosamplefiles";
const sas = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
const filePath = "StormEvents.csv";
const blobPath = `https://${account}.blob.core.windows.net/${container}/${filePath}${sas}`;
```

## <a name="create-a-table-on-your-test-cluster"></a>Skapa en tabell i ditt testkluster

Skapa en tabell som matchar schemat för data i filen `StormEvents.csv`. När den här koden körs returneras ett meddelande som liknar följande: *För att logga in använder du en webbläsare och öppnar sidan https://microsoft.com/devicelogin. Ange sedan koden XXXXXXXXX för att autentisera dig*. Följ stegen för att logga in och gå sedan tillbaka för att köra nästa kodblock. Efterföljande kodblock som upprättar en anslutning kräver att du loggar in igen.

```javascript
const kustoClient = new KustoClient(kcsbData);
const createTableCommand = `.create table ${destTable} (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)`;

kustoClient.executeMgmt(kustoDatabase, createTableCommand, (err, results) => {
    console.log(result.primaryResults[0]);
});
```

## <a name="define-ingestion-mapping"></a>Definiera mappning av inmatning

Mappa inkommande CSV-data till kolumnnamnen och datatyperna som används när du skapade tabellen.

```javascript
const createMappingCommand = `.create table ${destTable} ingestion csv mapping '${destTableMapping}' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'`;

kustoClient.executeMgmt(kustoDatabase, createMappingCommand, (err, results) => {
    console.log(result.primaryResults[0]);
});
```

## <a name="queue-a-message-for-ingestion"></a>Köa ett meddelande för inmatning

Köa ett meddelande för att hämta data från Blob Storage och mata in data i Azure Data Explorer.

```javascript
const defaultProps  = new IngestionProperties(kustoDatabase, destTable, DataFormat.csv, null,destTableMapping, {'ignoreFirstRecord': 'true'});
const ingestClient = new KustoIngestClient(kcsbIngest, defaultProps);
// All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties

const blobDesc = new BlobDescriptor(blobPath, 10);
ingestClient.ingestFromBlob(blobDesc,null, (err) => {
    if (err) throw new Error(err);
});
```

## <a name="validate-that-table-contains-data"></a>Verifiera att tabellen innehåller data

Verifiera att data matades in i tabellen. Vänta fem till tio minuter medan den köade datainmatningen schemaläggs för inmatning och läser in data i Azure Data Explorer. Kör sedan följande kod för att hämta posterna i tabellen `StormEvents`.

```javascript
const query = `${destTable} | count`;

kustoClient.execute(kustoDatabase, query, (err, results) => {
    if (err) throw new Error(err);  
    console.log(results.primaryResults[0].toString());
});
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

Om du planerar att följa våra andra snabbstarter och självstudier kan du spara alla resurser som du skapade. Om inte kör du följande kommando i din databas för att rensa tabellen `StormEvents`.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skriv frågor](write-queries.md)
