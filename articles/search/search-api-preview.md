---
title: Förhandsversion av REST API för Azure Search 2017-11-11-förhandsversion – Azure Search
description: Azure Search Service REST API-Version 2017-11-11-förhandsversion innehåller experimentella funktioner, till exempel synonymer och moreLikeThis sökningar.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 06/28/2018
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 524c1a6d083db02349c7dae9a0131228613dc170
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997618"
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Azure Search Service REST api-versionen 2017-11-11-förhandsversion
Den här artikeln beskriver den `api-version=2017-11-11-Preview` versionen av Azure Search service REST API, erbjuder experimentella funktioner som inte ännu är allmänt tillgänglig.

> [!NOTE]
> Förhandsversionsfunktioner är tillgängliga för testning och experimentering med målet att samla in feedback och kan komma att ändras. Vi att rekommenderar starkt mot använder förhandsversionen av API: er i produktionsprogram.


## <a name="new-in-2017-11-11-preview"></a>Nytt i 2017-11-11-förhandsversion

[**automatisk komplettering** ](search-autocomplete-tutorial.md) ansluter till den befintliga [förslag API](https://docs.microsoft.com/rest/api/searchservice/suggestions) att lägga till kompletterande frågeifyllningsförslag inträffar till sökfältet. automatisk komplettering returnerar kandidat sökord som en användare kan välja som frågesträngen för en efterföljande sökning. Förslag visar faktiska dokument som svar på partiella indata: sökresultaten är omedelbar och ändras dynamiskt när termen sökinmatning växer och dessutom.

[**Kognitiv sökning**](cognitive-search-concept-intro.md), en ny funktion för funktioner i Azure Search söker efter latent information i icke-text källor och odifferentierade text, omvandla det till fulltext sökbart innehåll i Azure Search. Följande resurser introducerades eller ändras i förhandsversionen av REST API. Alla andra REST API: er är samma oavsett om du anropar den allmänt tillgängliga eller förhandsversionen.

+ [Kompetens operations(api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/skillset-operations)

+ [Skapa indexerare (api-version = 2017-11-11-förhandsversion)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)

Alla andra REST API: er är desamma oavsett hur du ställer in api-versionen. Till exempel `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11-Preview` och `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11` (utan `Preview`) har samma funktioner.

## <a name="other-preview-features"></a>Andra funktioner i förhandsversion

Funktioner som vi tillkännagav i tidigare förhandsversioner är fortfarande i offentlig förhandsversion. Om du anropar ett API med en tidigare förhandsversion api-version, kan du fortsätta att använda den här versionen eller växla till `2017-11-11-Preview` utan ändringar i förväntat beteende.

+ [CSV-filer i Azure Blob-indexering](search-howto-index-csv-blobs.md), introducerades i `api-version=2015-02-28-Preview`, förblir en förhandsversion av funktionen. Den här funktionen är en del av Azure Blob-indexering och anropas via en parameter-inställning. Varje rad i en CSV-fil indexeras som en separat dokument.

+ [JSON-matriser i Azure Blob-indexering](search-howto-index-json-blobs.md), introducerades i `api-version=2015-02-28-Preview`, förblir en förhandsversion av funktionen. Den här funktionen är en del av Azure Blob-indexering och anropas via en parameter-inställning. där varje element i matrisen indexeras som en separat dokument.

+ [moreLikeThis frågeparameter](search-more-like-this.md) söker efter dokument som är relevanta för ett visst dokument. Den här funktionen har varit i tidigare förhandsversioner. 


## <a name="how-to-call-a-preview-api"></a>Hur du anropar en förhandsversionen av API

Äldre förhandsversioner är fortfarande fungerar men blir inaktuella över tid. Om din kod anropar `api-version=2016-09-01-Preview` eller `api-version=2015-02-28-Preview`, anropen fortfarande är giltiga. Dock har endast den senaste förhandsversionen uppdaterats med förbättringar. 

Följande exempel illustrerar ett anrop till förhandsversionen av API-version.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

Azure Search-tjänsten är tillgänglig i flera versioner. Mer information finns i [API-versioner](search-api-versions.md).
