---
title: Aktiva och inaktiva händelser - Personalizer
description: I den här artikeln beskrivs användningen av aktiva och inaktiva händelser i Personalizer-tjänsten.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: a8f27542208965e2b820b9fc45cfcc5353a7f193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624257"
---
# <a name="active-and-inactive-events"></a>Aktiva och inaktiva händelser

En **aktiv** händelse är ett samtal till Rank där du vet att du kommer att visa resultatet för kunden och bestämma belöningspoängen. Det här är standardbeteendet.

En **inaktiv** händelse är ett anrop till Rank där du inte är säker på om användaren någonsin kommer att se den rekommenderade åtgärden, på grund av affärslogik. På så sätt kan du ignorera händelsen så att Personalizer inte tränas med standardbelöningen. Inaktiva händelser bör inte anropa belönings-API:et.

Det är viktigt att inlärningsslingan känner till den faktiska typen av händelse. En inaktiv händelse kommer inte att ha ett belöningssamtal. En aktiv händelse bör ha ett belöningsanrop, men om API-anropet aldrig görs tillämpas standardbelöningspoängen. Ändra status för en händelse från inaktiv till aktiv så snart du vet att den kommer att påverka användarupplevelsen.

## <a name="typical-active-events-scenario"></a>Typiskt scenario för aktiva händelser

När ditt program anropar Rank API får du åtgärden, som programmet ska visa i fältet **rewardActionId.**  Från det ögonblicket förväntar sig Personalizer ett belöningssamtal med en belöningspoäng som har samma eventId. Belöningspoängen används för att träna modellen för framtida Rank-samtal. Om inget belöningssamtal tas emot för eventId tillämpas en standardbelöning. [Standardbelöningar](how-to-settings.md#configure-rewards-for-the-feedback-loop) anges på din Personalizer-resurs i Azure-portalen.

## <a name="other-event-type-scenarios"></a>Andra händelsetypsscenarier

I vissa fall kan programmet behöva anropa Rank innan det ens vet om resultatet kommer att användas eller visas för användaren. Detta kan inträffa i situationer där till exempel sidåtergivningen av marknadsfört innehåll skrivs över av en marknadsföringskampanj. Om resultatet av Rank-samtalet aldrig användes och användaren aldrig såg det ska du inte skicka ett motsvarande belöningssamtal.

Vanligtvis inträffar dessa scenarier när:

* Du föringår från användargränssnittet som användaren kanske eller kanske inte får se.
* Ditt program gör prediktiv anpassning där Rank-anrop görs med lite kontext i realtid och programmet kanske eller kanske inte använder utdata.

I dessa fall använder Du Personalizer för att anropa Rank och begär att händelsen ska vara _inaktiv_. Personalizer förväntar sig inte en belöning för den här händelsen, och den kommer inte att tillämpa en standardbelöning.

Senare i din affärslogik, om programmet använder informationen från Rank-anropet, _aktivera_ bara händelsen. Så snart evenemanget är aktivt förväntar sig Personalizer en händelsebelöning. Om inget explicit samtal görs till belönings-API:et tillämpar Personalizer en standardbelöning.

## <a name="inactive-events"></a>Inaktiva händelser

Om du vill inaktivera utbildning för `learningEnabled = False`en händelse anropar du Rank med hjälp av .

För en inaktiv händelse aktiveras inlärning implicit om du skickar en `activate` belöning för eventId eller anropar API:et för den eventId.

## <a name="next-steps"></a>Nästa steg

* Läs om hur du [avgör belöningspoäng och vilka data du bör tänka på.](concept-rewards.md)
