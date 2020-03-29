---
title: Azure Cosmos DB Table API .NET SDK & resurser
description: Lär dig allt om Azure Cosmos DB Table API, inklusive utgivningsdatum, pensioneringsdatum och ändringar som görs mellan varje version.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.openlocfilehash: 5a5305ffd388d2573d250d93131c1fed236008b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76771620"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET API: Hämta och viktig information

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK nedladdning**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Quickstart**|[Azure Cosmos DB: Skapa en app med .NET och tabell-API:et](create-table-dotnet.md)|
|**Självstudier**|[Azure Cosmos DB: Utveckla med tabell-API i .NET](tutorial-develop-table-dotnet.md)|
|**Nuvarande ramverk som stöds**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) är i underhållsläge och det kommer snart att vara inaktuellt. Uppgradera till det nya .NET Standard-biblioteket [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) för att fortsätta att få de senaste funktionerna som stöds av tabell-API:et.

> Om du skapade ett tabell-API-konto under förhandsversionen ska du skapa ett [nytt tabell-API-konto](create-table-dotnet.md#create-a-database-account) som fungerar med de allmänt tillgängliga SDK:erna för API-tabellen.
>

## <a name="release-notes"></a>Viktig information

### <a name="212"></a><a name="2.1.2"/>2.1.2

* Felkorrigeringar

### <a name="210"></a><a name="2.1.0"/>2.1.0

* Felkorrigeringar

### <a name="200"></a><a name="2.0.0"/>2.0.0

* Tillagt skrivstöd för flera regioner
* Fast NuGet-paketberoenden för Microsoft.Azure.DocumentDB, Microsoft.OData.Core, Microsoft.OData.Edm, Microsoft.Spatial

### <a name="113"></a><a name="1.1.3"/>1.1.3

* Fast NuGet paketberoenden på Microsoft.Azure.Storage.Common och Microsoft.Azure.DocumentDB.
* Buggfixar vid tabell serialisering när JsonConvert.DefaultSettings är konfigurerade.

### <a name="111"></a><a name="1.1.1"/>1.1.1

* Lade till validering för felformade ETAGs i direktläge.
* Fast LINQ-frågefel i gateway-läge.
* Synkrona API:er körs nu på trådpoolen med SynchronizationContext.

### <a name="110"></a><a name="1.1.0"/>1.1.0

* Lägg till TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism och TableQueryContinuationTokenLimitInKb till TableRequestOptions
* Felkorrigeringar

### <a name="100"></a><a name="1.0.0"/>1.0.0

* Allmän tillgänglighetsfrigång

### <a name="090-preview"></a><a name="0.1.0-preview"/>0.9.0-förhandsvisning

* Inledande förhandsversion

## <a name="release-and-retirement-dates"></a>Utgivnings- och pensionsdatum

Microsoft meddelar minst **12 månader** innan en SDK går i pension för att underlätta övergången till en nyare/stödd version.

Biblioteket `Microsoft.Azure.CosmosDB.Table` är endast tillgängligt för .NET Framework och är i underhållsläge och kommer snart att vara inaktuellt. Nya funktioner och optimeringar läggs bara till i .NET Standard-biblioteket [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), som du rekommenderar att du uppgraderar till [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).

Förhandspaketet [för WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) har inaktuellt. WindowsAzure.Storage-PremiumTable SDK kommer att dras tillbaka den 15 november 2018, då begäranden till den pensionerade SDK inte tillåts. 

Alla begäranden till Azure Cosmos DB med hjälp av en pensionerad SDK avvisas av tjänsten.
<br/>

| Version | Utgivningsdatum | Pensionering Datum |
| --- | --- | --- |
| [2.1.2](#2.1.2) |den 16 september 2019| |
| [2.1.0](#2.1.0) |22 januari 2019|Den 1 april 2020 |
| [2.0.0](#2.0.0) |den 26 september 2018|Den 1 mars 2020 |
| [1.1.3](#1.1.3) |17 juli 2018|den 1 december 2019 |
| [1.1.1](#1.1.1) |den 26 mars 2018|den 1 december 2019 |
| [1.1.0](#1.1.0) |den 21 februari 2018|den 1 december 2019 |
| [1.0.0](#1.0.0) |15 november 2017|den 15 november 2019 |
| 0.9.0-förhandsvisning |den 11 november 2017 |den 11 november 2019 |

## <a name="troubleshooting"></a>Felsökning

Om du får felet 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

När du försöker använda paketet Microsoft.Azure.CosmosDB.Table NuGet har du två alternativ för att åtgärda problemet:

* Använd Package Manage Console för att installera paketet Microsoft.Azure.CosmosDB.Table och dess beroenden. Det gör du genom att skriva följande i Package Manager-konsolen för din lösning. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Installera paketet Microsoft.Azure.Storage.Common NuGet innan du installerar Microsoft.Azure.CosmosDB.Table med ditt föredragna NuGet-pakethanteringsverktyg.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se även

Mer information om Azure Cosmos DB Table API finns i [Introduktion till Azure Cosmos DB Table API](table-introduction.md). 
