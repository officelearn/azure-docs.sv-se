---
title: 'Känslo-API: översikt | Microsoft Docs'
description: 'Använd Microsoft senaste, molnbaserad känslo recognition algoritmen för att skapa mer personliga appar med Känslo-API: et i kognitiva Services.'
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 8383370cba3f78060e809f444f4ad3dab7380f4e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352563"
---
# <a name="what-is-emotion-api"></a>Vad är Känslo-API?

> [!IMPORTANT]
> Känslo-API togs bort på 30 oktober 2017. Funktionen är nu en del av [Ansikts-API](https://docs.microsoft.com/en-us/azure/cognitive-services/face/).

Välkommen till Microsoft Känslo-API som gör att du kan skapa flera anpassade appar med Microsofts molnbaserade känslo recognition algoritm.

### <a name="emotion-recognition"></a>Känslo igenkänning

Känslo-API-beta tar en bild som indata och returnerar förtroende mellan en uppsättning emotikoner för varje yta bild, samt avgränsningsram för från Ansikts-API. Emotikoner upptäckte är lycka, sadness, oväntat, resulterande, fruktan, förhandlingar, disgust eller neutral. Dessa emotikoner förmedlas cross-culturally och universellt via samma grundläggande ansikte uttryck, där identifieras av Känslo-API. 

**Tolkning resultat:** 

I tolkning resultat från Känslo-API: et känslo upptäckte ska tolkas som känslo med högsta poäng, som är normaliserade resultat ska summeras till en. Användare kan välja att ange ett högre förtroende tröskelvärde i sina program, beroende på deras behov. 

Mer information om känsloigenkänning finns i API-referens: 
  * Grundläggande: Om en användare har redan anropats Ansikts-API, de kan skicka ansikte rektangeln som indata och använder den grundläggande nivån. [API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * Standard: Om en användare inte lämnar en rektangel står inför de ska använda standardläge.  [API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Ett exempel på hur du tolkar direktuppspelad video med Känslo-API finns [hur du analyserar videor i realtid](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion).
