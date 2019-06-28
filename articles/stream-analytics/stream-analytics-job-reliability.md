---
title: Undvik avbrott i tjänsten i Azure Stream Analytics-jobb
description: Den här artikeln innehåller på att göra din Stream Analytics-jobb uppgradera flexibel.
services: stream-analytics
author: jseb225
ms.author: sidram
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 672706c97a423819dd26941e0b6e22affa9c2bb8
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329843"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garanterar tillförlitlighet för Stream Analytics-jobb under uppdateringar av tjänsten

För att en helt hanterad tjänst är möjligheten att introducera nya service-funktioner och förbättringar i snabb takt. Stream Analytics kan därför ha en tjänstuppdatering distribuera varje vecka (eller oftare). Oavsett hur mycket testning har fortfarande en risk att en befintlig, körs jobbet kan sluta fungera på grund av introduktionen av en bugg. Om du kör verksamhetskritiska kritiska måste jobb, riskerna undvikas. Du kan minska risken genom följande Azure **[parad region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** modell. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Hur Azure länkade regioner upp det här problemet?

Stream Analytics garanterar jobb i hopparade regioner har uppdaterats i separata batchar. Det finns därför en tillräcklig lucka i tiden mellan uppdateringar att identifiera potentiella problem och åtgärda dem.

_Med undantag för centrala Indien_ (vars parad region, södra Indien har inte Stream Analytics-presence), distribution av en uppdatering av Stream Analytics inte skulle ske samtidigt i en uppsättning med länkade regioner. Distributioner i flera regioner **i samma grupp** kan uppstå **samtidigt**.

Artikeln om **[tillgänglighet och länkade regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** har den senaste informationen som regioner har parats ihop.

Det rekommenderas att distribuera identiska jobb till båda länkade regioner. Du bör sedan [övervaka dessa jobb](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) att bli meddelad när det händer något oväntat. Om någon av dessa jobb avslutas upp i en [ett felaktigt tillstånd](https://docs.microsoft.com/azure/stream-analytics/job-states) efter en tjänstuppdatering för Stream Analytics kan du kontakta Kundsupport för att identifiera den bakomliggande orsaken. Du bör också redundansväxla alla underordnade kunder till felfria jobbutdata.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Stream Analytics](stream-analytics-introduction.md)
* [Kom igång med Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Frågespråksreferens för Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics management REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx)
