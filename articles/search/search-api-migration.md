---
title: Uppgradera till den senaste versionen av Azure Search Service REST API | Microsoft Docs
description: Uppgradera till den senaste versionen av Azure Search Service REST API
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: 2efe7769f68988f3c0d52c8806b78c1b96d8c639
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39620237"
---
# <a name="upgrading-to-the-latest-azure-search-service-rest-api-version"></a>Uppgradera till den senaste versionen av Azure Search Service REST API
Om du använder en tidigare version av den [Azure Search Service REST API](https://docs.microsoft.com/rest/api/searchservice/), den här artikeln hjälper dig att uppgradera programmet till senaste allmänt tillgängliga API-versionen 2017-11-11.

Version 2017-11-11 av REST API innehåller vissa ändringar från tidigare versioner. Det här är främst bakåtkompatibla, så ändra din kod kräver bara enkelt, beroende på vilken version du använde före. Se [stegen för att uppgradera](#UpgradeSteps) anvisningar om hur du ändrar din kod till den nya API-versionen.

> [!NOTE]
> Din Azure Search-tjänstinstans har stöd för flera REST API-versioner, inklusive det senaste. Du kan fortsätta att använda en version när den inte längre det senaste, men vi rekommenderar att du migrerar din kod för att använda den senaste versionen.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2017-11-11"></a>Vad är nytt i version 2017-11-11
Version 2017-11-11 är senast allmänt tillgängliga versionen av Azure Search Service REST API. Nya funktioner i den här API-versionen:

* [Synonymer](search-synonyms.md). Den nya synonymfunktionen för kan du definiera ekvivalenta termer och expandera omfattningen av frågan.
* [Stöd för indexering effektivt text blobar](https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#IndexingPlainText). Den nya `text` parsningsläge för Azure Blob-indexerare avsevärt förbättrar indexering prestanda.
* [Tjänsten statistik API](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). Visa aktuell användning och gränser för resurser i Azure Search med den här nya API: et.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Steg för att uppgradera
Om du uppgraderar från en GA-version 2015-02-28 eller 2016-09-01 du förmodligen inte göra några ändringar i koden, annat än för att ändra versionsnumret. De enda situationer där du kan behöva ändra kod är när:

* Det går inte att din kod när okänd egenskaper returneras som en API-svar. Ditt program bör Ignorera egenskaper som inte förstår som standard.
* Din kod kvarstår API-begäranden och försöker skicka om dem till den nya API-versionen. Exempel: Detta kan inträffa om programmet kvarstår fortsättning token som returnerades från API: et Search (leta efter mer information `@search.nextPageParameters` i den [Search API-referens](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Om något av dessa fall gäller dig kan sedan du behöva ändras din kod. I annat fall inga ändringar bör vara nödvändigt om du inte vill börja använda den [nya funktioner](#WhatsNew) version 2017-11-11.

Om du uppgraderar från en api-versionen, ovanstående gäller också, men du måste också vara medveten om att vissa funktioner i förhandsversion inte är tillgängliga i version 2017-11-11:

* Azure Blob Storage-indexeringsstöd för CSV-filer och blobar som innehåller JSON-matriser.
* ”Mer så här”-frågor

Om din kod använder dessa funktioner kan du inte uppgradera till 2017-11-11 utan att ta bort din användning av dem.

> [!IMPORTANT]
> Du kommer ihåg, förhandsgranska API: er är avsedda för testning och utvärdering och ska inte användas i produktionsmiljöer.
> 
> 

## <a name="conclusion"></a>Sammanfattning
Om du behöver mer information om hur du använder Azure Search Service REST API kan se den nyligen uppdaterade [API-referens](https://docs.microsoft.com/rest/api/searchservice/) på MSDN.

Vi uppskattar din feedback på Azure Search. Om du får problem kan passa på att be om hjälp oss på den [Azure Search MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) eller [StackOverflow](http://stackoverflow.com/). Om du ställa en fråga om Azure Search på StackOverflow, se till att tagga den med `azure-search`.

Tack för att använda Azure Search!

