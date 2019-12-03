---
title: Vad är Personanpassning?
titleSuffix: Azure Cognitive Services
description: Personanpassar är en molnbaserad API-tjänst som gör att du kan välja den bästa upplevelsen som ska visas för användarna, lära sig från deras real tids beteende.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: b5d38ffeda3600fd90c4ee84acdd29ed599886ae
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707950"
---
# <a name="what-is-personalizer"></a>Vad är Personanpassning?

Azure-personanpassa är en molnbaserad API-tjänst som gör att ditt program kan välja den bästa upplevelsen för användarna, lära sig från deras kollektiva real tids beteende.

* Ange information om dina användare och innehåll och ta emot den främsta åtgärden för att visa dina användare. 
* Du behöver inte rensa och märka data innan du använder Personanpassaren.
* Ge dig feedback till Personanpassaren när det passar dig. 
* Visa analys i real tid. 

Se en demonstration av [hur en personanpassare fungerar](https://personalizercontentdemo.azurewebsites.net/)

## <a name="how-does-personalizer-work"></a>Hur fungerar Personanpassare?

I personanpassaren används maskin inlärnings modeller för att identifiera vilken åtgärd som ska rangordnas högst i en kontext. Klient programmet innehåller en lista över möjliga åtgärder, med information om dem. och information om kontexten, som kan innehålla information om användaren, enheten osv. Personanpassaren bestämmer vilken åtgärd som ska vidtas. När ditt klient program använder den valda åtgärden ger det feedback till Personanpassaren i form av en belönings poäng. När feedbacken har tagits emot uppdaterar Personanpassaren automatiskt sin egen modell som används för framtida rangordning. Med tiden tränar vi en modell som kan föreslå den bästa åtgärden att välja i varje sammanhang baserat på deras funktioner.

## <a name="how-do-i-use-the-personalizer"></a>Hur gör jag för att använder du Personanpassaren?

![Använda Personanpassaren för att välja vilken video som ska visas för en användare](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Välj en upplevelse i din app för att anpassa.
1. Skapa och konfigurera en instans av anpassnings tjänsten i Azure Portal. Varje instans är en personanpassa slinga.
1. Använd [rang-API: et](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) för att anropa personanpassaren med information (_funktioner_) om dina användare och innehållet (_åtgärder_). Du behöver inte tillhandahålla rena, märkta data innan du använder Personanpassaren. API: er kan anropas direkt eller använda SDK: er som är tillgängliga för olika programmeringsspråk.
1. I klient programmet visar du användaren den åtgärd som valts av Personanpassaren.
1. Använd [belönings-API: et](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) för att ge feedback till en person som anger om användaren har valt att utföra åtgärden. Detta är en _[belönings Poäng](concept-rewards.md)_ .
1. Visa analyser i Azure Portal för att utvärdera hur systemet fungerar och hur dina data bidrar till anpassning.

## <a name="where-can-i-use-personalizer"></a>Var kan jag använda Personanpassare?

Ditt klient program kan till exempel lägga till en Personanpassare till:

* Anpassa vilken artikel som är markerad på en nyhets webbplats.    
* Optimera AD-placering på en webbplats.
* Visa ett personligt "rekommenderat objekt" på en shopping webbplats.
* Föreslå användar gränssnitts element, till exempel filter som ska användas för ett bestämt foto.
* Välj ett svar för chatt-roboten för att klargöra användar avsikten eller föreslå en åtgärd.
* Prioritera förslag på vad en användare ska göra som nästa steg i en affärs process.

Personanpassaren är inte en tjänst för att spara och hantera information om användar profiler, eller för att logga enskilda användares inställningar eller historik. Personanpassaren lär sig från varje interaktions funktion i en enskild modell som kan få högsta möjliga belöning när liknande funktioner inträffar. 

## <a name="personalization-for-developers"></a>Anpassning för utvecklare

Tjänsten för personliga tjänster har två API: er:

* *Rankning*: Använd rang-API: et för att avgöra vilken _åtgärd_ som ska visas i den aktuella _kontexten_. Åtgärder skickas som en matris med JSON-objekt, med ID och information (_funktioner_) om var och en. kontexten skickas som ett annat JSON-objekt. API: t returnerar de actionId som programmet ska återge för användaren.
* *Belöning*: när din användare interagerar med ditt program, mäter du hur väl anpassningen fungerar som ett tal mellan 0 och 1 och skickar den som en [belönings Poäng](concept-rewards.md). 

![Grundläggande sekvens med händelser för anpassning](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Nästa steg

* [Vad är nytt i Personanpassaren?](whats-new.md)
* [Hur fungerar personanpassa?](how-personalizer-works.md)
* [Vad är förstärknings inlärning?](concepts-reinforcement-learning.md)
* [Lär dig mer om funktioner och åtgärder för rang förfrågan](concepts-features.md)
* [Läs om hur du avgör poängen för belönings förfrågan](concept-rewards.md)
* [Använd den interaktiva demonstrationen](https://personalizationdemo.azurewebsites.net/)
