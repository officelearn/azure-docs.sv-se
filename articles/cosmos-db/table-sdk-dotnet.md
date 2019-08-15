---
title: Azure Cosmos DB Tabell-API .NET SDK & resurser
description: Läs mer om Azure Cosmos DB Table API inklusive frisläppningsdatum, dras tillbaka datum och ändringar som gjorts mellan varje version.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.openlocfilehash: aa9752cdc2148036b3edff8a6002dc81ebff6312
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036312"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB tabell .NET-API: Information om hämtning och version

> [!div class="op_single_selector"]
> * [NET](table-sdk-dotnet.md)
> * [.NET-standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Hämta SDK**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Snabbstart**|[Azure Cosmos DB: Bygg en app med .NET och Tabell-API](create-table-dotnet.md)|
|**Självstudie**|[Azure Cosmos DB: Utveckla med Tabell-API i .NET](tutorial-develop-table-dotnet.md)|
|**Aktuella framework som stöds**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> .NET Framework SDK [Microsoft. Azure. CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) är i underhålls läge och kommer snart att vara inaktuell. Uppgradera till det nya .NET standard-biblioteket [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) för att fortsätta hämta de senaste funktionerna som stöds av tabell-API.

> Om du skapade ett tabell-API-konto under förhandsversionen ska du skapa ett [nytt tabell-API-konto](create-table-dotnet.md#create-a-database-account) som fungerar med de allmänt tillgängliga SDK:erna för API-tabellen.
>

## <a name="release-notes"></a>Viktig information

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Felkorrigeringar

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Stöd för skrivning till flera regioner har lagts till
* Fasta NuGet-paket beroenden på Microsoft. Azure. DocumentDB, Microsoft. OData. Core, Microsoft. OData. EDM, Microsoft. spatial

### <a name="a-name113113"></a><a name="1.1.3"/>1.1.3

* Fasta NuGet-paket beroenden på Microsoft. Azure. Storage. common och Microsoft. Azure. DocumentDB.
* Fel korrigeringar i tabell serialisering när JsonConvert. DefaultSettings har kon figurer ATS.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Verifiering har lagts till för felaktigt utformade ETAGs i direkt läge.
* Fel i den fasta LINQ-frågan i Gateway-läge.
* Synkrona API: er körs nu på trådpoolen med SynchronizationContext.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Lägg till TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism och TableQueryContinuationTokenLimitInKb i TableRequestOptions
* Fel korrigeringar

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Allmänt tillgänglig version

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0 – för hands version

* Inledande förhandsversion

## <a name="release-and-retirement-dates"></a>Datum för lansering och indragning

Microsoft tillhandahåller ett meddelande minst **12 månader** i förväg för att dra tillbaka en SDK för att utjämna över gången till en nyare/version som stöds.

`Microsoft.Azure.CosmosDB.Table` Biblioteket är för närvarande endast tillgängligt för .NET Framework och är i underhålls läge och kommer att bli inaktuellt snart. Nya funktioner och funktioner och optimeringar läggs bara till i .NET standard-biblioteket [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), så vi rekommenderar att du uppgraderar till [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).

[Windowsazure. Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) för hands versions paketet är inaktuell. WindowsAzure. Storage-PremiumTable SDK kommer att dras tillbaka den 15 november 2018, där det inte är tillåtet att begära förfrågningar till det tillbakadragna SDK: n. 

Förfrågningar till Azure Cosmos DB med hjälp av en tillbakadragen SDK avvisas av tjänsten.
<br/>

| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| [2.1.0](#2.1.0) |Den 22 januari 2019|01 april 2020 |
| [2.0.0](#2.0.0) |26 september 2018|01 mars 2020 |
| [1.1.3](#1.1.3) |17 juli 2018|01 december 2019 |
| [1.1.1](#1.1.1) |26 mars 2018|01 december 2019 |
| [1.1.0](#1.1.0) |21 februari 2018|01 december 2019 |
| [1.0.0](#1.0.0) |Den 15 november 2017|15 november 2019 |
| 0.9.0 – för hands version |11 november 2017 |11 november 2019 |

## <a name="troubleshooting"></a>Felsökning

Om du får felet 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

När du försöker använda paketet Microsoft. Azure. CosmosDB. table NuGet har du två alternativ för att åtgärda problemet:

* Använd paket hanterings konsolen för att installera Microsoft. Azure. CosmosDB. table-paketet och dess beroenden. Det gör du genom att skriva följande i Package Manager-konsolen för din lösning. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Använd det önskade verktyget för hantering av NuGet-paket och installera paketet Microsoft. Azure. Storage. common NuGet innan du installerar Microsoft. Azure. CosmosDB. Table.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också

Mer information om Azure Cosmos DB Tabell-API finns i [Introduktion till Azure Cosmos DB tabell-API](table-introduction.md). 