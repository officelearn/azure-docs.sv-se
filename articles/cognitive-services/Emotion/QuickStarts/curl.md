---
title: 'Snabbstart: Känsloigenkänning i ansikten i en bild – Känslo-API, cURL'
titlesuffix: Azure Cognitive Services
description: Hämta information och kodexempel som hjälper dig att snabbt komma igång med Känslo-API:et med cURL.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: bd157bfeff8ceaa18a75f15895b7304385c5e522
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232045"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Snabbstart: Skapa en app för att känna igen känslor i ansikten i en bild.

> [!IMPORTANT]
> Känslo-API:et blir inaktuellt den 15 februari 2019. Funktionen för känsloigenkänning är nu allmänt tillgänglig som en del av [Ansikts-API:et](https://docs.microsoft.com/azure/cognitive-services/face/).

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med att använda [metoden Recognize för Känslo-API:et](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) med cURL för att känna igen känslor som uttrycks av en eller flera personer i en bild.

## <a name="prerequisite"></a>Krav
* Hämta din kostnadsfria prenumerationsnyckel [här](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-curl-example-request"></a>Exempelbegäran för känsloigenkänning med cURL

> [!NOTE]
> Du måste använda samma plats i REST-anropet som du använde för att hämta prenumerationsnycklarna. Om du till exempel hämtade prenumerationsnycklarna från regionen westcentralus (USA, västra centrala), ersätter du ”westus” i URL:en nedan med ”westcenbtralus”.

```json
@ECHO OFF

curl -v -X POST "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"

--data-ascii "{body}"
```

## <a name="recognize-emotions-sample-response"></a>Exempelsvar för känsloigenkänning
Ett genomfört anrop returnerar en matris med ansiktsposter och deras associerade känsloresultat, rankade efter ansiktsrektangelns storlek i fallande ordning. Ett tomt svar indikerar att inga ansikten kändes igen. En känslopost innehåller följande fält:
* faceRectangle – ansiktsrektangelns placering i bilden.
* scores – känsloresultat för varje ansikte i bilden.

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
