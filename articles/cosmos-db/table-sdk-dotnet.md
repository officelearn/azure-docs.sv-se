---
title: Azure Cosmos DB Table API .NET SDK och resurser
description: Läs mer om Azure Cosmos DB Table API inklusive frisläppningsdatum, dras tillbaka datum och ändringar som gjorts mellan varje version.
services: cosmos-db
author: rnagpal
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.author: rnagpal
ms.openlocfilehash: 7092daa7cf3a1cfcbeac06db308fa53e18d27131
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52878592"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB-tabell .NET API: Ladda ned och viktig information
> [!div class="op_single_selector"]
> * [NET](table-sdk-dotnet.md)
> * [.NET-standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Hämta SDK**|[NuGet](https://aka.ms/acdbtablenuget)|
|**API-dokumentation**|[.NET API-referensdokumentation](https://aka.ms/acdbtableapiref)|
|**Snabbstart**|[Azure Cosmos DB: Skapa en app med .NET och tabell-API](create-table-dotnet.md)|
|**Självstudie**|[Azure Cosmos DB: Utveckla med tabell-API i .NET](tutorial-develop-table-dotnet.md)|
|**Aktuella framework som stöds**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> Om du skapade ett tabell-API-konto under förhandsversionen ska du skapa ett [nytt tabell-API-konto](create-table-dotnet.md#create-a-database-account) som fungerar med de allmänt tillgängliga SDK:erna för API-tabellen.
>

## <a name="release-notes"></a>Viktig information

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Stöd har lagts till flera regioner skrivning
* Fast NuGet-paketberoenden på Microsoft.Azure.DocumentDB, Microsoft.OData.Core, Microsoft.OData.Edm, Microsoft.Spatial

### <a name="a-name113113"></a><a name="1.1.3"/>1.1.3
* Fast NuGet-paketberoenden på Microsoft.Azure.Storage.Common och Microsoft.Azure.DocumentDB.
* Felkorrigeringar på tabellen serialisering när JsonConvert.DefaultSettings konfigureras.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Har lagts till verifieringen av felaktig ETAGs i direkt-läge.
* Åtgärdat fel på grund av LINQ-frågan i Gateway-läge.
* Synkron API: er kan nu köra på trådpoolen med SynchronizationContext.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Lägga till TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism och TableQueryContinuationTokenLimitInKb i TableRequestOptions
* Felkorrigeringar

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Allmänt tillgänglig version

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-Preview
* Inledande förhandsversion

## <a name="release-and-retirement-dates"></a>Versionen och dras tillbaka datum
Microsoft meddelar minst **12 månader** förväg dra tillbaka en SDK för att utjämna övergången till en nyare/stöds version.

Den [WindowsAzure.Storage PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) förhandsversion paketet har inaktuell och ersatts med den [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) paketet. WindowsAzure.Storage-PremiumTable SDK kommer att dras tillbaka den 15 November 2018, vid vilken tid förfrågningar om att dras tillbaka SDK: N tillåts inte. Den `Microsoft.Azure.CosmosDB.Table` bibliotek är för närvarande tillgängligt för .NET Standard endast, den ännu inte är tillgängliga för .NET Core.

Nya funktioner och funktionalitet och optimeringar läggs endast till den aktuella SDK, så vi rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så tidigt som möjligt. 

Alla begäranden till Azure Cosmos DB med hjälp av en pensionerad SDK avvisas av tjänsten.
<br/>

| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| [1.1.3](#1.1.3) |17 juli 2018|--- |
| [1.1.1](#1.1.1) |26 mars 2018|--- |
| [1.1.0](#1.1.0) |21 februari 2018|--- |
| [1.0.0](#1.0.0) |Den 15 november 2017|--- |
| [0.9.0-Preview](#0.9.0-preview) |11 november 2017 |--- |

## <a name="troubleshooting"></a>Felsökning

Om du får felet 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

När du försöker använda Microsoft.Azure.CosmosDB.Table NuGet-paketet har du två alternativ för att åtgärda problemet:

* Använd paketet hantera konsolen för att installera Microsoft.Azure.CosmosDB.Table-paketet och dess beroenden. Om du vill göra detta, skriver du följande i Package Manager-konsolen för din lösning. 
    ```
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```
    
* Använder din önskade hanteringsverktyg för NuGet-paketet, installera Microsoft.Azure.Storage.Common NuGet-paketet innan du installerar Microsoft.Azure.CosmosDB.Table.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Läs mer om Azure Cosmos DB Table API i [introduktion till Azure Cosmos DB Table API](table-introduction.md). 
