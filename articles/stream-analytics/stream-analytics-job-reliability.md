---
title: Undvik avbrott i tjänsten i Azure Stream Analytics-jobb
description: Den här artikeln innehåller på att göra din Stream Analytics-jobb uppgradera flexibel.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7375fb2763ad83e049b1ef30a623f164e059a792
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479464"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garanterar tillförlitlighet för Stream Analytics-jobb under uppdateringar av tjänsten

För att en helt hanterad tjänst är möjligheten att introducera nya service-funktioner och förbättringar i snabb takt. Stream Analytics kan därför ha en tjänstuppdatering distribuera varje vecka (eller oftare). Oavsett hur mycket testning har fortfarande en risk att en befintlig, körs jobbet kan sluta fungera på grund av introduktionen av en bugg. Dessa risker måste undvikas för kunder som kör kritiska direktuppspelningsjobb bearbetning. En mekanism som kunder kan använda för att minska denna risk är Azures **[parad region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** modell. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Hur Azure länkade regioner upp det här problemet?

Stream Analytics garanterar jobb i hopparade regioner har uppdaterats i separata batchar. Det finns därför en tillräcklig lucka i tiden mellan uppdateringar att identifiera potentiella programfel för de senaste och åtgärda dem.

_Med undantag för centrala Indien_ (vars parad region, södra Indien har inte Stream Analytics-presence), distribution av en uppdatering av Stream Analytics inte skulle ske samtidigt i en uppsättning med länkade regioner. Distributioner i flera regioner **i samma grupp** kan uppstå **samtidigt**.

Artikeln om **[tillgänglighet och länkade regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** har den senaste informationen som regioner har parats ihop.

Kunder bör distribuera identiska jobb till båda länkade regioner. Förutom Stream Analytics interna funktioner för övervakning, kunder bör också övervaka jobb som om **både** är produktionsjobb. Om ett avbrott identifieras för att vara ett resultat av Stream Analytics service-uppdatering, eskalera på rätt sätt och växla över några underordnade konsumenter till felfria jobbutdata. Eskalering till stöd för kommer att förhindra att den parade regionen påverkas av den nya distributionen och underhålla integriteten hos de kopplade jobb.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Stream Analytics](stream-analytics-introduction.md)
* [Kom igång med Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Frågespråksreferens för Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics management REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx)
