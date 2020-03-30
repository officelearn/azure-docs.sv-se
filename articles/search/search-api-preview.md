---
title: Förhandsgranska funktioner i REST API
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search service REST API Version 2019-05-06-Preview innehåller experimentella funktioner som kunskapsbutik och indexeringscachelagring för inkrementell anrikning.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162284"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Förhandsgranska funktioner i Azure Cognitive Search

I den här artikeln visas funktioner som för närvarande förhandsgranskas. Funktioner som övergår från förhandsversion till allmän tillgänglighet tas bort från den här listan. Du kan kontrollera [serviceuppdateringar](https://azure.microsoft.com/updates/?product=search) eller [Nyheter](whats-new.md) för meddelanden om allmän tillgänglighet.

Vissa förhandsgranskningsfunktioner kan vara tillgängliga i portalen och .NET SDK, men REST API har alltid förhandsgranskningsfunktioner.

+ För sökåtgärder [**`2019-05-06-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) är den aktuella förhandsversionen.
+ För hanteringsåtgärder [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) är den aktuella förhandsversionen.

> [!IMPORTANT]
> Förhandsversionsfunktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ai-enrichment-features"></a>Funktioner för AI-anrikning

Utforska de senaste förbättringarna av AI-anrikning via [förhandsversionen av sök-API: et](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview).

|||
|-|-|
| [Anpassad entitetsuppslagningsfärdighet (förhandsgranskning)](cognitive-search-skill-custom-entity-lookup.md ) | En kognitiv färdighet som söker efter text från en anpassad, användardefinierad lista med ord och fraser. Med den här listan etiketterar den alla dokument med alla matchande entiteter. Färdigheten stöder också en grad av luddig matchning som kan tillämpas för att hitta matcher som är liknande men inte riktigt exakt. | 
| [PII-identifieringsfärdighet (förhandsgranskning)](cognitive-search-skill-pii-detection.md) | En kognitiv färdighet som används vid indexering som extraherar personligt identifierbar information från en indatatext och ger dig möjlighet att maskera den från den texten på olika sätt.| 
| [Inkrementell anrikning (förhandsgranskning)](cognitive-search-incremental-indexing-conceptual.md) | Lägger till cachelagring i en anrikningspipeline, så att du kan återanvända befintligt utdata om en riktad ändring, till exempel en uppdatering av en kompetens eller ett annat objekt, inte ändrar innehållet. Cachelagring gäller endast berikade dokument som produceras av en kompetens.| 
| [Knowledge Store (förhandsgranskning)](knowledge-store-concept-intro.md) | En ny destination för en AI-baserad anrikningspipeline. Den fysiska datastrukturen finns i Azure Blob storage och Azure Table Storage, och den skapas och fylls i när du kör en indexerare som har en ansluten kognitiv kompetens. Definitionen av ett kunskapslager själv anges i en kompetensdefinition. Inom definitionen för kunskapslager styr du de fysiska strukturerna för dina data genom *projektionselement* som avgör hur data utformas, om data lagras i Tabelllagring eller Blob-lagring och om det finns flera vyer.| 

## <a name="indexing-and-query-features"></a>Indexerings- och frågefunktioner

Förhandsgranskningsfunktioner för Indexer är tillgängliga i förhandsversionen av sök-API: er. 

|||
|-|-|
| [Cosmos DB-indexeraren](search-howto-index-cosmosdb.md) | Stöd för MongoDB API (förhandsversion), Gremlin API (förhandsversion) och Cassandra API -API-typer (förhandsversion). | 
|  [Azure Data Lake Storage Gen2-indexerare (förhandsgranskning)](search-howto-index-azure-data-lake-storage.md) | Indexera innehåll och metadata från Data Lake Storage Gen2.| 
| [merSomDenna frågeparameter (förhandsgranskning)](search-more-like-this.md) | Söker efter dokument som är relevanta för ett visst dokument. Den här funktionen har varit i tidigare förhandsvisningar. | 

## <a name="management-features"></a>Hanteringsfunktioner

|||
|-|-|
| [Stöd för privata slutpunkter](service-create-private-endpoint.md) | Du kan skapa ett virtuellt nätverk med en säker klient (till exempel en virtuell dator) och sedan skapa en söktjänst som använder privat slutpunkt. |
| Begränsning av IP-åtkomst | Med [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) hjälp av REST-API:et för hantering kan du skapa en tjänst som har begränsningar för vilka IP-adresser som tillåts åtkomst till. |

## <a name="earlier-preview-features"></a>Tidigare förhandsgranskningsfunktioner

Funktioner som meddelats i tidigare förhandsversioner, om de inte har övergått till allmän tillgänglighet, är fortfarande i offentlig förhandsversion. Om du anropar ett API med en tidigare api-version för förhandsversionen `2019-05-06-Preview` kan du fortsätta att använda den versionen eller växla till utan ändringar av förväntat beteende.

## <a name="how-to-call-a-preview-api"></a>Så här anropar du ett api för förhandsgranskning

Äldre förhandsgranskningar är fortfarande i drift men blir inaktuella med tiden. Om koden `api-version=2016-09-01-Preview` ringer `api-version=2017-11-11-Preview`eller är dessa samtal fortfarande giltiga. Endast den senaste förhandsversionen uppdateras dock med förbättringar. 

Följande exempelsyntax illustrerar ett anrop till api-versionen för förhandsversionen.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure Cognitive Search-tjänsten är tillgänglig i flera versioner. Mer information finns i [API-versioner](search-api-versions.md).

## <a name="next-steps"></a>Nästa steg

Granska referensdokumentationen för sök-REST API. Om du stöter på problem kan du be oss om hjälp med [StackOverflow](https://stackoverflow.com/) eller [kontakta supporten.](https://azure.microsoft.com/support/community/?product=search)

> [!div class="nextstepaction"]
> [REST API-referens för söktjänst](https://docs.microsoft.com/rest/api/searchservice/)