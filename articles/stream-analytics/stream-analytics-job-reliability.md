---
title: Undvik avbrott i tjänsten i Azure Stream Analytics-jobb
description: Den här artikeln innehåller på att göra din Stream Analytics-jobb uppgradera flexibel.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d2d21e081b274bd985c48dac91fff5203a6b5b8a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425996"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garanterar tillförlitlighet för Stream Analytics-jobb under uppdateringar av tjänsten

För att en helt hanterad tjänst är möjligheten att introducera nya service-funktioner och förbättringar i snabb takt. Stream Analytics kan därför ha en tjänstuppdatering distribuera varje vecka (eller oftare). Oavsett hur mycket testning har fortfarande en risk att en befintlig, körs jobbet kan sluta fungera på grund av introduktionen av en bugg. Om du kör uppdrags kritiska jobb måste dessa risker undvikas. Du kan minska den här risken genom att följa den **[kopplade region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** modellen i Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Hur Azure länkade regioner upp det här problemet?

Stream Analytics garanterar jobb i hopparade regioner har uppdaterats i separata batchar. Det finns därför en tillräckligt lång lucka mellan uppdateringarna för att identifiera potentiella problem och åtgärda dem.

_Med undantag för centrala Indien_ (vars parad region, södra Indien har inte Stream Analytics-presence), distribution av en uppdatering av Stream Analytics inte skulle ske samtidigt i en uppsättning med länkade regioner. Distributioner i flera regioner **i samma grupp** kan uppstå **samtidigt**.

Artikeln om **[tillgänglighet och länkade regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** har den senaste informationen som regioner har parats ihop.

Vi rekommenderar att du distribuerar identiska jobb till båda kopplade regioner. Du bör sedan [övervaka dessa jobb](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) för att få meddelanden när något oväntade inträffar. Om ett av dessa jobb upphör i [fel tillstånd](https://docs.microsoft.com/azure/stream-analytics/job-states) efter en Stream Analytics tjänst uppdatering kan du kontakta kund supporten för att identifiera rotor saken. Du bör också redundansväxla eventuella efterföljande användare till den felfria utskriften.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Stream Analytics](stream-analytics-introduction.md)
* [Kom igång med Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Frågespråksreferens för Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics management REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx)
