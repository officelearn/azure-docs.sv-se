---
title: Uppgradera till den Azure Söktjänsts-REST API version 2016-09-01 | Microsoft Docs
description: Uppgradera till den Azure Söktjänsts-REST API version 2016-09-01
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: brjohnst
ms.openlocfilehash: ea901462677d42d90007a2130825bd3b382407f2
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="upgrading-to-the-azure-search-service-rest-api-version-2016-09-01"></a>Uppgradera till den Azure Söktjänsts-REST API version 2016-09-01
Om du använder version 2015-02-28 eller 2015-02-28-Preview av den [Azure Söktjänsts-REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx), den här artikeln hjälper dig att uppgradera ditt program att använda den nästa allmänt tillgänglig API-versionen 2016-09-01.

Version 2016-09-01 av REST API innehåller vissa ändringar från tidigare versioner. Dessa är främst bakåtkompatibla så ändrar koden kräver endast minsta möjliga ansträngning, beroende på vilken version du använde före. Se [steg för att uppgradera](#UpgradeSteps) för instruktioner om hur du ändrar koden för att använda den nya API-versionen.

> [!NOTE]
> Din Azure Search-tjänstinstansen stöder flera REST API-versioner, inklusive det senaste. Du kan fortsätta att använda en version när det är inte längre den senaste, men vi rekommenderar att du migrerar din kod för att använda den senaste versionen.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2016-09-01"></a>Vad är nytt i version 2016-09-01
Version 2016-09-01 är den allmänt tillgängliga andra utgåvan av den Azure Söktjänsts-REST API. Nya funktioner i den här API-versionen är:

* [Anpassade analyzers](https://aka.ms/customanalyzers), vilket gör att du kan ta kontroll över processen konvertera text till indexeras och sökbara token.
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) och [Azure Table Storage](search-howto-indexing-azure-tables.md) indexerare som gör att du enkelt kan importera data från Azure-lagring till Azure Search i ett schema eller på begäran.
* [Fältet mappningar](search-indexer-field-mappings.md), vilket kan du anpassa hur indexerare importerar data till Azure Search.
* ETags som gör att du kan uppdatera definitionerna av index och indexerare datakällor på samtidighet-säkert sätt. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Instruktioner för uppgradering
Om du uppgraderar från version 2015-02-28 behöver du förmodligen inte göra ändringar i din kod än för att ändra versionsnumret. Endast situationer där du kan behöva ändra koden är när:

* Det går inte att din kod när okända egenskaper returneras som en API-svar. Ditt program bör Ignorera egenskaper som inte förstår som standard.
* Koden kvarstår API-begäranden och försöker skicka dem till den nya API-versionen. T.ex, detta kan inträffa om tillämpningsprogrammet kvarstår fortsättning token som returnerades från Sök-API (leta efter mer information `@search.nextPageParameters` i den [Sök API-referens](https://msdn.microsoft.com/library/azure/dn798927.aspx#Anchor_1)).

Om någon av dessa situationer gäller dig måste att ändra koden i enlighet med detta. I annat fall inga ändringar bör vara nödvändigt om du inte vill börja använda den [nya funktioner](#WhatsNew) version 2016-09-01.

Om du uppgraderar från version 2015-02-28-Preview ovanstående gäller också, men du måste också vara medveten om att vissa funktioner inte är tillgängliga i version 2016 09 01:

* Azure Blob Storage indexeraren-stöd för CSV-filer och blobbar som innehåller JSON-matriser.
* Synonymer
* ”Mer så här” frågor

Om din kod använder dessa funktioner, kan du inte uppgradera till 2016-09-01 utan att ta bort din användning av dessa.

> [!IMPORTANT]
> Kontrollera komma ihåg, förhandsgranska API: er är avsedd för testning och utvärdering och ska inte användas i produktionsmiljöer.
> 
> 

## <a name="conclusion"></a>Sammanfattning
Om du vill ha mer information om hur du använder Azure Search Service REST-API finns nyligen uppdaterat [API-referens](https://msdn.microsoft.com/library/azure/dn798935.aspx) på MSDN.

Vi uppskattar din feedback på Azure Search. Om du får problem passa på att be om hjälp oss på den [Azure Search MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) eller [StackOverflow](http://stackoverflow.com/). Om begär du en fråga om Azure Search på StackOverflow, kontrollerar du att märka den med `azure-search`.

Tack för att använda Azure Search!

