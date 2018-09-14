---
title: Känslo-API-översikt | Microsoft Docs
description: Använd Microsoft avancerade, molnbaserade igenkänning av algoritmen för att bygga mer personligt anpassade appar med Känslo-API i Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 210990b0f436fd75cb36e71ea28928c457a5232e
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45573562"
---
# <a name="what-is-emotion-api"></a>Vad är Känslo-API?

> [!IMPORTANT]
> Känslo-API upphörde den 30 oktober 2017. Funktionerna är nu en del av [Ansikts-API](https://docs.microsoft.com/azure/cognitive-services/face/).

Välkommen till Microsoft Känslo-API där du kan bygga personanpassade appar med Microsofts molnbaserade igenkänning av algoritmen.

### <a name="emotion-recognition"></a>Igenkänning av känslor

Känslo-API-beta tar en bild som indata och returnerar förtroendepoäng för en uppsättning känslor för varje ansikte i bilden, samt en avgränsningsruta för ansiktet från Ansikts-API. De identifierade känslorna är lycka, sorg, förvåning, ilska, rädsla, förakt, avsky eller neutral. Dessa känslor kommer över olika kulturer och kommuniceras universellt via samma grundläggande ansiktsuttryck där identifieras av Känslo-API. 

**Tolka resultaten:** 

I tolka resultaten från Känslo-API, känslor har identifierats ska tolkas som känslo har högst poäng när du poäng normaliseras ska summeras till en. Användare kan välja att ange ett högre förtroende tröskelvärde i sina program, beroende på deras behov. 

Mer information om känsloigenkänning finns i API-referens: 
  * Grundläggande: Om en användare redan har anropat Ansikts-API, de kan skicka ansiktsrektangeln som en inmatning och använder basic-nivån. [API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * Standard: Om en användare inte lämnar en ansiktsrektangeln de ska använda standardläge.  [API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Ett exempel på hur du tolkar direktuppspelad video med Känslo-API, se [så analysera videor i realtid](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion).
