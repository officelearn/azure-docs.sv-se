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
ms.openlocfilehash: e5cd7fcd0c853f03dbafb4d95b8459dcc83aecdf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>Azure Söktjänsts-REST API: Version 2016-09-01-Preview
Den här artikeln innehåller funktioner för förhandsgranskning av `api-version=2016-09-01-Preview`. Den här förhandsgranskningen utökar den allmänt tillgängliga tidigare [api-version = 2016-09-01](https://docs.microsoft.com/rest/api/searchservice), genom att tillhandahålla experiment följande funktioner:

* [`moreLikeThis` Frågeparametern](search-more-like-this.md) att hitta dokument som är relevanta för ett visst dokument. Den här funktionen har varit i tidigare förhandsversioner. Om du anropar den här API: et med ett äldre api-version, kan du fortsätta att använda den här versionen.


## <a name="how-to-call-a-preview-api"></a>Hur du anropar en API-förhandsgranskning

Följande exempel visar hur förhandsgranskningen api-versionen har angetts i att göra en mer den här liknande fråga.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Förhandsgranskningsfunktioner finns tillgängliga för testning och experiment med målet att samla in feedback och kan komma att ändras. **Vi starkt rekommenderar mot att använda Förhandsgranska API: er i program i produktion.**

Azure Search-tjänsten är tillgänglig i flera versioner. Se [Sök Versionhantering](https://docs.microsoft.com/azure/search/search-api-versions) mer information.
