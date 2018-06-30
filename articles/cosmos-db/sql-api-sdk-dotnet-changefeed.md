---
title: 'Azure Cosmos DB: .NET ändra Feed Processor API, SDK & resurser | Microsoft Docs'
description: Läs mer om ändringen Feed Processor-API och SDK inklusive frisläppningsdatum, tillbakadragning datum och ändringar mellan varje version av .NET-ändra Feed Processor-SDK.
services: cosmos-db
author: ealsur
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/21/2018
ms.author: maquaran
ms.openlocfilehash: f47b847b3a356540e5f366235713b8f99aea3404
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113725"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET ändra Feed Processor SDK: Hämta och viktig information
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET ändra Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**SDK-hämtningen**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API-dokumentationen**|[Ändra Feed Processor biblioteket API-referensdokumentation](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Kom igång**|[Kom igång med ändringen Feed Processor .NET SDK](change-feed.md)|
|**Aktuella framework som stöds**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Viktig information

### <a name="v2-builds"></a>v2-versioner

### <a name="a-name204204"></a><a name="2.0.4"/>2.0.4
* GA-SDK

### <a name="a-name203-prerelease203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3-prerelease
* Åtgärdat följande problem:
  * När partition dela händer, kan det finnas dubbla bearbetning av dokument som har ändrats innan delningen.
  * GetEstimatedRemainingWork API returnerade 0 när det fanns inga lån i samlingen lån.

* Följande undantag görs offentliga. Tillägg som implementerar IPartitionProcessor orsaka sådana undantag.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="a-name202-prerelease202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-prerelease
* Mindre ändringar i API:
  * Ta bort ChangeFeedProcessorOptions.IsAutoCheckpointEnabled som har markerats som föråldrade.

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-prerelease
* Förbättringar av stabiliteten:
  * Bättre hantering av lånet initiering av certifikatarkiv. När lånet store är tom, bara en instans av processorn kan initiera den väntar övriga.
  * Mer stabilt effektivt lån förnyelse och utgåva. Förnya och lanserar en lån en partition är oberoende av förnyar andra. I v1 som gjordes i tur och ordning för alla partitioner.
* Nya v2 API:
  * Builder mönster för flexibel konstruktionen processorn: ChangeFeedProcessorBuilder-klassen.
    * Det kan ta en kombination av parametrar.
    * Det kan ta DocumentClient instans för övervakning och/eller lån samling (inte tillgängligt i v1).
  * IChangeFeedObserver.ProcessChangesAsync tar nu CancellationToken.
  * IRemainingWorkEstimator - återstående arbete exteriörbedömning kan användas separat från processorn.
  * Nya punkter:
    * IParitionLoadBalancingStrategy - för anpassade belastningsutjämning av partitioner mellan olika instanser av processorn.
    * ILease ILeaseManager - för hantering av anpassade lån.
    * IPartitionProcessor - anpassad bearbetning ändringar på en partition.
* Loggning - använder [LibLog](https://github.com/damianh/LibLog) bibliotek.
* 100% bakåtkompatibel med API: n v1.
* Nya kodbas.
* Kompatibel med [SQL .NET SDK](sql-api-sdk-dotnet.md) versioner 1.21.1 och högre.

### <a name="v1-builds"></a>V1-versioner

### <a name="a-name133133"></a><a name="1.3.3"/>1.3.3
* Lägga till mer loggning.
* Fast en DocumentClient läcka vid anrop av pågående arbete uppskattning flera gånger.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* Korrigeringar i den pågående arbete beräkning av.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* Stabilitetsförbättringar.
  * Lösning för hantering av avbrutna aktiviteter problem som kan leda till stoppades observatörerna för några partitioner.
* Stöd för manuell kontrollpunkter.
* Kompatibel med [SQL .NET SDK](sql-api-sdk-dotnet.md) versioner 1.21 och högre.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Lägger till stöd för .NET Standard 2.0. Paketet stöder nu `netstandard2.0` och `net451` framework monikrar.
* Kompatibel med [SQL .NET SDK](sql-api-sdk-dotnet.md) versioner 1.17.0 och högre.
* Kompatibel med [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) versioner 1.5.1 och högre.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Åtgärdar problemet med beräkningen av uppskattning av återstående när ändringen Feed var tom eller inget arbete pågick.
* Kompatibel med [SQL .NET SDK](sql-api-sdk-dotnet.md) versioner 1.13.2 och högre.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Lägga till en metod för att få en uppskattning av återstående arbete som ska bearbetas i ändra Feed.
* Kompatibel med [SQL .NET SDK](sql-api-sdk-dotnet.md) versioner 1.13.2 och högre.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA-SDK
* Kompatibel med [SQL .NET SDK](sql-api-sdk-dotnet.md) versioner 1.14.1 och nedan.


## <a name="release--retirement-dates"></a>Versionen & pensionering datum
Microsoft meddelar notification minst **12 månader** innan du tar bort en SDK för att utjämna övergången till en nyare/stöds version.

Nya funktioner och funktionalitet och optimeringar bara lägga till den aktuella SDK, som vi rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så snart som möjligt. 

Alla förfrågningar till Cosmos-databasen med en pensionerad SDK avvisas av tjänsten.

<br/>

| Version | Utgivningsdatum | Datumet för tillbakadragandet |
| --- | --- | --- |
| [1.3.3](#1.3.3) |08 kan 2018 |--- |
| [1.3.2](#1.3.2) |18 april 2018 |--- |
| [1.3.1](#1.3.1) |13 mars 2018 |--- |
| [1.2.0](#1.2.0) |31 oktober 2017 |--- |
| [1.1.1](#1.1.1) |Den 29 augusti 2017 |--- |
| [1.1.0](#1.1.0) |13 augusti 2017 |--- |
| [1.0.0](#1.0.0) |07 juli 2017 |--- |


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Läs mer om Cosmos-DB i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) sida. 

