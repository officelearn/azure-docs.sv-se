---
title: Vad är anpassad beslut Service? -Kognitiva azure-tjänster | Microsoft Docs
description: Den här artikeln översikter Azure anpassad beslut Service ett moln-baserad API för kontextuella beslutsfattande som ökar skärpan upplevelse.
services: cognitive-services
author: alekh
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/08/2018
ms.author: slivkins;marcozo;alekh;marossi
ms.openlocfilehash: 774467446513dcd7ade7255d998b11f41824cafe
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "35356297"
---
# <a name="what-is-custom-decision-service"></a>Vad är anpassad beslut Service?

I en typisk webb- eller mobilprogram framsidan länkar till flera artiklar eller andra typer av innehåll. Den kan begära anpassade beslut tjänsten rank artiklar som ingår i den framsidan som främre sidan läses in. Så, när en användare väljer en artikel genom att klicka på den, en andra begäran kunde skickas till tjänsten anpassad beslut, som kommer att logga in resultatet av att användaren beslut.

Anpassade beslut tjänsten är enkla att använda, eftersom den kräver bara en RSS-feed för ditt innehåll och några få rader med JavaScript som ska läggas till i ditt program.

Anpassade beslut tjänsten konverterar innehållet till funktioner för machine learning. Systemet använder dessa funktioner för att förstå ditt innehåll med avseende på dess text, bilder, videor och övergripande sentiment. Den använder flera andra [Microsoft kognitiva Services](https://www.microsoft.com/cognitive-services), till exempel [entitet länka](../entitylinking/home.md), [textanalys](../text-analytics/overview.md), [Känslo](../emotion/home.md), och [Datorn Vision](../computer-vision/home.md).

Några vanliga användningsområden för anpassad beslut tjänsten omfattar:

* Anpassa artiklar på en webbplats för Nyheter
* Anpassa videoinnehåll på media-portalen
* Optimera ad placeringar eller webbsidor som ad leder till
* Rangordning rekommenderade objekt på en shopping webbplats.

Anpassade beslut-tjänsten är för närvarande i *kostnadsfri förhandsversion*. Det kan anpassa en lista över artiklar på en webbplats eller en app. Funktionen uppackningen fungerar bäst för innehåll engelska. [Begränsad funktionalitet](../text-analytics/overview.md) erbjuds för andra språk som spanska, franska, tyska, portugisiska och japanska. Den här dokumentationen kommer att ändras allteftersom nya funktioner blir tillgängliga.

Anpassad beslut tjänst kan användas i program som inte ingår i domänen innehåll anpassning. Dessa program kanske passar bra för en anpassad förhandsgranskning. [Kontakta oss](https://azure.microsoft.com/overview/sales-number/) vill veta mer.

## <a name="api-usage-modes"></a>Lägen för användning av API

Anpassad beslut tjänst kan användas både webbsidor och mobila appar. API: er kan anropas från en webbläsare eller en app. API-användning är liknande på både, men vissa uppgifter är olika.

## <a name="glossary-of-terms"></a>Ordlista

Flera villkor ofta uppstår i den här dokumentationen:

* **Åtgärden Ange**: uppsättningen innehållsobjekt för anpassad beslut tjänsten till rang. Den här uppsättningen kan anges som en *RSS* eller *Atom* slutpunkt.
* **Rangordning**: varje förfrågan till anpassad beslut tjänsten anger en eller flera uppsättningar av åtgärden. Systemet svarar genom att välja alla innehållsalternativ från dessa uppsättningar och returnerar dem i ordning som rangordnas.
* **Återanropsfunktionen**: den här funktionen som du anger, skapar innehåll i Gränssnittet. Innehållet är sorterade efter den rank sorteringen som returneras av anpassade beslut Service.
* **Ersättning**: ett mått på hur användaren svarat på det renderade innehållet. Anpassad beslut tjänst mäter användarens svar med hjälp av klick. Klick rapporteras till systemet med hjälp av anpassad kod som infogats i ditt program.

## <a name="next-steps"></a>Nästa steg

* [Registrera ditt program](custom-decision-service-get-started-register.md) med anpassade beslut Service
* Komma igång för att optimera [en webbsida](custom-decision-service-get-started-browser.md) eller [en smartphone-app](custom-decision-service-get-started-app.md).
* Läs den [API-referens](custom-decision-service-api-reference.md) lära dig mer om de angivna funktionerna.