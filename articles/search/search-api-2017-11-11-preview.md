---
title: Azure Search-tjänsten REST API-versionen 2017-11-11-Preview | Microsoft Docs
description: Azure Search Service REST API-versionen 2017-11-11-Preview innehåller experiment funktioner, till exempel synonymer och moreLikeThis sökningar.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/17/2018
ms.author: HeidiSteen
ms.openlocfilehash: afcc8ac31c45c1a43d3d759ef6f5a1cee8166c0a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Azure Söktjänsts-REST API: Version 2017-11-11-Preview
Den här artikeln beskriver den `api-version=2017-11-11-Preview` versionen av Azure Search-tjänst, med experiment funktioner som inte ännu allmänt tillgänglig.

> [!NOTE]
> Förhandsgranskningsfunktioner finns tillgängliga för testning och experiment med målet att samla in feedback och kan komma att ändras. Vi starkt rekommenderar mot att använda Förhandsgranska API: er i program i produktion.


## <a name="new-in-this-preview"></a>I den här förhandsgranskningen

+ [Kognitiva Sök](cognitive-search-concept-intro.md), en ny berikande funktion i Azure Search hittar Latenta information i icke-text källor och omvandla det till fulltext sökbara innehåll i Azure Search odifferentierad text.

Följande två åtgärder införs eller ändras i förhandsgranskningen REST API. Alla andra REST API: er är samma oavsett om du anropar den allmänt tillgängliga eller Förhandsgranska version (t.ex.

+ [Skapa kunskaper (api-version = 2017-11-11-Preview)](ref-create-skillset.md)

+ [Skapa indexerare (api-version = 2017-11-11-Preview)](ref-create-indexer.md)

Alla andra REST API: er är samma oavsett om du anropar den allmänt tillgängliga eller förhandsgranskning. Till exempel `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11-Preview` och `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11` (utan `Preview`) är funktionellt likvärdiga.

## <a name="other-preview-features"></a>Andra funktioner för förhandsgranskning

Funktionerna från tidigare förhandsversioner är fortfarande i förhandsversion.

+ [CSV-filer i Azure Blob-indexering](search-howto-index-csv-blobs.md), där varje rad i en CSV-fil indexeras som ett separat dokument.

+ [JSON-matriser i Azure Blob-indexering](search-howto-index-json-blobs.md), där varje element i matrisen indexeras som ett separat dokument.

+ [`moreLikeThis` Frågeparametern](search-more-like-this.md) att hitta dokument som är relevanta för ett visst dokument. Den här funktionen har varit i tidigare förhandsversioner. Om du anropar den här API: et med ett äldre api-version, kan du fortsätta att använda den här versionen.


## <a name="how-to-call-a-preview-api"></a>Hur du anropar en API-förhandsgranskning

Äldre förhandsversioner fortfarande fungerar utan blir inaktuella över tid. Om koden anropar `api-version=2016-09-01-Preview` eller `api-version=2015-02-25-Preview`, anropen fortfarande är giltiga. Dock uppdateras bara den senaste förhandsversionen med förbättringar. 

Följande exempel illustrerar ett anrop till förhandsversionen API.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

Azure Search-tjänsten är tillgänglig i flera versioner. Mer information finns i [API-versioner](search-api-versions.md).
