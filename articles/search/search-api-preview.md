---
title: REST API version 2019-05-06 – för hands version
titleSuffix: Azure Cognitive Search
description: Azure Kognitiv sökning service REST API version 2019-05-06 – för hands version innehåller experimentella funktioner som kunskaps lager och indexerare-cachelagring för stegvis berikning.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/04/2020
ms.openlocfilehash: eb73d614ca94bc1fa007a14f3705e50c74ab9e4f
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922480"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>Azure Kognitiv sökning service REST API-version 2019-05-06-för hands version

I den här artikeln beskrivs `api-version=2019-05-06-Preview` versionen av search service REST API, som erbjuder experimentella funktioner som ännu inte är allmänt tillgängliga.

> [!NOTE]
> För hands versions funktionerna är tillgängliga för testning och experimentering med målet att samla in feedback och kan komma att ändras. Vi rekommenderar starkt att du använder för hands versioner av API: er i produktions program.


## <a name="new-in-2019-05-06-preview"></a>Nyhet i 2019-05-06 – för hands version

+ [Stegvis anrikning (för hands version)](cognitive-search-incremental-indexing-conceptual.md) lägger till cachelagring i en pipeline för berikning, som gör att du kan återanvända befintliga utdata om en viss ändring, till exempel en uppdatering av en färdigheter eller ett annat objekt, inte ändrar innehållet. Cachelagring gäller endast för berikade dokument som produceras av en färdigheter.

+ [Cosmos db indexerare](search-howto-index-cosmosdb.md) stöder MONGODB-API (för hands version), GREMLIN-API (för hands version) och API för Cassandra (för hands version).

+ [Azure Data Lake Storage Gen2 indexerare (för hands version)](search-howto-index-azure-data-lake-storage.md) kan indexera innehåll och metadata från data Lake Storage Gen2.

+ [Extrahering av dokument (för hands version)](cognitive-search-skill-document-extraction.md) är en kognitiv kunskap som används vid indexering och som gör att du kan extrahera innehållet i en fil från en färdigheter. Tidigare inträffade endast dokument sprickor innan färdigheter kördes. Med att lägga till den här kunskapen kan du också utföra den här åtgärden i färdigheter-körning.

+ [Text översättning (för hands version)](cognitive-search-skill-text-translation.md) är en kognitiv färdighet som används vid indexering som utvärderar text och, för varje post, returnerar texten översatt till det angivna mål språket.

+ [Kunskaps lager](knowledge-store-concept-intro.md) är ett nytt mål för en AI-baserad pipeline för anrikning. Den fysiska data strukturen finns i Azure Blob Storage och Azure Table Storage, och den skapas och fylls i när du kör en indexerare som har en bifogad kognitiv färdigheter. Definitionen av ett kunskaps lager anges i en färdigheter-definition. I kunskaps lager definitionen styr du de fysiska strukturerna för dina data genom *projektions* element som avgör hur data är formade, om data lagras i Table Storage eller Blob Storage, samt om det finns flera vyer.

## <a name="earlier-preview-features"></a>Tidigare för hands versions funktioner

Funktioner som meddelats i tidigare förhands granskningar finns fortfarande i offentlig för hands version. Om du anropar ett API med en tidigare för hands version av API-version kan du fortsätta att använda den versionen eller växla till `2019-05-06-Preview` utan att ändra förväntat beteende.

+ [moreLikeThis Query-parameter](search-more-like-this.md) hittar dokument som är relevanta för ett speciellt dokument. Den här funktionen finns i tidigare för hands versioner. 

+ [CSV-BLOB-indexering](search-howto-index-csv-blobs.md) skapar ett dokument per rad, i stället för ett dokument per text-blob.

## <a name="how-to-call-a-preview-api"></a>Så här anropar du ett för hands versions-API

Äldre förhands visningar fungerar fortfarande, men blir inaktuella över tid. Om din kod anropar `api-version=2016-09-01-Preview` eller `api-version=2017-11-11-Preview`är dessa anrop fortfarande giltiga. Men endast den nyaste för hands versionen har uppdaterats med förbättringar. 

Följande exempel på syntax illustrerar ett anrop till för hands versionen av API-versionen.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure Kognitiv sökning-tjänsten är tillgänglig i flera versioner. Mer information finns i [API-versioner](search-api-versions.md).

## <a name="next-steps"></a>Nästa steg

Läs REST API referens dokumentation för Sök. Om du stöter på problem kan du be oss om hjälp om [StackOverflow](https://stackoverflow.com/) eller [kontakta supporten](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Sök tjänst REST API referens](https://docs.microsoft.com/rest/api/searchservice/)