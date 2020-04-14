---
title: Azure Cosmos DB Table API .NET Standard SDK & resurser
description: Lär dig allt om Azure Cosmos DB Table API och .NET Standard SDK inklusive utgivningsdatum, återsändningsdatum och ändringar som gjorts mellan varje version.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: f7c6829d858e36b55f079f8ee03e431f67995e66
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272584"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB Tabell .NET Standard API: Hämta och viktig information
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK nedladdning**|[NuGet (nuget)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Prov**|[Cosmos DB Table API .NET-exempel](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Snabbstart**|[Snabbstart](create-table-dotnet.md)|
|**Självstudier**|[Självstudier](tutorial-develop-table-dotnet.md)|
|**Nuvarande ramverk som stöds**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|
|**Rapportproblem**|[Rapportproblem](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>Viktig information för 2.0.0-serien
2.0.0-serien tar beroendet av [Microsoft.Azure.Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), med prestandaförbättringar och namnområdeskonsolidering till Cosmos DB-slutpunkt.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-förhandsvisning
* första förhandsversionen av 2.0.0 Table SDK som tar beroendet av [Microsoft.Azure.Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), med prestandaförbättringar och namnområdeskonsolidering till Cosmos DB-slutpunkt. Det offentliga API:et förblir detsamma.

## <a name="release-notes-for-100-series"></a>Viktig information för 1.0.0-serien
1.0.0-serien tar beroendet av [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/).

### <a name="107"></a><a name="1.0.7"/>1.0.7
* Prestandaförbättring genom att ange tabell SDK-standardspårningsnivå till SourceLevels.Off, som kan väljas in via app.config

### <a name="105"></a><a name="1.0.5"/>1.0.5
* Introducera ny config under TableClientConfiguration att använda Rest Executor för att kommunicera med Cosmos DB Table API

### <a name="105-preview"></a><a name="1.0.5-preview"/>1.0.5-förhandsvisning
* Felkorrigeringar

### <a name="104"></a><a name="1.0.4"/>1.0.4
* Felkorrigeringar
* Ange alternativet HttpClientTimeout för RestExecutorConfiguration.

### <a name="104-preview"></a><a name="1.0.4-preview"/>1.0.4-förhandsvisning
* Felkorrigeringar
* Ange alternativet HttpClientTimeout för RestExecutorConfiguration.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Felkorrigeringar

### <a name="100"></a><a name="1.0.0"/>1.0.0
* Allmän tillgänglighetsfrigång

### <a name="0110-preview"></a><a name="0.11.0-preview"/>0.11.0-förhandsvisning
* Ändringar har gjorts i hur CloudTableClient kan konfigureras. Det tar nu ett tableclientConfiguration-objekt under konstruktionen. TableClientConfiguration innehåller olika egenskaper för att konfigurera klientbeteendet beroende på om målslutpunkten är Cosmos DB Table API eller Azure Storage Table API.
* Lade till stöd i TableQuery för att returnera resultat i sorterad ordning på en anpassad kolumn. Den här funktionen stöds bara på Cosmos DB Table-slutpunkter.
* Lade till stöd för att exponera RequestCharges på olika resultattyper. Den här funktionen stöds bara på Cosmos DB Table-slutpunkter.

### <a name="0101-preview"></a><a name="0.10.1-preview"/>0.10.1-förhandsvisning
* Lägg till stöd för SAS-token, åtgärder för TablePermissions, ServiceProperties och ServiceStats mot slutpunkter för Azure Storage Table. 
   > [!NOTE]
   > Vissa funktioner i tidigare Azure Storage Table SDK-filer stöds ännu inte, till exempel kryptering på klientsidan.

### <a name="0100-preview"></a><a name="0.10.0-preview"/>0.10.0-förhandsvisning
* Lägg till stöd för kärn-CRUD-, batch- och frågeåtgärder mot slutpunkter för Azure Storage Table. 
   > [!NOTE]
   > Vissa funktioner i tidigare Azure Storage Table SDK-filer stöds ännu inte, till exempel kryptering på klientsidan.

### <a name="091-preview"></a><a name="0.9.1-preview"/>0.9.1-förhandsvisning
* Azure Cosmos DB Table .NET Standard SDK är ett PLATTFORMSO-bibliotek som ger effektiv åtkomst till tabelldatamodellen på Cosmos DB. Den här första versionen stöder hela uppsättningen crud- och entitets-CRUD + Frågefunktioner med liknande API:er som [Cosmos DB Table SDK for .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Slutpunkter för Azure Storage Table stöds ännu inte i 0.9.1-förhandsversionen.

## <a name="release-and-retirement-dates"></a>Utgivnings- och pensionsdatum
Microsoft meddelar minst **12 månader** innan en SDK går i pension för att underlätta övergången till en nyare/stödd version.

Det här cross-platform .NET Standard-biblioteket [Microsoft.Azure.Cosmos.Table ersätter](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) .NET Framework-biblioteket [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

### <a name="200-series"></a>2.0.0-serien
| Version | Utgivningsdatum | Pensionering Datum |
| --- | --- | --- |
| [2.0.0-förhandsvisning](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>1.0.0-serien
| Version | Utgivningsdatum | Pensionering Datum |
| --- | --- | --- |
| [1.0.5](#1.0.5) |den 13 september 2019 |--- |
| [1.0.5-förhandsvisning](#1.0.5-preview) |Auguest 20, 2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4-förhandsvisning](#1.0.4-preview) |den 26 juli 2019 |--- |
| 1.0.2-förhandsvisning |den 2 maj 2019 |--- |
| [1.0.1](#1.0.1) |den 19 april 2019 |--- |
| [1.0.0](#1.0.0) |den 13 mars 2019 |--- |
| [0.11.0-förhandsvisning](#0.11.0-preview) |den 5 mars 2019 |--- |
| [0.10.1-förhandsvisning](#0.10.1-preview) |22 januari 2019 |--- |
| [0.10.0-förhandsvisning](#0.10.0-preview) |den 18 december 2018 |--- |
| [0.9.1-förhandsvisning](#0.9.1-preview) |den 18 oktober 2018 |--- |


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se även
Mer information om Azure Cosmos DB Table API finns i [Introduktion till Azure Cosmos DB Table API](table-introduction.md).
