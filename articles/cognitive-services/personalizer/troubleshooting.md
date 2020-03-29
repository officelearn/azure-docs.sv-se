---
title: Felsökning - Personalizer
description: Den här artikeln innehåller svar på vanliga felsökningsfrågor om Personalizer.
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: 904953f028eb31afe42cf477ac05be43e8b72a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336019"
---
# <a name="personalizer-troubleshooting"></a>Felsökning av personalizer

Den här artikeln innehåller svar på vanliga felsökningsfrågor om Personalizer.

## <a name="transaction-errors"></a>Transaktionsfel

<details>
<summary><b>Jag får ett HTTP 429 (för många begäranden) svar från tjänsten. Vad kan jag göra?</b></summary>

**Svar**: Om du valde en kostnadsfri prisnivå när du skapade Personalizer-instansen finns det en kvotgräns för antalet Rank-begäranden som tillåts. Granska din API-anropshastighet för Rank API (i fönstret Mått i Azure-portalen för din Personalizer-resurs) och justera prisnivån (i fönstret Prisnivå) om samtalsvolymen förväntas öka utöver tröskelvärdet för vald prisnivå.

</details>

<details>
<summary><b>Jag får ett 5xx fel på Rank eller Reward API: er. Vad ska jag göra?</b></summary>

**Svar**: Dessa frågor bör vara öppna. Om de fortsätter kontaktar du supporten genom att välja **Ny supportbegäran** i avsnittet **Support + felsökning** i Azure-portalen för din Personalizer-resurs.

</details>

## <a name="learning-loop"></a>Lärande loop

<details>
<summary>
<b>Inlärningsslingan uppnår inte en 100% matchning till systemet utan Personalizer. Hur åtgärdar jag detta?</b></summary>

**Svar**: Anledningarna till att du inte uppnår ditt mål med inlärningsslingan:
* Det finns inte tillräckligt med funktioner som skickas med Rank API-anrop
* Buggar i de funktioner som skickas - till exempel skicka icke-aggregerade funktionsdata som tidsstämplar till Rank API
* Buggar med loopbearbetning – till exempel att inte skicka belöningsdata till Reward API för händelser

För att åtgärda det måste du ändra bearbetningen genom att antingen ändra de funktioner som skickas till loopen eller se till att belöningen är en korrekt utvärdering av kvaliteten på rankens svar.

</details>

<details>
<summary>
<b>Inlärningsslingan verkar inte lära sig. Hur åtgärdar jag detta?</b></summary>

**Svar**: Inlärningsslingan behöver några tusen belöningssamtal innan Rank-samtal prioriterar effektivt.

Om du är osäker på hur din utbildningsloop för närvarande beter sig kör du en [offlineutvärdering](concepts-offline-evaluation.md)och tillämpar den korrigerade inlärningsprincipen.

</details>

<details>
<summary><b>Jag får rank resultat med alla samma sannolikheter för alla objekt. Hur vet jag att Personalizer lär sig?</b></summary>

**Svar:** Personalizer returnerar samma sannolikheter i ett Rank API-resultat när det just har startat och har en _tom_ modell, eller när du återställer Personalizer Loop, och din modell är fortfarande inom din **modell uppdateringsfrekvensperiod.**

När den nya uppdateringsperioden börjar används den uppdaterade modellen och sannolikheterna ändras.

</details>

<details>
<summary><b>Inlärningsslingan lärde sig men verkar inte lära sig längre, och kvaliteten på Rank resultaten är inte så bra. Vad ska jag göra?</b></summary>

**Svar:**
* Kontrollera att du har slutfört och tillämpat en utvärdering i Azure-portalen för den Personalizer-resursen (utbildningsloop).
* Se till att alla belöningar skickas, via belönings-API:et, och bearbetas.

</details>


<details>
<summary><b>Hur vet jag att lärslingan uppdateras regelbundet och används för att få mina data att göra mål?</b></summary>

**Svar**: Du kan hitta den tid då modellen senast uppdaterades på sidan **Modell- och utbildningsinställningar** i Azure-portalen. Om du ser en gammal tidsstämpel beror det sannolikt på att du inte skickar rank- och belöningssamtalen. Om tjänsten inte har några inkommande data uppdateras inte inlärningen. Om lärorikloopen inte uppdateras tillräckligt ofta kan du redigera loopens **modelluppdateringsfrekvens**.

</details>

## <a name="offline-evaluations"></a>Offlineutvärderingar

<details>
<summary><b>En offlineutvärderings funktionsvikt returnerar en lång lista med hundratals eller tusentals objekt. Vad hände?</b></summary>

**Svar**: Detta beror vanligtvis på tidsstämplar, användar-ID eller några andra finkorniga funktioner som skickas in.

</details>

<details>
<summary><b>Jag skapade en offline utvärdering och det lyckades nästan omedelbart. Varför är det så? Ser jag inga resultat?</b></summary>

**Svar**: Offlineutvärderingen använder tränade modelldata från händelserna under den tidsperioden. Om du inte har skickat några data under tidsperioden mellan utvärderingens start- och sluttid fylls de utan några resultat. Skicka in en ny offlineutvärdering genom att välja ett tidsintervall med händelser som du vet skickades till Personalizer.

</details>


## <a name="learning-policy"></a>Utbildningspolitik

<details>
<summary><b>Hur importerar jag en utbildningspolitik?</b></summary>

**Svar**: Läs mer om [begrepp för lärande och](concept-active-learning.md#understand-learning-policy-settings) hur du [tillämpar](how-to-manage-model.md) en ny inlärningspolicy. Om du inte vill välja en utbildningsprincip kan du använda [offlineutvärderingen](how-to-offline-evaluation.md) för att föreslå en utbildningspolitik, baserat på dina aktuella händelser.

</details>

## <a name="security"></a>Säkerhet

<details>
<summary><b>API-nyckeln för min loop har komprometterats. Vad kan jag göra?</b></summary>

**Svar**: Du kan återskapa en nyckel efter att ha bytt dina klienter för att använda den andra nyckeln. Med två nycklar kan du sprida nyckeln på ett lat sätt utan att behöva ha några driftstopp. Vi rekommenderar att du gör detta på en vanlig cykel som en säkerhetsåtgärd.

</details>

## <a name="next-steps"></a>Nästa steg

[Konfigurera uppdateringsfrekvensen för modellen](how-to-settings.md#model-update-frequency)