---
title: Känslo-API cURL Snabbstart | Microsoft Docs
description: Hämta information och exempel på kod för att snabbt komma igång med Känslo-API med cURL i kognitiva Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: d36a191d3589702d676e694715f34ea1c924e2d3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352386"
---
# <a name="emotion-api-curl-quick-start"></a>Känslo-API cURL Snabbstart

> [!IMPORTANT]
> Förhandsgranskning av video API avslutas på 30 oktober 2017. Prova den nya [Video indexeraren API Preview](https://azure.microsoft.com/services/cognitive-services/video-indexer/) enkelt extrahera insikter från videor och förbättra innehållsidentifiering upplevelser, till exempel sökresultat med hjälp av tal, ytor, tecken och känslor. [Läs mer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med den [Känslo-API känner igen metoden](https://dev.projectoxford.ai/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) med cURL för att identifiera emotikoner uttryckt genom en eller flera personer i en bild. 

## <a name="prerequisite"></a>Krav
* Hämta din kostnadsfria prenumerationen nyckel [här](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-curl-example-request"></a>Identifiera emotikoner cURL exempel begäran

> [!NOTE]
> Du måste använda samma plats i REST-anrop som du använde för att hämta dina nycklar för prenumerationen. Till exempel om du har köpt din prenumeration nycklar från westcentralus Ersätt ”westus” i URL-Adressen nedan med ”westcentralus”.

```json
@ECHO OFF

curl -v -X POST "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"

--data-ascii "{body}" 
```

## <a name="recognize-emotions-sample-response"></a>Identifiera emotikoner Exempelsvar
Lyckade anrop returnerar en matris av framsidan poster och deras associerade känslo poäng rangordnas efter ansikte rektangel storlek i fallande ordning. Ett tomt svar anger att ingen ytor upptäcktes. Känslo-posten innehåller följande fält:
* faceRectangle - placering av ansikte i avbildningen.
* resultat - Place poängen för varje yta i bilden. 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]

