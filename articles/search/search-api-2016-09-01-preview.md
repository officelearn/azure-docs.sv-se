---
title: "Azure Search-tjänsten REST API-versionen 2016-09-01-Preview | Microsoft Docs"
description: "Azure Search Service REST API-versionen 2016-09-01-Preview innehåller experiment funktioner, till exempel synonymer och moreLikeThis sökningar."
services: search
documentationcenter: na
author: mhko
manager: jlembicz
editor: 
ms.assetid: 3dba3bf8-9c83-42f6-82bc-04727bd11037
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 10/25/2017
ms.author: nateko
ms.openlocfilehash: 082c207f892fcc277d30d66c6165dd9920d3ab27
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>Azure Söktjänsts-REST API: Version 2016-09-01-Preview
Den här artikeln är referensdokumentationen för `api-version=2016-09-01-Preview`. Den här förhandsgranskningen utökar den aktuella allmänt tillgängliga versionen [api-version = 2016 09 01](https://msdn.microsoft.com/library/dn798935.aspx), genom att tillhandahålla experiment följande funktioner:

* [Synonymer API](search-synonyms.md) att överföra synonymen maps och utöka sökningen.
* [`moreLikeThis`Frågeparametern](search-more-like-this.md) att hitta dokument som är relevanta för ett visst dokument.

Kontrollera om du vill rikta förhandsversionen API `api-version=2016-09-01-Preview` försöka funktionerna experiment. Följande exempel visar hur förhandsgranskningen api-versionen har angetts i att göra en mer den här liknande fråga.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Förhandsgranskningsfunktioner finns tillgängliga för testning och experiment med målet att samla in feedback och kan komma att ändras. **Vi starkt rekommenderar mot att använda Förhandsgranska API: er i program i produktion.**

Azure Search-tjänsten är tillgänglig i flera versioner. Se [Sök Versionhantering](http://msdn.microsoft.com/library/azure/dn864560.aspx) mer information.
