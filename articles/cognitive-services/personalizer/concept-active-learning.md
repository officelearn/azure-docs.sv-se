---
title: Aktiva och inaktiva händelser – Personanpassare
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: aa6f53901f21dcb0726454d641a4a2a66007f9e0
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429050"
---
# <a name="active-and-inactive-events"></a>Aktiva och inaktiva händelser

När programmet anropar rang-API: et får du vilken åtgärd programmet ska visa i fältet rewardActionId.  Från och med den tidpunkten förväntar sig personanpassa ett belönings samtal med samma eventId. Belönings poängen kommer att användas för att träna modellen som ska användas för framtida Rangbaserade samtal. Om inget belönings anrop tas emot för eventId kommer en defaul-belöning att tillämpas. Standard förmåner upprättas i Azure-portalen.

I vissa fall kan programmet behöva anropa rang diametern även om resultatet ska användas eller visas för användaren. Detta kan inträffa i situationer där exempelvis sid åter givningen av framhävda innehåll skrivs över med en marknadsförings kampanj. Om resultatet av rang anropet aldrig har använts och användaren aldrig kunde se det, skulle det vara fel på att träna det med all belöning, noll eller på annat sätt.
Detta inträffar vanligt vis när:

* Du kanske för hands återger ett användar gränssnitt som användaren kanske inte kan se. 
* Ditt program kan göra en förutsägelse anpassning i vilka Rangbaserade anrop görs med mindre real tids sammanhang och deras utdata kan eventuellt inte användas av programmet. 

I dessa fall är det rätt sätt att använda Personanpassaren genom att anropa rangen som begär händelsen som _inaktiv_. Personanpassaren förväntar sig inte en belöning för den här händelsen och kommer inte att tillämpa någon standard belöning. Letr i din affärs logik, om programmet använder informationen från rang anropet, behöver du bara _Aktivera_ händelsen. Från den tidpunkt då händelsen är aktiv förväntar sig Personanpassan en belöning för evenemanget eller använder en standard belöning om inget uttryckligt anrop görs till belönings-API: et.

## <a name="get-inactive-events"></a>Hämta inaktiva händelser

Om du vill inaktivera utbildning för en händelse anropar du rang med `learningEnabled = False`.

Inlärningen för en inaktiv händelse aktive ras implicit om du skickar en belöning för eventId eller anropar `activate`-API: et för eventId.

## <a name="learning-settings"></a>Utbildnings inställningar

Inlärnings inställningarna bestämmer de exaktaste *parametrarna* för modell träningen. Två modeller av samma data, som har tränats på olika inlärnings inställningar, kommer att få en annan modell.

### <a name="import-and-export-learning-policies"></a>Importera och exportera utbildnings principer

Du kan importera och exportera learning policy-filer från Azure Portal. På så sätt kan du spara befintliga principer, testa dem, ersätta dem och arkivera dem i käll kods kontrollen som artefakter för framtida referens och granskning.

### <a name="learning-policy-settings"></a>Policy inställningar för inlärning

Inställningarna i **inlärnings principen** är inte avsedda att ändras. Ändra bara inställningarna när du förstår hur de påverkar Personanpassaren. Om du ändrar inställningar utan den här kunskapen kan det orsaka sido effekter, inklusive invalidering av anpassnings modeller.

### <a name="comparing-effectiveness-of-learning-policies"></a>Jämför effektiviteten för utbildnings principer

Du kan jämföra hur olika inlärnings principer har utförts mot tidigare data i personanpassa loggar genom att göra [offline-utvärderingar](concepts-offline-evaluation.md).

[Ladda upp dina egna utbildnings principer](how-to-offline-evaluation.md) för att jämföra med den aktuella inlärnings policyn.

### <a name="discovery-of-optimized-learning-policies"></a>Identifiering av optimerade utbildnings principer

En personanpassare kan skapa en mer optimerad inlärnings princip när du gör en [offline-utvärdering](how-to-offline-evaluation.md). En mer optimerad inlärnings policy, som visas som en bättre belöning i en offline-utvärdering, ger bättre resultat när de används online i Personanpassaren.

När en optimerad inlärnings princip har skapats kan du tillämpa den direkt på en Personligre så att den ersätter den aktuella principen omedelbart, eller så kan du spara den för ytterligare utvärdering och besluta i framtiden om du vill ta bort, Spara eller tillämpa den senare.
