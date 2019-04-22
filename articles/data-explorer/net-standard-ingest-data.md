---
title: 'Snabbstart: Mata in data med hjälp av Azure Data Explorer .NET Standard SDK (förhandsversion)'
description: I den här snabbstarten lär du dig att mata in (läsa in) data i Azure-datautforskaren med hjälp av .NET Standard SDK.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/18/2018
ms.openlocfilehash: 6a068c45a13bd45a09ed51fd154b5842938e0c5e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59044678"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-net-standard-sdk-preview"></a>Snabbstart: Mata in data med hjälp av Azure Data Explorer .NET Standard SDK (förhandsversion)

Azure Data Explorer (ADX) är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. ADX tillhandahåller två klientbibliotek för .NET Standard: ett [bibliotek för inmatning](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard) och [ett databibliotek](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard). I biblioteken kan du mata in (läsa in) data i ett kluster och fråga data från din kod. I den här snabbstarten skapar du först en tabell och datamappning i ett testkluster. Sedan köar du en inmatning till klustret och verifierar resultatet.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

* [Ett testkluster och en databas](create-cluster-database-portal.md)

## <a name="install-the-ingest-library"></a>Installera biblioteket för inmatning

```
Install-Package Microsoft.Azure.Kusto.Ingest.NETStandard
```

## <a name="authentication"></a>Authentication

Azure Data Explorer använder ditt AAD-klient-ID för att autentisera ett program. Du hittar ditt klient-ID genom att använda följande URL. Byt ut *YourDomain* mot din domän.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Om din domän till exempel är *contoso.com* blir URL:en: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Klicka på den här URL:en för att se resultatet. Den första raden ser ut så här. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Klient-ID är i det här fallet `6babcaad-604b-40ac-a9d7-9fd97c0b779f`.

I det här exemplet används en AAD-användare och lösenord för autentisering för åtkomst till klustret. Du kan också använda ett AAD-programcertifikat och en AAD-programnyckel. Ange värdena för `tenantId`, `user` och `password` innan du kör den här koden.

```csharp
var tenantId = "<TenantId>";
var user = "<User>";
var password = "<Password>";
```

## <a name="construct-the-connection-string"></a>Skapa anslutningssträngen
Nu kan du skapa anslutningssträngen. Du kan skapa måltabellen och mappningen i ett senare steg.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var database = "<DatabaseName>";

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };
```

## <a name="set-source-file-information"></a>Ange information om källfilen

Ange sökvägen till källfilen. Det här exemplet används en exempelfil som finns på Azure Blob Storage. I exempeldatauppsättningen **StormEvents** finns väderrelaterade data från [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```csharp
var blobPath = "https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
```

## <a name="create-a-table-on-your-test-cluster"></a>Skapa en tabell i ditt testkluster
Skapa en tabell med namnet `StormEvents` som matchar schemat för data i filen `StormEvents.csv`.

```csharp
var table = "StormEvents";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("StartTime", "System.DateTime"),
                Tuple.Create("EndTime", "System.DateTime"),
                Tuple.Create("EpisodeId", "System.Int32"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("State", "System.String"),
                Tuple.Create("EventType", "System.String"),
                Tuple.Create("InjuriesDirect", "System.Int32"),
                Tuple.Create("InjuriesIndirect", "System.Int32"),
                Tuple.Create("DeathsDirect", "System.Int32"),
                Tuple.Create("DeathsIndirect", "System.Int32"),
                Tuple.Create("DamageProperty", "System.Int32"),
                Tuple.Create("DamageCrops", "System.Int32"),
                Tuple.Create("Source", "System.String"),
                Tuple.Create("BeginLocation", "System.String"),
                Tuple.Create("EndLocation", "System.String"),
                Tuple.Create("BeginLat", "System.Double"),
                Tuple.Create("BeginLon", "System.Double"),
                Tuple.Create("EndLat", "System.Double"),
                Tuple.Create("EndLon", "System.Double"),
                Tuple.Create("EpisodeNarrative", "System.String"),
                Tuple.Create("EventNarrative", "System.String"),
                Tuple.Create("StormSummary", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="define-ingestion-mapping"></a>Definiera mappning av inmatning

Mappa inkommande CSV-data till de kolumnnamn som används när du skapade tabellen.
Etablera ett [mappningsobjekt för CSV-kolumnen](/azure/kusto/management/tables#create-ingestion-mapping) på tabellen

```csharp
var tableMapping = "StormEvents_CSV_Mapping";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
            table,
            tableMapping,
            new[]
            {
                new CsvColumnMapping { ColumnName = "StartTime", Ordinal = 0 },
                new CsvColumnMapping { ColumnName = "EndTime", Ordinal = 1 },
                new CsvColumnMapping { ColumnName = "EpisodeId", Ordinal = 2 },
                new CsvColumnMapping { ColumnName = "EventId", Ordinal = 3 },
                new CsvColumnMapping { ColumnName = "State", Ordinal = 4 },
                new CsvColumnMapping { ColumnName = "EventType", Ordinal = 5 },
                new CsvColumnMapping { ColumnName = "InjuriesDirect", Ordinal = 6 },
                new CsvColumnMapping { ColumnName = "InjuriesIndirect", Ordinal = 7 },
                new CsvColumnMapping { ColumnName = "DeathsDirect", Ordinal = 8 },
                new CsvColumnMapping { ColumnName = "DeathsIndirect", Ordinal = 9 },
                new CsvColumnMapping { ColumnName = "DamageProperty", Ordinal = 10 },
                new CsvColumnMapping { ColumnName = "DamageCrops", Ordinal = 11 },
                new CsvColumnMapping { ColumnName = "Source", Ordinal = 12 },
                new CsvColumnMapping { ColumnName = "BeginLocation", Ordinal = 13 },
                new CsvColumnMapping { ColumnName = "EndLocation", Ordinal = 14 },
                new CsvColumnMapping { ColumnName = "BeginLat", Ordinal = 15 },
                new CsvColumnMapping { ColumnName = "BeginLon", Ordinal = 16 },
                new CsvColumnMapping { ColumnName = "EndLat", Ordinal = 17 },
                new CsvColumnMapping { ColumnName = "EndLon", Ordinal = 18 },
                new CsvColumnMapping { ColumnName = "EpisodeNarrative", Ordinal = 19 },
                new CsvColumnMapping { ColumnName = "EventNarrative", Ordinal = 20 },
                new CsvColumnMapping { ColumnName = "StormSummary", Ordinal = 21 },
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="queue-a-message-for-ingestion"></a>Köa ett meddelande för inmatning

Köa ett meddelande för att hämta data från Blob Storage och mata in data i ADX.

```csharp
var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
var ingestConnectionStringBuilder =
    new KustoConnectionStringBuilder(ingestUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };

using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder))
{
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.csv,
            CSVMappingReference = tableMapping,
            IgnoreFirstRecord = true
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
}
```

## <a name="validate-data-was-ingested-into-the-table"></a>Verifiera att data har matats in i tabellen

Vänta fem till tio minuter medan den köade datainmatningen schemaläggs för inmatning och läser in data i ADX. Kör sedan följande kod för att hämta posterna i tabellen `StormEvents`.

```csharp
using (var cslQueryProvider = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
{
    var query = $"{table} | count";

    var results = cslQueryProvider.ExecuteQuery<long>(query);
    Console.WriteLine(results.Single());
}
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
