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
ms.date: 05/01/2018
ms.author: HeidiSteen
ms.openlocfilehash: 3a9ff6697357dec443691b261ae6fc0477603a9c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Azure Söktjänsts-REST API: Version 2017-11-11-Preview
Den här artikeln beskriver den `api-version=2017-11-11-Preview` versionen av Azure Search-tjänsten REST-API som tillhandahåller experiment följande funktioner:

+ [Kognitiva Sök](cognitive-search-concept-intro.md), en ny berikande-funktion i Azure Sökindexeringen som söker efter Latenta information i icke-text källor och omvandla det till fulltext sökbara innehåll i Azure Search odifferentierad text.

Förhandsgranska API motsvarar den allmänt tillgängliga API med undantag för följande två åtgärder:

+ [Skapa kunskaper (api-version = 2017-11-11-Preview)](ref-create-skillset.md)
+ [Skapa indexerare (api-version = 2017-11-11-Preview)](ref-create-indexer.md)

Se till att rikta förhandsversionen API `api-version=2017-11-11-Preview` vid utvärdering av förhandsversionsfunktioner. Följande exempel illustrerar ett anrop till förhandsversionen API.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

> [!NOTE]
> Förhandsgranskningsfunktioner finns tillgängliga för testning och experiment med målet att samla in feedback och kan komma att ändras. **Vi starkt rekommenderar mot att använda Förhandsgranska API: er i program i produktion.**

Azure Search-tjänsten är tillgänglig i flera versioner. Se [API-versioner](search-api-versions.md) mer information.
