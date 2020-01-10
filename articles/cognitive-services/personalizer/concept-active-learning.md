---
title: Aktiva och inaktiva händelser – Personanpassare
titleSuffix: Azure Cognitive Services
description: Den här artikeln beskriver hur du använder aktiva och inaktiva händelser, inlärnings inställningar och utbildnings principer i tjänsten personanpassa.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 90658e030c907a9fd99dd8fb9a6e90698d72b1f0
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834466"
---
# <a name="active-and-inactive-events"></a>Aktiva och inaktiva händelser

När programmet anropar rang-API: et får du den åtgärd som programmet ska visa i fältet **rewardActionId** .  Från och med den tidpunkten förväntar sig personanpassa ett belönings samtal som har samma eventId. Belönings poängen kommer att användas för att träna modellen för framtida Rangbaserade samtal. Om inget belönings anrop tas emot för eventId tillämpas en standard belöning. Standard förmåner anges i Azure Portal.

I vissa fall kan programmet behöva anropa rang innan det vet om resultatet ska användas eller visas för användaren. Detta kan inträffa i situationer där exempelvis sid åter givningen av framhävda innehåll skrivs över av en marknadsförings kampanj. Om resultatet av rang anropet aldrig har använts och användaren aldrig har sett det, ska du inte skicka ett motsvarande belönings samtal.

De här scenarierna inträffar vanligt vis när:

* Du föråterger användar gränssnittet som användaren kan eller inte kan se.
* Ditt program gör en förutsägelse anpassning i vilken ranknings anrop görs med en liten real tids kontext och programmet kan eventuellt inte använda utdata.

I dessa fall använder du Personanpassare för att anropa rang, vilket begär att händelsen ska vara _inaktiv_. Personanpassaren förväntar sig inte en belöning för den här händelsen och kommer inte att tillämpa en standard belöning.
Om programmet använder informationen från rang anropet senare i din affärs logik _aktiverar_ du bara händelsen. Så snart händelsen är aktiv förväntar sig en händelse genom att göra en händelse belöning. Om inget uttryckligt anrop görs till belönings-API: et, tillämpar Personanpassaren en standard belöning.

## <a name="inactive-events"></a>Inaktiva händelser

Om du vill inaktivera utbildning för en händelse anropar du rang genom att använda `learningEnabled = False`. Vid inaktivitet aktive ras inlärningen implicit om du skickar en belöning för eventId eller anropar `activate` API för eventId.

## <a name="learning-settings"></a>Utbildnings inställningar

Inlärnings inställningarna bestämmer modell utbildningens *Egenskaper* . Två modeller av samma data som har tränats på olika inlärnings inställningar kommer att upphöra annorlunda.

### <a name="import-and-export-learning-policies"></a>Importera och exportera utbildnings principer

Du kan importera och exportera filer för inlärnings principer från Azure Portal. Använd den här metoden för att spara befintliga principer, testa dem, ersätta dem och arkivera dem i käll kods kontrollen som artefakter för framtida referens och granskning.

Lär dig [hur du](how-to-learning-policy.md) importerar och exporterar en utbildnings princip.

### <a name="understand-learning-policy-settings"></a>Förstå policy inställningar för inlärning

Inställningarna i inlärnings principen är inte avsedda att ändras. Ändra endast inställningar om du förstår hur de påverkar Personanpassaren. Utan den här kunskapen kan du orsaka problem, t. ex. invaliderar anpassnings modeller.

I personanpassaren används [vowpalwabbit](https://github.com/VowpalWabbit) för att träna och räkna upp händelser. Läs vowpalwabbit- [dokumentationen](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) om hur du redigerar inlärnings inställningarna med hjälp av vowpalwabbit. När du har rätt kommando rads argument sparar du kommandot till en fil med följande format (Ersätt egenskap svärdet argument med önskat kommando) och laddar upp filen för att importera inlärnings inställningar i fönstret **modell-och inlärnings inställningar** i Azure Portal för din personanpassa resurs.

Följande `.json` är ett exempel på en utbildnings princip.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

### <a name="compare-learning-policies"></a>Jämför inlärnings principer

Du kan jämföra hur olika inlärnings principer fungerar mot tidigare data i personanpassa loggar genom att göra [offline-utvärderingar](concepts-offline-evaluation.md).

[Ladda upp dina egna utbildnings principer](how-to-learning-policy.md) för att jämföra dem med den aktuella inlärnings policyn.

### <a name="optimize-learning-policies"></a>Optimera inlärnings principer

En personanpassare kan skapa en optimerad utbildnings princip i en [offline-utvärdering](how-to-offline-evaluation.md). En optimerad utbildnings princip som har bättre fördelar i en offline-utvärdering ger bättre resultat när den används online i personanpassa företaget.

När du har optimerat en inlärnings princip kan du tillämpa den direkt på en Personanpassare så att den omedelbart ersätter den aktuella principen. Eller så kan du spara den optimerade principen för ytterligare utvärdering och senare bestämma om du vill ignorera, Spara eller tillämpa den.
