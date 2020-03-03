---
title: Fel sökning – Personanpassare
description: Den här artikeln innehåller svar på vanliga fel söknings frågor om Personanpassare.
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: 39db920b663ace12f2fc6ca35f9082a791da1541
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228035"
---
# <a name="personalizer-troubleshooting"></a>Fel sökning av personanpassa

Den här artikeln innehåller svar på vanliga fel söknings frågor om Personanpassare.

## <a name="transaction-errors"></a>Transaktions fel

<details>
<summary><b>Jag får ett svar för HTTP 429 (för många begär Anden) från tjänsten. Vad kan jag göra?</b></summary>

**Svar**: om du har valt en kostnads fri pris nivå när du skapade personanpassa instansen, finns det en kvot gräns för antalet ranknings begär Anden som tillåts. Granska API-anrops frekvensen för rang-API: n (i fönstret mått i Azure Portal för din personanpassa resurs) och justera pris nivån (i fönstret pris nivå) om din anrops volym förväntas öka efter tröskelvärdet för den valda pris nivån.

</details>

<details>
<summary><b>Jag får ett 5xx-fel vid rang-eller belönings-API: er. Vad ska jag göra?</b></summary>

**Svar**: de här problemen bör vara transparenta. Om de fortsätter kan du kontakta supporten genom att välja **ny supportbegäran** i avsnittet **support och fel sökning** i Azure Portal för din personanpassa resurs.

</details>

## <a name="learning-loop"></a>Inlärnings slinga

<details>
<summary>
<b>Inlärnings slingan inte uppnår en 100%-matchning till systemet utan någon personligt tillverkare. Hur gör jag för att åtgärda detta?</b></summary>

**Svar**: anledningen till att du inte uppnår ditt mål med inlärnings slingan:
* Inte tillräckligt med funktioner som skickas med rang-API-anrop
* Buggar i de funktioner som skickas – till exempel skicka icke-aggregerade funktions data, till exempel tidsstämplar till Range-API
* Buggar med loop-bearbetning – till exempel att inte skicka belönings data till belönings-API för händelser

För att åtgärda detta måste du ändra bearbetningen genom att antingen ändra de funktioner som skickats till slingan, eller se till att belöningen är en korrekt utvärdering av kvaliteten på rangens svar.

</details>

<details>
<summary>
<b>Inlärnings slingan verkar inte lära sig. Hur gör jag för att åtgärda detta?</b></summary>

**Svar**: inlärnings slingan behöver några tusen belönings samtal innan ranknings anropen prioriteras effektivt.

Om du är osäker på hur din inlärnings slinga för närvarande fungerar, kör du en [offline-utvärdering](concepts-offline-evaluation.md)och tillämpar den korrigerade inlärnings principen.

</details>

<details>
<summary><b>Jag vill få ranknings resultat med samma sannolikhet för alla objekt. Hur gör jag för att vet du att personanpassa är inlärning?</b></summary>

**Svar**: en personanpassare returnerar samma sannolikhet i ett rang-API-resultat när den precis har startat och har en _Tom_ modell, eller när du återställer en egen loop och din modell fortfarande ligger inom **modell uppdateringens frekvens** period.

När den nya uppdaterings perioden börjar används den uppdaterade modellen och du ser sannolikheten för ändringen.

</details>

<details>
<summary><b>Inlärnings-slingan var inlärning men verkar inte längre, och kvaliteten på ranknings resultatet är inte så bra. Vad ska jag göra?</b></summary>

**Svar**:
* Se till att du har slutfört och tillämpat en utvärdering i Azure Portal för denna personanpassa resurs (inlärnings slinga).
* Se till att alla förmåner skickas, via belönings-API: et och bearbetas.

</details>


<details>
<summary><b>Hur gör jag för att vet du att inlärnings slingan uppdateras regelbundet och används för att räkna data?</b></summary>

**Svar**: du kan se tiden då modellen senast uppdaterades på sidan modell- **och inlärnings inställningar** i Azure Portal. Om du ser en gammal tidsstämpel är det troligt att du inte skickar rang-och belönings samtal. Om tjänsten inte har några inkommande data uppdateras inte inlärningen. Om inlärnings-slingan inte uppdateras tillräckligt ofta kan du redigera **uppdaterings frekvensen**för loopen.

</details>

## <a name="offline-evaluations"></a>Offlineutvärderingar

<details>
<summary><b>En offline-utvärderings funktions prioritet returnerar en lång lista med hundratals eller tusentals objekt. Vad hände?</b></summary>

**Svar**: Detta beror vanligt vis på tidsstämplar, användar-ID: n eller några andra detaljerade funktioner som skickas i.

</details>

<details>
<summary><b>Jag skapade en offline-utvärdering och den lyckades nästan omedelbart. Varför är det? Jag ser inga resultat?</b></summary>

**Svar**: offline-utvärderingen använder de tränade modell data från händelserna under den tids perioden. Om du inte skickade några data under tids perioden mellan start-och slut tiden för utvärderingen, slutförs det utan några resultat. Skicka en ny offline-utvärdering genom att välja ett tidsintervall med händelser som du vet har skickats till Personanpassaren.

</details>


## <a name="learning-policy"></a>Utbildningsprincip

<details>
<summary><b>Hur gör jag för att importera en utbildnings princip?</b></summary>

**Svar**: Lär dig mer om [begrepp för inlärnings principer](concept-active-learning.md#understand-learning-policy-settings) och [hur du använder](how-to-manage-model.md) en ny utbildnings princip. Om du inte vill välja en utbildnings princip kan du använda [offline-utvärderingen](how-to-offline-evaluation.md) för att föreslå en utbildnings princip baserat på dina aktuella händelser.

</details>

## <a name="security"></a>Säkerhet

<details>
<summary><b>API-nyckeln för min slinga har komprometterats. Vad kan jag göra?</b></summary>

**Svar**: du kan återskapa en nyckel när du har bytt ut klienterna så att de använder den andra nyckeln. Med två nycklar kan du sprida nyckeln på ett Lazy sätt utan att behöva ha några drift avbrott. Vi rekommenderar att du gör detta på ett regelbundet sätt som ett säkerhets mått.

</details>

## <a name="next-steps"></a>Nästa steg

[Konfigurera modell uppdaterings frekvensen](how-to-settings.md#model-update-frequency)