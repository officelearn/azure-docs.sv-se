---
title: Vad är Custom Decision Service?
titlesuffix: Azure Cognitive Services
description: Den här artikeln ger en översikt över Custom Decision Service.
services: cognitive-services
author: alekh
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-decision-service
ms.topic: overview
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: 273f2965a0fcaaa729175c5232da1aba69589eec
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364219"
---
# <a name="what-is-custom-decision-service"></a>Vad är Custom Decision Service?

I ett normalt webb- eller mobilprogram länkar en förstasida till flera artiklar eller andra typer av innehåll. Det kan begära att Custom Decision Service rangordnar artiklarna på förstasidan medan den läses in. Så när en användare väljer en artikel genom att klicka på den, kan en andra begäran skickas till Custom Decision Service, som då loggar resultatet av användarens beslut.

Custom Decision Service är lätt att använda, eftersom funktionen bara kräver en RSS-feed för ditt innehåll och några få rader med JavaScript som läggs till i programmet.

Custom Decision Service konverterar ditt innehåll till funktioner för maskininlärning. Systemet använder dessa funktioner för att förstå ditt innehåll när det gäller dess text, bilder, videor och genomsnittligt sentiment. Det använder flera andra [Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services), t.ex. [Entitetslänkning](../entitylinking/home.md), [Textanalys](../text-analytics/overview.md), [Känsloigenkänning](../emotion/home.md) och [Visuellt innehåll](../computer-vision/home.md).

Några vanliga användningsområden för Custom Decision Service är:

* Anpassa artiklar på en nyhetswebbplats
* Anpassa videoinnehåll i en mediaportal
* Optimera annonsplaceringar eller webbsidor som annonsen leder till
* Rangordning rekommenderade objekt på en shoppingwebbplats.

Custom Decision Service är för närvarande i *kostnadsfri offentlig förhandsversion*. Den kan anpassa en lista över artiklar på en webbplats eller i en app. Funktionsextraheringen fungerar bäst för engelskspråkligt innehåll. [Begränsad funktionalitet](../text-analytics/overview.md) erbjuds för andra språk, t.ex. spanska, franska, tyska, portugisiska eller japanska. Den här dokumentationen ändras när de nya funktionerna blir tillgängliga.

Custom Decision Service kan användas i program som inte ingår i domänen för innehållsanpassning. Dessa program kan vara ett bra alternativ för en anpassad förhandsversion. [Kontakta oss](https://azure.microsoft.com/overview/sales-number/) om du vill veta mer.

## <a name="api-usage-modes"></a>API-användningslägen

Custom Decision Service kan användas på både webbsidor och i mobilappar. API:erna kan anropas från en webbläsare eller en app. API-användningen är liknande i båda fallen, men vissa detaljer skiljer sig åt.

## <a name="glossary-of-terms"></a>Ordlista

Flera termer förekommer ofta i den här dokumentationen:

* **Åtgärdsuppsättning**: Den uppsättning av innehållsposter som Custom Decision Service ska rangordna. Den här uppsättningen kan anges som en *RSS*- eller *Atom*-slutpunkt.
* **Rangordning**: Varje begäran till Custom Decision Service anger en eller flera åtgärdsuppsättningar. Systemet svarar genom att välja alla innehållsalternativ från dessa uppsättningar och returnera dem i rangordning.
* **Återanropsfunktion**: Den här funktionen, som du anger, återger innehållet i ditt användargränssnitt. Innehållet är sorteras efter rangordningen som returneras av Custom Decision Service.
* **Utmärkelse**: Ett mått på hur användaren svarat på det återgivna innehållet. Custom Decision Service mäter användarens svar med hjälp av klick. Klickningarna rapporteras till systemet med hjälp av anpassad kod som infogats i ditt program.

## <a name="next-steps"></a>Nästa steg

* [Registrera ditt program](custom-decision-service-get-started-register.md) med Custom Decision Service
* Kom igång med att optimera [en webbsida](custom-decision-service-get-started-browser.md) eller [en smartphone-app](custom-decision-service-get-started-app.md).
* Läs den [API-referens](custom-decision-service-api-reference.md) mer information om de angivna funktionerna.