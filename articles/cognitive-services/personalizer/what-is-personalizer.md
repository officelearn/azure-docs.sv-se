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
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 99750971e11171c0b315cac38089c216d42c7ba6
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663670"
---
# <a name="what-is-personalizer"></a>Vad är Personanpassning?

Personanpassning i Azure är en molnbaserad API-tjänst som gör det möjligt att välja det bästa alternativet att visa användarna och drar lärdom av beteende i realtid.

* Ange information om dina användare och innehåll och ta emot den främsta åtgärden för att visa dina användare. 
* Du behöver inte rensa och märka data innan du använder Personanpassaren.
* Ge dig feedback till Personanpassaren när det passar dig. 
* Visa analys i real tid. 
* Använd Personanpassare som en del av en större data vetenskaps ansträngning för att verifiera befintliga experiment.

## <a name="how-does-personalizer-work"></a>Hur fungerar Personanpassare?

I personanpassaren används maskin inlärnings modeller för att identifiera vilken åtgärd som ska rangordnas högst i en kontext. Klient programmet innehåller en lista över möjliga åtgärder, med information om dem. och information om kontexten, som kan innehålla information om användaren, enheten osv. Personanpassaren bestämmer vilken åtgärd som ska vidtas. När ditt klient program använder den valda åtgärden ger det feedback till Personanpassaren i form av en belönings poäng. När feedbacken har tagits emot uppdaterar Personanpassaren automatiskt sin egen modell som används för framtida rangordning.

## <a name="how-do-i-use-the-personalizer"></a>Hur gör jag för att använder du Personanpassaren?

![Använda Personanpassaren för att välja vilken video som ska visas för en användare](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Välj en upplevelse i din app för att anpassa.
1. Skapa och konfigurera en instans av anpassnings tjänsten i Azure Portal. Varje instans är en personanpassa slinga.
1. Använd SDK för att anropa Personanpassaren med information (_funktioner_) om dina användare och innehållet (_åtgärder_). Du behöver inte tillhandahålla rena, märkta data innan du använder Personanpassaren. 
1. I klient programmet visar du användaren den åtgärd som valts av Personanpassaren.
1. Använd SDK för att ge feedback till en person som anger om användaren har valt att utföra åtgärden. Detta är en _belönings Poäng_, vanligt vis mellan-1 och 1.
1. Visa analyser i Azure Portal för att utvärdera hur systemet fungerar och hur dina data bidrar till anpassning.

## <a name="where-can-i-use-personalizer"></a>Var kan jag använda Personanpassare?

Ditt klient program kan till exempel lägga till en Personanpassare till:

* Anpassa vilken artikel som är markerad på en nyhets webbplats.    
* Optimera AD-placering på en webbplats.
* Visa ett personligt "rekommenderat objekt" på en shopping webbplats.
* Föreslå användar gränssnitts element, till exempel filter som ska användas för ett bestämt foto.
* Välj ett svar för chatt-roboten för att klargöra användar avsikten eller föreslå en åtgärd.
* Prioritera förslag på vad en användare ska göra som nästa steg i en affärs process.

## <a name="personalization-for-developers"></a>Anpassning för utvecklare

Tjänsten för personliga tjänster har två API: er:

* Skicka information (_funktioner_) om dina användare och innehållet (_åtgärder_) för att anpassa. En personanpassare svarar med den främsta åtgärden.
* Skicka feedback till Personanpassare om hur väl rangordningen fungerar som ett tal vanligt vis mellan 0 och 1 (föregående avsnitt, 1 och 1). 

![Grundläggande sekvens med händelser för anpassning](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa en feedback-slinga iC#](csharp-quickstart-commandline-feedback-loop.md)
* [Använd den interaktiva demonstrationen](https://personalizationdemo.azurewebsites.net/)
