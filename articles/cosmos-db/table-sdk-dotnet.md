---
title: Azure CosmosDB tabell API .NET SDK & resurser | Microsoft Docs
description: Läs mer om Azure Cosmos DB tabell API inklusive frisläppningsdatum, tillbakadragning datum och ändringar mellan varje version.
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/26/2018
ms.author: mimig
ms.openlocfilehash: 2afd7df65e7b223845752fc6bea5bc0ab4d3efd8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB tabell .NET-API: Hämta och viktig information
> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK-hämtningen**|[NuGet](https://aka.ms/acdbtablenuget)|
|**API-dokumentationen**|[.NET API-referensdokumentation](https://aka.ms/acdbtableapiref)|
|**Snabbstart**|[Azure Cosmos DB: Skapa en app med .NET och tabellen API](create-table-dotnet.md)|
|**Självstudie**|[Azure Cosmos DB: Utveckla med tabell-API i .NET](tutorial-develop-table-dotnet.md)|
|**Aktuella framework som stöds**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> Om du skapade ett tabell-API-konto under förhandsversionen ska du skapa ett [nytt tabell-API-konto](create-table-dotnet.md#create-a-database-account) som fungerar med de allmänt tillgängliga SDK:erna för API-tabellen.
>

## <a name="release-notes"></a>Viktig information

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Tillagda verifieringen av felaktig ETAGs i direkt läge.
* Fast programfel på grund av LINQ-fråga i Gateway-läge.
* Synkron API: er körs på trådpoolen med SynchronizationContext.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Lägga till TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism och TableQueryContinuationTokenLimitInKb TableRequestOptions
* Buggfixar

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Versionen för allmän tillgänglighet

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-Preview
* Inledande förhandsversion

## <a name="release-and-retirement-dates"></a>Versionen och tillbakadragning datum
Microsoft meddelar minst **12 månader** innan du tar bort en SDK för att utjämna övergången till en nyare/stöds version.

Den [WindowsAzure.Storage PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) preview paketet har föråldrad och ersättas med den [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) paketet. WindowsAzure.Storage PremiumTable-SDK ska tas bort på 15 November 2018, vid vilken tidpunkt begäranden till tillbakadragna SDK: N tillåts inte.

Nya funktioner och funktionalitet och optimeringar bara lägga till den aktuella SDK, som vi rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så snart som möjligt. 

Alla begäranden till Azure Cosmos-databasen med en pensionerad SDK avvisas av tjänsten.
<br/>

| Version | Utgivningsdatum | Datumet för tillbakadragandet |
| --- | --- | --- |
| [1.1.1](#1.1.1) |26 mars 2018|--- |
| [1.1.0](#1.1.0) |21 februari 2018|--- |
| [1.0.0](#1.0.0) |15 november 2017|--- |
| [0.9.0-Preview](#0.9.0-preview) |11 november 2017 |--- |

## <a name="troubleshooting"></a>Felsökning

Om du får felet 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

När du försöker använda Microsoft.Azure.CosmosDB.Table NuGet-paketet har två alternativ för att åtgärda problemet:

* Använd paketet hantera konsolen för att installera paketet Microsoft.Azure.CosmosDB.Table och dess beroenden. Om du vill göra det, skriver du följande i Package Manager-konsolen för din lösning. 
    ```
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```
    
* Använd din önskade hanteringsverktyg för Nuget-paketet installera Microsoft.Azure.Storage.Common Nuget-paketet innan du installerar Microsoft.Azure.CosmosDB.Table.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Läs mer om Azure Cosmos DB tabell API i [introduktion till Azure Cosmos DB tabell API](table-introduction.md). 
