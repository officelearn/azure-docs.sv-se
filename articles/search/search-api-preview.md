---
title: Förhands gransknings funktioner i REST API
titleSuffix: Azure Cognitive Search
description: Azure Kognitiv sökning service REST API version 2019-05-06 – för hands version innehåller experimentella funktioner som kunskaps lager och indexerare-cachelagring för stegvis berikning.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: fd21a4b821e1911e94d542a0922e5269786c365d
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76991073"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Förhands gransknings funktioner i Azure Kognitiv sökning

Den här artikeln innehåller funktioner som för närvarande finns i för hands version. Funktioner som övergår från förhands granskning till allmän tillgänglighet tas bort från listan. Du kan kontrol lera [tjänst uppdateringar](https://azure.microsoft.com/updates/?product=search) eller [Nyheter](whats-new.md) för meddelanden om allmän tillgänglighet.

Vissa för hands versions funktioner kan vara tillgängliga i portalen och .NET SDK, men REST API alltid har för hands versions funktioner. 

+ För Sök åtgärder är den aktuella för hands versionen av API-versionen [`2019-05-06-Preview`](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview)
+ För hanterings åtgärder är den aktuella för hands versionen [`2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview)

> [!IMPORTANT]
> För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ai-enrichment-features"></a>Funktioner för AI-anrikning

Utforska de senaste förbättringarna av AI-anrikning genom API för för [hands versions sökning](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview).

+ [Anpassad Sök kompetens för entitet (förhands granskning)](cognitive-search-skill-custom-entity-lookup.md ) är en kognitiv färdighet som söker efter text från en anpassad, användardefinierad lista med ord och fraser. Med den här listan etiketteras alla dokument med matchande entiteter. Kompetensen har även stöd för en viss fuzzy-matchning som kan användas för att söka efter matchningar som liknar varandra, men som inte exakt stämmer. 

+ [Kunskap om identifiering av PII (för hands version)](cognitive-search-skill-pii-detection.md) är en kognitiv färdighet som används vid indexering som extraherar personligt identifierbar information från en indata-text och ger dig möjlighet att maskera den från den texten på olika sätt.

+ [Stegvis anrikning (för hands version)](cognitive-search-incremental-indexing-conceptual.md) lägger till cachelagring i en pipeline för berikning, som gör att du kan återanvända befintliga utdata om en viss ändring, till exempel en uppdatering av en färdigheter eller ett annat objekt, inte ändrar innehållet. Cachelagring gäller endast för berikade dokument som produceras av en färdigheter.

+ [Kunskaps lagret (för hands version)](knowledge-store-concept-intro.md) är ett nytt mål för en AI-baserad pipeline för anrikning. Den fysiska data strukturen finns i Azure Blob Storage och Azure Table Storage, och den skapas och fylls i när du kör en indexerare som har en bifogad kognitiv färdigheter. Definitionen av ett kunskaps lager anges i en färdigheter-definition. I kunskaps lager definitionen styr du de fysiska strukturerna för dina data genom *projektions* element som avgör hur data är formade, om data lagras i Table Storage eller Blob Storage, samt om det finns flera vyer.

## <a name="indexing-and-query-features"></a>Indexerings-och fråge funktioner

Indexerings funktionerna för indexeraren finns i API för för hands versions sökning. 

+ [Cosmos db indexerare](search-howto-index-cosmosdb.md) stöder MONGODB-API (för hands version), GREMLIN-API (för hands version) och API för Cassandra (för hands version).

+ [Azure Data Lake Storage Gen2 indexerare (för hands version)](search-howto-index-azure-data-lake-storage.md) kan indexera innehåll och metadata från data Lake Storage Gen2.

+ [moreLikeThis (för hands version)](search-more-like-this.md) hittar dokument som är relevanta för ett enskilt dokument. Den här funktionen finns i tidigare för hands versioner. 

## <a name="management-features"></a>Hanterings funktioner

+ [Stöd för privata slut punkter](service-create-private-endpoint.md) via [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) av hanterings REST API. Du kan skapa en tjänst som har begränsningar för hur slut punkten nås.

## <a name="earlier-preview-features"></a>Tidigare för hands versions funktioner

Funktioner som meddelats i tidigare förhands granskningar, om de inte har övergått till allmän tillgänglighet, finns fortfarande i offentlig för hands version. Om du anropar ett API med en tidigare för hands version av API-version kan du fortsätta att använda den versionen eller växla till `2019-05-06-Preview` utan att ändra förväntat beteende.

## <a name="how-to-call-a-preview-api"></a>Så här anropar du ett för hands versions-API

Äldre förhands visningar fungerar fortfarande, men blir inaktuella över tid. Om din kod anropar `api-version=2016-09-01-Preview` eller `api-version=2017-11-11-Preview`är dessa anrop fortfarande giltiga. Men endast den nyaste för hands versionen har uppdaterats med förbättringar. 

Följande exempel på syntax illustrerar ett anrop till för hands versionen av API-versionen.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure Kognitiv sökning-tjänsten är tillgänglig i flera versioner. Mer information finns i [API-versioner](search-api-versions.md).

## <a name="next-steps"></a>Nästa steg

Läs REST API referens dokumentation för Sök. Om du stöter på problem kan du be oss om hjälp om [StackOverflow](https://stackoverflow.com/) eller [kontakta supporten](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Sök tjänst REST API referens](https://docs.microsoft.com/rest/api/searchservice/)