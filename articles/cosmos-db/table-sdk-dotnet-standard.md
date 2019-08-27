---
title: Azure Cosmos DB Tabell-API .NET standard SDK & resurser
description: Lär dig allt om Azure Cosmos DB Tabell-API och .NET standard SDK, inklusive versions datum, datum för indragningen och ändringar som gjorts mellan varje version.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: 8fd3aedd2e371e511730fd98751ae3214545c489
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034586"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB tabell .NET standard-API: Information om hämtning och version
> [!div class="op_single_selector"]
> 
> * [NET](table-sdk-dotnet.md)
> * [.NET-standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Hämta SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Exempel**|[Cosmos DB Tabell-API .NET-exempel](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Snabbstart**|[Snabbstart](create-table-dotnet.md)|
|**Självstudie**|[Självstudie](tutorial-develop-table-dotnet.md)|
|**Aktuella framework som stöds**|[Microsoft .NET standard 2,0](https://www.nuget.org/packages/NETStandard.Library)|
|**Rapportera problem**|[Rapportera problem](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>Viktig information för 2.0.0-serien
2.0.0-serien använder sig av beroendet av [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), med prestanda förbättringar och namn områdes konsolidering för att Cosmos DB slut punkten.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview
* första för hands versionen av 2.0.0 Table SDK som tar beroendet av [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), med prestanda förbättringar och namn områdes konsolidering för att Cosmos DB slut punkten. Det offentliga API: t är oförändrat.

## <a name="release-notes-for-100-series"></a>Viktig information för 1.0.0-serien
1.0.0-serien använder sig av beroendet av [Microsoft. Azure. DocumentDB. Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/).

### <a name="a-name105-preview105-preview"></a><a name="1.0.5-preview"/>1.0.5 – för hands version
* Felkorrigeringar

### <a name="a-name104104"></a><a name="1.0.4"/>1.0.4
* Felkorrigeringar
* Ange alternativet HttpClientTimeout för RestExecutorConfiguration.

### <a name="a-name104-preview104-preview"></a><a name="1.0.4-preview"/>1.0.4 – för hands version
* Felkorrigeringar
* Ange alternativet HttpClientTimeout för RestExecutorConfiguration.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Felkorrigeringar

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Allmänt tillgänglig version

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0 – för hands version
* Ändringar har gjorts i hur CloudTableClient kan konfigureras. Det tar nu ett TableClientConfiguration-objekt under konstruktion. TableClientConfiguration tillhandahåller olika egenskaper för att konfigurera klient beteendet beroende på om mål slut punkten är Cosmos DB Tabell-API eller Azure Storage Tabell-API.
* Stöd har lagts till för TableQuery för att returnera resultat i sorterad ordning i en anpassad kolumn. Den här funktionen stöds bara för Cosmos DB tabell slut punkter.
* Stöd har lagts till för att exponera RequestCharges på olika resultat typer. Den här funktionen stöds bara för Cosmos DB tabell slut punkter.

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1 – för hands version
* Lägg till stöd för SAS-token, åtgärder för TablePermissions, ServiceProperties och ServiceStats mot Azure Storage tabell slut punkter. 
   > [!NOTE]
   > Vissa funktioner i tidigare Azure Storage tabell-SDK: er stöds inte än, t. ex. kryptering på klient sidan.

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0 – för hands version
* Lägg till stöd för grundläggande CRUD-, batch-och fråge åtgärder mot Azure Storage tabell slut punkter. 
   > [!NOTE]
   > Vissa funktioner i tidigare Azure Storage tabell-SDK: er stöds inte än, t. ex. kryptering på klient sidan.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1 till och – för hands version
* Azure Cosmos DB Table .NET standard SDK är ett plattforms oberoende .NET-bibliotek som ger effektiv åtkomst till tabell data modellen på Cosmos DB. Den här inledande versionen stöder en fullständig uppsättning tabell-och entitets-CRUD + fråge funktioner med liknande API: er som [Cosmos DB Table SDK för .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Azure Storage tabell slut punkter stöds inte ännu i 0.9.1 till och-Preview-versionen.

## <a name="release-and-retirement-dates"></a>Datum för lansering och indragning
Microsoft tillhandahåller ett meddelande minst **12 månader** i förväg för att dra tillbaka en SDK för att utjämna över gången till en nyare/version som stöds.

Detta plattforms oberoende .NET standard-bibliotek [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) kommer att ersätta .NET Framework-biblioteket [Microsoft. Azure. CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

### <a name="200-series"></a>2.0.0-serien
| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| [2.0.0 – för hands version](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>1.0.0-serien
| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| [1.0.5 – för hands version](#1.0.5-preview) |Auguest 20, 2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4 – för hands version](#1.0.4-preview) |26 juli 2019 |--- |
| 1.0.2 – för hands version |2 maj 2019 |--- |
| [1.0.1](#1.0.1) |19 april 2019 |--- |
| [1.0.0](#1.0.0) |13 mars 2019 |--- |
| [0.11.0 – för hands version](#0.11.0-preview) |5 mars 2019 |--- |
| [0.10.1 – för hands version](#0.10.1-preview) |Den 22 januari 2019 |--- |
| [0.10.0 – för hands version](#0.10.0-preview) |18 december 2018 |--- |
| [0.9.1 till och – för hands version](#0.9.1-preview) |18 oktober 2018 |--- |


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Mer information om Azure Cosmos DB Tabell-API finns i [Introduktion till Azure Cosmos DB tabell-API](table-introduction.md).
