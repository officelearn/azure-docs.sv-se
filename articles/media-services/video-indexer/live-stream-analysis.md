---
title: Livestream-analys med videoindexerare
titleSuffix: Azure Media Services
description: Den här artikeln visar hur du utför en livestream-analys med videoindexerare.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186002"
---
# <a name="live-stream-analysis-with-video-indexer"></a>Live stream-analys med Video Indexer

Azure Media Services Video Indexer är en Azure-tjänst som utformats för att extrahera djupa insikter från video- och ljudfiler offline. Detta för att analysera en viss mediefil som redan skapats i förväg. Men för vissa användningsfall är det viktigt att få medieinsikterna från en live-feed så snabbt som möjligt för att låsa upp drifts- och andra användningsfall som pressas i tid. Sådana omfattande metadata på en livestream kan till exempel användas av innehållsproducenter för att automatisera TV-produktionen.

En lösning som beskrivs i den här artikeln gör det möjligt för kunder att använda Video Indexer i nästan realtidslösningar i liveflöden. Fördröjningen i indexeringen kan vara så låg som fyra minuter med den här lösningen, beroende på vilka datasegment som indexeras, indataupplösningen, typen av innehåll och den beräkningsdrivna som används för den här processen.

![Metadata för videoindexeraren i livestreamen](./media/live-stream-analysis/live-stream-analysis01.png)

*Bild 1 – Exempelspelare som visar metadata för videoindexeraren i livestreamen*

Den [byggda strömanalyslösningen](https://aka.ms/livestreamanalysis) använder Azure Functions och två Logic Apps för att bearbeta ett live-program från en live-kanal i Azure Media Services med Video Indexer och visar resultatet med Azure Media Player som visar den nära realtidsresultatströmmen.

På hög nivå består den av två huvudsteg. Det första steget körs var 60:e sekund och tar ett underknur på de senaste 60 sekunderna som spelats upp, skapar en tillgång från det och indexerar den via Video Indexer. Sedan anropas det andra steget när indexeringen är klar. De intagna insikterna bearbetas, skickas till Azure Cosmos DB och det indexerade underknästen tas bort.

Exempelspelaren spelar livestreamen och hämtar insikterna från Azure Cosmos DB med hjälp av en dedikerad Azure-funktion. Den visar metadata och miniatyrer synkroniseras med livevideon.

![De två logikapparna som bearbetar livestreamen varje minut i molnet](./media/live-stream-analysis/live-stream-analysis02.png)

*Bild 2 – De två logikapparna som bearbetar livestreamen varje minut i molnet.*

## <a name="step-by-step-guide"></a>Steg-för-steg-guide 

Den fullständiga koden och en steg-för-steg-guide för att distribuera resultaten finns i [GitHub-projektet för Live media analytics med Video Indexer](https://aka.ms/livestreamanalysis). 

## <a name="next-steps"></a>Nästa steg

[Översikt över Video Indexer](video-indexer-overview.md)
