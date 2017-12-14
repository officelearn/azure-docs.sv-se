---
title: Azure CosmosDB Graph API .NET SDK & resurser | Microsoft Docs
description: "Läs mer om Azure CosmosDB Graph API inklusive frisläppningsdatum, tillbakadragning datum och ändringar mellan varje version."
services: cosmos-db
documentationcenter: .net
author: luisbosquez
manager: jhubbard
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/17/2017
ms.author: lbosq
ms.openlocfilehash: 28f926d8d1f4c6006a348ba9c8289b885aef7641
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-cosmos-db-graph-net-api-download-and-release-notes"></a>Azure Cosmos DB Graph .NET API: Hämta och viktig information

|   |   |
|---|---|
|**SDK-hämtningen**|[NuGet](https://aka.ms/acdbgraphnuget)|
|**API-dokumentationen**|[.NET API-referensdokumentation](https://aka.ms/acdbgraphapiref)|
|**Snabbstart**|[Azure Cosmos DB: Skapa en graph-app med .NET och Graph-API](create-graph-dotnet.md)|
|**Självstudie**|[Azure CosmosDB: Skapa en behållare med Graph-API](tutorial-develop-graph-dotnet.md)|
|**Aktuella framework som stöds**| [Microsoft .NET Framework 4.6.1](https://www.microsoft.com/en-us/download/details.aspx?id=49981)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |


## <a name="release-notes"></a>Viktig information

### <a name="a-name031-preview031-preview"></a><a name="0.3.1-preview"/>0.3.1-Preview

#### <a name="bug-fixes"></a>Felkorrigeringar
* Åtgärda om du vill läsa in `appsettings.json` (`netstandard1.6`)

#### <a name="whats-new"></a>Nyheter
* Växla Microsoft.Azure.Graphs till målplattform AnyCPU.
* Ta bort monoljud sammansättning från `net461` paketet manifest.

### <a name="a-name030-preview030-preview"></a><a name="0.3.0-preview"/>0.3.0-Preview

#### <a name="whats-new"></a>Nyheter
* Tillagt stöd för`.netstandard 1.6`
  * Kräver`Microsoft.Azure.DocumentDB.Core >= 1.5.1`
* Lägga till en ny `gremlin-groovy` parsern för att ersätta befintliga parsern. Den här parsern stöder en delmängd av Tinkerpop's `gremlin-groovy` syntax och omfattar:
  * Bättre tolkning prestanda 2 x.
  * Matcha ett antal problem relaterade till tecknet undantagstecken i strängar, felaktigt hanterat teckenvärden och andra oegentligheter i gamla parsern.
* Tillagda optimeringar för traversals med kant-predikat.
  *  Traversal hopp med filter bör se denna förbättring, till exempel: `g.V('1').outE().has('name', 'marko').inV()`.
* Lägga till optimeringar för traversals med `limit()` steg.

#### <a name="breaking-changes"></a>Gör ändringar
* Borttagna stöd för .NET Framework 4.5.1

* Nya parsern justeras med `gremlin-groovy` grammatik. Därför är vissa uttryck som har fungerat tidigare tvetydiga för nya parsern. Ett fall av Obs:
  * `in`och `as` är reserverade nyckelord i `gremlin-groovy`, så här måste vara kvalificerat med `.in()` eller `.as()` att undvika syntaxfel. Till exempel: `g.V().repeat(in()).times(2)`  ->  _utlöser ett syntaxfel_  
 `g.V().repeat(__.in()).times(2)` -> _lyckas_

### <a name="a-name024-preview024-preview"></a><a name="0.2.4-preview"/>0.2.4-Preview

### <a name="a-name022-preview022-preview"></a><a name="0.2.2-preview"/>0.2.2-Preview

### <a name="a-name021-preview021-preview"></a><a name="0.2.1-preview"/>0.2.1-Preview

### <a name="a-name020-preview020-preview"></a><a name="0.2.0-preview"/>0.2.0-Preview

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-Preview
* Inledande förhandsversionen.

## <a name="release--retirement-dates"></a>Versionen & pensionering datum
Microsoft meddelar notification minst **12 månader** innan du tar bort en SDK för att utjämna övergången till en nyare/stöds version.

Nya funktioner och funktionalitet och optimeringar bara lägga till den aktuella SDK, som vi rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så snart som möjligt. 

Alla förfrågningar till Azure Cosmos-databasen med en pensionerad SDK avvisas av tjänsten.

<br/>

| Version | Utgivningsdatum | Datumet för tillbakadragandet |
| --- | --- | --- |
| [0.3.1-Preview](#0.3.1-preview) |17 oktober 2017 |--- |
| [0.3.0-Preview](#0.3.0-preview) |2 oktober 2017 |--- |
| [0.2.4-Preview](#0.2.4-preview) |4 augusti 2017 |--- |
| [0.2.2-Preview](#0.2.2-preview) |23 juni 2017 |--- |
| [0.2.1-Preview](#0.2.2-preview) |8 juni 2017 |--- |
| [0.2.0-Preview](#0.2.2-preview) |10 maj 2017 |--- |
| [0.1.0-Preview](#0.1.0-preview) |8 kan 2017 |--- |

## <a name="see-also"></a>Se även
Läs mer om Azure Cosmos DB Graph API i [introduktion till Azure Cosmos DB: Graph API](graph-introduction.md). 
