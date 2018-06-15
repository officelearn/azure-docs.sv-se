---
title: Uppgradera till den senaste versionen av Azure Söktjänsts-REST API | Microsoft Docs
description: Uppgradera till den senaste Azure Söktjänsts-REST API-versionen
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: 3848f317fd6d760961756f132edf9cbcb5f431ee
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181978"
---
# <a name="upgrading-to-the-latest-azure-search-service-rest-api-version"></a>Uppgradera till den senaste Azure Söktjänsts-REST API-versionen
Om du använder en tidigare version av den [Azure Söktjänsts-REST API](https://docs.microsoft.com/rest/api/searchservice/), den här artikeln hjälper dig att uppgradera ditt program att använda senast allmänt tillgänglig API-versionen, 2017-11-11.

Version 2017-11-11 av REST API innehåller vissa ändringar från tidigare versioner. Dessa är främst bakåtkompatibla så ändrar koden kräver endast minsta möjliga ansträngning, beroende på vilken version du använde före. Se [steg för att uppgradera](#UpgradeSteps) för instruktioner om hur du ändrar koden för att använda den nya API-versionen.

> [!NOTE]
> Din Azure Search-tjänstinstansen stöder flera REST API-versioner, inklusive det senaste. Du kan fortsätta att använda en version när det är inte längre den senaste, men vi rekommenderar att du migrerar din kod för att använda den senaste versionen.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2017-11-11"></a>Vad är nytt i version 2017-11-11
Version 2017-11-11 är senast allmänt tillgänglig version av den Azure Söktjänsts-REST API. Nya funktioner i den här API-versionen är:

* [Synonymer](search-synonyms.md). Funktionen synonymer kan du definiera motsvarande uttryck och expandera omfattningen av frågan.
* [Stöd för indexering effektivt text blobbar](https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#IndexingPlainText). Den nya `text` parsning läge för Azure Blob indexerare avsevärt förbättrar indexering prestanda.
* [Tjänsten statistik API](https://aka.ms/azure-search-stats). Visa aktuell användning och gränser för resurser i Azure Search med den här nya API.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Instruktioner för uppgradering
Om du uppgraderar från en GA-version behöver 2015-02-28 eller 2016-09-01, har du förmodligen inte göra ändringar i din kod än för att ändra versionsnumret. Endast situationer där du kan behöva ändra koden är när:

* Det går inte att din kod när okända egenskaper returneras som en API-svar. Ditt program bör Ignorera egenskaper som inte förstår som standard.
* Koden kvarstår API-begäranden och försöker skicka dem till den nya API-versionen. T.ex, detta kan inträffa om tillämpningsprogrammet kvarstår fortsättning token som returnerades från Sök-API (leta efter mer information `@search.nextPageParameters` i den [Sök API-referens](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Om någon av dessa situationer gäller dig måste att ändra koden i enlighet med detta. I annat fall inga ändringar bör vara nödvändigt om du inte vill börja använda den [nya funktioner](#WhatsNew) version 2017-11-11.

Om du uppgraderar från en api-förhandsversion ovanstående gäller också, men du måste också vara medveten om att vissa funktioner inte är tillgängliga i version 2017-11-11:

* Azure Blob Storage indexeraren-stöd för CSV-filer och blobbar som innehåller JSON-matriser.
* ”Mer så här” frågor

Om din kod använder dessa funktioner, kan du inte uppgradera till 2017-11-11 utan att ta bort din användning av dessa.

> [!IMPORTANT]
> Kontrollera komma ihåg, förhandsgranska API: er är avsedd för testning och utvärdering och ska inte användas i produktionsmiljöer.
> 
> 

## <a name="conclusion"></a>Sammanfattning
Om du vill ha mer information om hur du använder Azure Search Service REST-API finns nyligen uppdaterat [API-referens](https://docs.microsoft.com/rest/api/searchservice/) på MSDN.

Vi uppskattar din feedback på Azure Search. Om du får problem passa på att be om hjälp oss på den [Azure Search MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) eller [StackOverflow](http://stackoverflow.com/). Om begär du en fråga om Azure Search på StackOverflow, kontrollerar du att märka den med `azure-search`.

Tack för att använda Azure Search!

