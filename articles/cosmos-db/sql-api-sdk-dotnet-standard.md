---
title: 'Azure Cosmos DB: SQL .NET standard-API, SDK &-resurser'
description: Lär dig allt om SQL API och .NET SDK, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB .NET SDK.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 07/12/2019
ms.author: dech
ms.openlocfilehash: 0368e99135dd6e377dd2820b3e673c55182319b9
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663814"
---
# <a name="azure-cosmos-db-net-standard-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET standard SDK för SQL API: Information om hämtning och version
> [!div class="op_single_selector"]
> * [.NET-standard](sql-api-sdk-dotnet-standard.md)
> * [NET](sql-api-sdk-dotnet.md)
> * [.NET-Ändringsfeed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Mass utförar – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Mass utförar – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Hämta SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)|
|**API-dokumentation**|[.NET API-referensdokumentation](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Exempel**|[.NET-kodexempel](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)|
|**Kom igång**|[Kom igång med Azure Cosmos DB .NET SDK](sql-api-get-started.md)|
|**Självstudier om webbappen**|[Utveckling av webbappar med Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Aktuella framework som stöds**|[Microsoft .NET standard 2,0](/dotnet/standard/net-standard)|

## <a name="release-notes"></a>Viktig information
### <a name="a-name310310"></a><a name="3.1.0"/>3.1.0
#### <a name="added"></a>Har lagts till
- [#541](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/541) Ökad konsekvens nivå till klient-och frågealternativ
- [#544](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/544) Stöd för stöd för fortsatt token för LINQ
- [#557](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/557) Alternativ för utlösare för objekt begär ande alternativ har lagts till
- [#571](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/571) Lade till en standardJSON.net serialiserare med valfria inställningar
- [#572](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/572) Verifiering av partitionsnyckel för CreateContainerIfNotExistsAsync har lagts till
- [#581](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/581) Lägger till LINQ to egenskaps uppsättningen QueryDefinition-API
- [#592](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/592) Lade till CreateIfNotExistsAsync till container Builder
- [#597](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/597) Egenskapen för fortsättnings-token har lagts till i ResponseMessage
- [#604](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/604) Tilläggs metoden LINQ ToStreamIterator lades till

#### <a name="fixed"></a>Korrigerat
- [#548](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/548) Fast felaktigt skrivet meddelande i CosmosException. ToString ();
- [#558](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/558) LocationCache ConcurrentDict lås konkurrens
- [#561](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/561) GetItemLinqQueryable fungerar nu med null-fråga
- [#567](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/567) Frågan hanterar olika språk kulturer
- [#574](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/574) Fast tomt fel meddelande om fråga parsing Miss lyckas från ett oväntat undantag
- [#576](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/576) Frågan serialiserar indata till en data ström korrekt

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0 
* Allmän tillgänglighet för [version 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) av .NET SDK
* Riktar sig till .NET standard 2,0, som stöder .NET Framework 4.6.1 + och .NET Core 2.0 +
* Ny objekt modell med toppnivå CosmosClient och metoder som delas över relevanta databas-och container klasser
* Nya API: er för hög prestanda strömmar
* Inbyggt stöd för API: er för ändring av feed-processorer
* Fluent Builder-API: er för CosmosClient, container och byte av flödes processor
* API: er för idiomatiskt data flödes hantering
* Detaljerad RequestOptions och ResponseTypes för förfrågningar om databaser, behållare, objekt, frågor och data flöden
* Möjlighet att skala icke-partitionerade behållare 
* Utöknings bar och anpassningsbar serialiserare
* Pipeline för utöknings bar begäran med stöd för anpassade hanterare


## <a name="release--retirement-dates"></a>Frisläpp & indragnings datum
Microsoft tillhandahåller ett meddelande minst **12 månader** i förväg för att dra tillbaka en SDK för att utjämna över gången till en nyare/version som stöds.

Nya funktioner och funktioner och optimeringar läggs bara till i den aktuella SDK: n, så vi rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så tidigt som möjligt. 

Förfrågningar till Azure Cosmos DB med hjälp av en tillbakadragen SDK avvisas av tjänsten.

<br/>

| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| [3.1.0](#3.1.0) |29 juli 2019 |--- |
| [3.0.0](#3.0.0) |15 juli 2019 |--- |


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Mer information om Cosmos DB finns [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan. 

