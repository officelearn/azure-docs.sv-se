---
title: Fel sökning – Personanpassare
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller svar på vanliga fel söknings frågor om Personanpassare.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: diberry
ms.openlocfilehash: 5aeda9abcebda50cf97e1473b458d8f1f9d15970
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832179"
---
# <a name="personalizer-troubleshooting"></a>Fel sökning av personanpassa

Den här artikeln innehåller svar på vanliga fel söknings frågor om Personanpassare.

## <a name="transaction-errors"></a>Transaktions fel

### <a name="i-get-an-http-429-too-many-requests-response-from-the-service-what-can-i-do"></a>Jag får ett svar för HTTP 429 (för många begär Anden) från tjänsten. Vad kan jag göra?

Om du har valt en kostnads fri pris nivå när du skapade personanpassa instansen, finns det en kvot gräns för antalet ranknings begär Anden som tillåts. Granska API-anrops frekvensen för rang-API: n (i fönstret mått i Azure Portal för din personanpassa resurs) och justera pris nivån (i fönstret pris nivå) om din anrops volym förväntas öka efter tröskelvärdet för den valda pris nivån.

### <a name="im-getting-a-5xx-error-on-rank-or-reward-apis-what-should-i-do"></a>Jag får ett 5xx-fel vid rang-eller belönings-API: er. Vad gör jag?

Dessa problem bör vara transparenta. Om de fortsätter kan du kontakta supporten genom att välja **ny supportbegäran** i avsnittet **support och fel sökning** i Azure Portal för din personanpassa resurs.


## <a name="learning-loop"></a>Inlärnings slinga

<!--

### How do I import a learning policy?


-->

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Inlärnings slingan verkar inte lära sig. Hur gör jag för att åtgärda detta?

Inlärnings slingan behöver några tusen belönings samtal innan ranknings anropen prioriteras effektivt.

Om du är osäker på hur din inlärnings slinga för närvarande fungerar, kör du en [offline-utvärdering](concepts-offline-evaluation.md)och tillämpar den korrigerade inlärnings principen.

### <a name="i-keep-getting-rank-results-with-all-the-same-probabilities-for-all-items-how-do-i-know-personalizer-is-learning"></a>Jag vill få ranknings resultat med samma sannolikhet för alla objekt. Hur gör jag för att vet du att personanpassa är inlärning?

En personanpassare returnerar samma sannolikhet i ett rang-API-resultat när den precis har startat och har en _Tom_ modell, eller när du återställer en personanpassa-loop och din modell fortfarande ligger inom **modell uppdateringens frekvens** period.

När den nya uppdaterings perioden börjar används den uppdaterade modellen och du ser sannolikheten för ändringen.

### <a name="the-learning-loop-was-learning-but-seems-to-not-learn-anymore-and-the-quality-of-the-rank-results-isnt-that-good-what-should-i-do"></a>Inlärnings-slingan var inlärning men verkar inte längre, och kvaliteten på ranknings resultatet är inte så bra. Vad gör jag?

* Se till att du har slutfört och tillämpat en utvärdering i Azure Portal för denna personanpassa resurs (inlärnings slinga).
* Se till att alla förmåner skickas, via belönings-API: et och bearbetas.

### <a name="how-do-i-know-that-the-learning-loop-is-getting-updated-regularly-and-is-used-to-score-my-data"></a>Hur gör jag för att vet du att inlärnings slingan uppdateras regelbundet och används för att räkna data?

Du hittar tiden då modellen senast uppdaterades på sidan **modell-och inlärnings inställningar** i Azure Portal. Om du ser en gammal tidsstämpel är det troligt att du inte skickar rang-och belönings samtal. Om tjänsten inte har några inkommande data uppdateras inte inlärningen. Om inlärnings-slingan inte uppdateras tillräckligt ofta kan du redigera **uppdaterings frekvensen**för loopen.


## <a name="offline-evaluations"></a>Offlineutvärderingar

### <a name="an-offline-evaluations-feature-importance-returns-a-long-list-with-hundreds-or-thousands-of-items-what-happened"></a>En offline-utvärderings funktions prioritet returnerar en lång lista med hundratals eller tusentals objekt. Vad hände?

Detta beror vanligt vis på tidsstämpel, användar-ID: n eller andra detaljerade funktioner som skickas i.

### <a name="i-created-an-offline-evaluation-and-it-succeeded-almost-instantly-why-is-that-i-dont-see-any-results"></a>Jag skapade en offline-utvärdering och den lyckades nästan omedelbart. Vad beror det på? Jag ser inga resultat?

Offline-utvärderingen använder de tränade modell data från händelserna under den tids perioden. Om du inte skickade några data under tids perioden mellan start-och slut tiden för utvärderingen, slutförs det utan några resultat. Skicka en ny offline-utvärdering genom att välja ett tidsintervall med händelser som du vet har skickats till Personanpassaren.

## <a name="learning-policy"></a>Utbildnings princip

### <a name="how-do-i-import-a-learning-policy"></a>Hur gör jag för att importera en utbildnings princip?

Lär dig mer om [begrepp för inlärnings principer](concept-active-learning.md#understand-learning-policy-settings) och [hur du använder](how-to-learning-policy.md) en ny utbildnings princip. Om du inte vill välja en utbildnings princip kan du använda [offline-utvärderingen](how-to-offline-evaluation.md) för att föreslå en utbildnings princip baserat på dina aktuella händelser.


## <a name="security"></a>Säkerhet

### <a name="the-api-key-for-my-loop-has-been-compromised-what-can-i-do"></a>API-nyckeln för min slinga har komprometterats. Vad kan jag göra?

Du kan återskapa en nyckel när du har bytt ut klienterna så att de använder den andra nyckeln. Med två nycklar kan du sprida nyckeln på ett Lazy sätt utan att behöva ha några drift avbrott. Vi rekommenderar att du gör detta på ett regelbundet sätt som ett säkerhets mått.


## <a name="next-steps"></a>Nästa steg

[Konfigurera modell uppdaterings frekvensen](how-to-settings.md#model-update-frequency)