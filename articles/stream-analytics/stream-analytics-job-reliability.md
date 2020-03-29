---
title: Undvik avbrott i tjänsten i Azure Stream Analytics-jobb
description: I den här artikeln beskrivs vägledning om hur dina Stream Analytics-jobb uppgraderas.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d2d21e081b274bd985c48dac91fff5203a6b5b8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425996"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantera Stream Analytics jobbtillförlitlighet under serviceuppdateringar

En del av att vara en fullt hanterad tjänst är möjligheten att införa nya servicefunktioner och förbättringar i snabb takt. Därför kan Stream Analytics distribuera en tjänstuppdatering varje vecka (eller oftare). Oavsett hur mycket testning görs finns det fortfarande en risk att ett befintligt, löpande jobb kan gå sönder på grund av införandet av ett fel. Om du kör verksamhetskritiska jobb måste dessa risker undvikas. Du kan minska den här risken genom att följa Azures **[kopplade regionmodell.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Hur tar Azure-parade regioner itu med detta problem?

Stream Analytics garanterar att jobb i parade regioner uppdateras i separata batchar. Som ett resultat finns det en tillräcklig tidslucka mellan uppdateringarna för att identifiera potentiella problem och åtgärda dem.

_Med undantag för centrala Indien_ (vars parade region, Södra Indien, inte har Stream Analytics-närvaro) skulle distributionen av en uppdatering till Stream Analytics inte ske samtidigt i en uppsättning parade regioner. Distributioner i flera regioner **i samma grupp** kan ske **samtidigt**.

Artikeln om **[tillgänglighet och parade regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** har den senaste informationen om vilka regioner som paras ihop.

Vi rekommenderar att du distribuerar identiska jobb till båda parkopplade regioner. Du bör sedan [övervaka dessa jobb](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) för att få ett meddelande när något oväntat händer. Om ett av dessa jobb hamnar i ett [misslyckat tillstånd](https://docs.microsoft.com/azure/stream-analytics/job-states) efter en Stream Analytics-tjänstuppdatering kan du kontakta kundsupporten för att identifiera grundorsaken. Du bör också växla över alla nedströms konsumenter till hälsosam jobbproduktion.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Stream Analytics](stream-analytics-introduction.md)
* [Kom igång med Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Webbplatsreferens för Stream Analytics-frågor](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST-api-referens för Strömanalyshantering](https://msdn.microsoft.com/library/azure/dn835031.aspx)
