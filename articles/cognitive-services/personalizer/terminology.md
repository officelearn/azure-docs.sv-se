---
title: Terminologi – Personanpassare
description: 'Personanpassare använder terminologin från förstärkt inlärning. Dessa villkor används i Azure Portal och API: erna.'
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: f75437c5afd5d3fd7f7570079be410d3db1ca8db
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624279"
---
# <a name="terminology"></a>Terminologi

Personanpassare använder terminologin från förstärkt inlärning. Dessa villkor används i Azure Portal och API: erna.

## <a name="conceptual-terminology"></a>Konceptuell terminologi

* **Inlärnings slinga**: du skapar en personanpassa resurs, som kallas _inlärnings slinga_, för alla delar av ditt program som kan dra nytta av anpassning. Om du har mer än en upplevelse att anpassa, skapar du en slinga för var och en.

* **Modell**: en personanpassa modell som samlar in alla data som lärts om användarens beteende, hämtar tränings data från kombinationen av argument som du skickar till ranknings-och belönings samtal och med ett inlärnings beteende som fastställs av inlärnings principen.

## <a name="personalizer-configuration"></a>Personanpassa konfiguration

Personanpassaren har kon figurer ATS från [Azure Portal](https://portal.azure.com).

* **Fördelar**: konfigurera standardvärden för belönings vänte tid, standard belöning och agg regerings princip för belöning.

* **Undersökning**: Konfigurera procent andelen rang anrop som ska användas för utforskning

* **Modell uppdaterings frekvens**: hur ofta modellen omtränas.

* **Datakvarhållning**: hur många dagars data som ska lagras. Detta kan påverka utvärderingar offline, som används för att förbättra din inlärnings slinga.

## <a name="use-rank-and-reward-apis"></a>Använd rang-och belönings-API: er

* **Rangordning**: med hjälp av åtgärder med funktioner och kontext funktioner kan du använda utforska eller utnyttja för att returnera den översta åtgärden (innehålls objekt).

    * **Åtgärder**: åtgärder är innehålls objekt, till exempel produkter eller kampanjer, för att välja bland. Personanpassa väljer den främsta åtgärden (returnerat åtgärds-ID för belöning) som ska visas för användarna via rang-API: et.

    * **Kontext**: Ange en mer exakt rangordning genom att ange information om din kontext, till exempel:
        * Användaren.
        * Enheten som de är på.
        * Aktuell tid.
        * Andra data om den aktuella situationen.
        * Historiska data om användaren eller kontexten.

        Ditt specifika program kan ha annan Sammanhangs information.

    * **[Funktioner](concepts-features.md)** : en enhet med information om ett innehålls objekt eller en användar kontext. Se till att endast använda funktioner som är aggregerade. Använd inte vissa tider, användar-ID: n eller andra icke-aggregerade data som funktioner.

        * En _Åtgärds funktion_ är metadata om innehållet.
        * En _kontext funktion_ är metadata om kontexten där innehållet visas.

* **Utforskning**: tjänsten personanpassa är en utforskande av när du i stället för att returnera den bästa åtgärden väljer en annan åtgärd för användaren. Tjänsten personanpassa förhindrar drift, stagnation och kan anpassas till pågående användar beteende genom att utforska.

* **Utnyttjande**: tjänsten personanpassa använder den aktuella modellen för att avgöra den bästa åtgärden baserat på tidigare data.

* **Experimentets varaktighet**: den tid som tjänsten personanpassa förväntar sig en belöning, med början från det ögonblick då rang samtalet inträffade för händelsen.

* **Inaktiva händelser**: en inaktiv händelse är en där du anropade rang, men du är inte säker på att användaren någonsin kommer att se resultatet, på grund av klient programs beslut. Inaktiva händelser gör att du kan skapa och lagra anpassnings resultat och sedan välja att ta bort dem senare utan att påverka Machine Learning-modellen.


* **Belöning**: ett mått på hur användaren svarade på rang-API: et returnerade belönings åtgärds-ID, som ett resultat mellan 0 och 1. Värdet 0 till 1 anges av din affärs logik, baserat på hur valet hjälpte dig att uppnå dina affärs mål för anpassning. Inlärnings slingan lagrar inte den här belöningen som enskild användar historik.

## <a name="offline-evaluations"></a>Offlineutvärderingar

* **Utvärdering**: en offline-utvärdering avgör den bästa inlärnings policyn för din loop baserat på loopens data.

* **Inlärnings princip**: hur personanpassare en modell i varje händelse kommer att fastställas av vissa parametrar som påverkar hur Machine Learning-algoritmen fungerar. En ny inlärnings slinga börjar med en standard **inlärnings princip**som kan ge måttliga prestanda. När du kör [utvärderingarna](concepts-offline-evaluation.md)skapar personanpassaren nya inlärnings principer som är specifikt optimerade för användnings fall i slingan. Personanpassaren kommer att utföra avsevärt bättre med principer som är optimerade för varje enskild slinga, som genereras under utvärderingen. Inlärnings policyn heter _utbildnings inställningar_ på **modell-och utbildnings inställningarna** för personanpassa-resursen i Azure Portal.