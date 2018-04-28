---
title: Azure Search-tjänsten REST API-versionen 2016-09-01-Preview | Microsoft Docs
description: Azure Search Service REST API-versionen 2016-09-01-Preview innehåller experiment funktioner, till exempel moreLikeThis sökningar.
author: mhko
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: reference
ms.date: 04/18/2018
ms.author: nateko
ms.openlocfilehash: 8eae54c912711a11c015737903b6898b98fd5159
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>Azure Söktjänsts-REST API: Version 2016-09-01-Preview
Den här artikeln är referensdokumentationen för `api-version=2016-09-01-Preview`. Den här förhandsgranskningen utökar den aktuella allmänt tillgängliga versionen [api-version = 2016 09 01](https://docs.microsoft.com/rest/api/searchservice), genom att tillhandahålla experiment följande funktioner:

* [`moreLikeThis` Frågeparametern](search-more-like-this.md) att hitta dokument som är relevanta för ett visst dokument.

Kontrollera om du vill rikta förhandsversionen API `api-version=2016-09-01-Preview` försöka funktionerna experiment. Följande exempel visar hur förhandsgranskningen api-versionen har angetts i att göra en mer den här liknande fråga.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Förhandsgranskningsfunktioner finns tillgängliga för testning och experiment med målet att samla in feedback och kan komma att ändras. **Vi starkt rekommenderar mot att använda Förhandsgranska API: er i program i produktion.**

Azure Search-tjänsten är tillgänglig i flera versioner. Se [Sök Versionhantering](https://docs.microsoft.com/azure/search/search-api-versions) mer information.
