---
title: Real tids analys med Video Indexer
titleSuffix: Azure Media Services
description: Den här artikeln visar hur du utför en real tids analys med Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 0f34aad4a8590c71f926d12d201f9a614afaa127
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114934"
---
# <a name="live-stream-analysis-with-video-indexer"></a>Direkt uppspelnings analys med Video Indexer

Azure Media Services Video Indexer är en Azure-tjänst som är utformad för att extrahera djupgående insikter från video-och ljudfiler offline. Detta är att analysera en specifik mediefil som redan har skapats i förväg. För vissa användnings fall är det dock viktigt att få ut medie insikter från en live-feed så snabbt som möjligt för att låsa upp drift och andra användnings fall som är intryckta i tid. Sådana avancerade metadata i en Live Stream kan till exempel användas av innehålls producenter för att automatisera TV-produktion. Till exempel, den prepresse [Skin-gruppen](https://customers.microsoft.com/story/esg-media-telecommunications-azure), där journalister av en Newsroom sökte live-feeds för att bygga meddelande tjänster baserat på innehållet.

En lösning som beskrivs i den här artikeln gör det möjligt för kunder att använda Video Indexer i nära real tids lösningar på direktsända sändningar. Fördröjningen i indexeringen kan vara så låg som fyra minuter som använder den här lösningen, beroende på vilka data segment som indexeras, vilken typ av innehåll som används för den här processen.

![Video Indexer metadata i Live Stream](./media/live-stream-analysis/live-stream-analysis01.png)

*Figur 1 – exempel Player som visar Video Indexer metadata i den aktiva strömmen*

[Stream Analysis-lösningen](https://github.com/Azure-Samples/media-services-dotnet-functions-integration/blob/master/media-functions-for-logic-app/LiveStreamAnalysis.md) , använder Azure Functions och två Logic Apps för att bearbeta ett Live-program från en Live-kanal i Azure Media Services med video Indexer och visar resultatet med Azure Media Player som visar den resulterande strömmen i nära real tid.

På hög nivå består den av två huvud steg. Det första steget körs var 60 sekund och tar ett under klipp av de senaste 60 sekunderna som spelas upp, skapar en till gång från det och indexerar det via Video Indexer. Sedan anropas det andra steget när indexeringen är klar. De insikter som fångas bearbetas, skickas till Azure Cosmos DB och det under klipp som indexeras tas bort.

Exempel spelaren spelar live stream och får insikter från Azure Cosmos DB med hjälp av en dedikerad Azure-funktion. Den visar de metadata och miniatyr bilder som synkroniseras med Live-videon.

![De två Logic Apps bearbetar Live Stream varje minut i molnet](./media/live-stream-analysis/live-stream-analysis02.png)

*Bild 2 – de två Logic Apps bearbetar Live Stream varje minut i molnet.*

## <a name="step-by-step-guide"></a>Steg-för-steg-guide 

Den fullständiga koden och en steg-för-steg-guide för att distribuera resultaten finns i [GitHub-projekt för Live Media Analytics med video Indexer](https://aka.ms/livestreamanalysis). 

## <a name="next-steps"></a>Nästa steg

[Översikt över Video Indexer](video-indexer-overview.md)
