---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: dae56e05f01e83f05e75fdf378c0c50679d18728
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819980"
---
Visa en förutsägelse resultatet i en webbläsare för att förstå vad en LUIS förutsägelse slutpunkten returnerar. För att fråga en offentlig app, behöver du en egen nyckel och app-ID. Offentliga IoT-app-ID, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, tillhandahålls som en del av URL: en i steg ett.

Formatet på URL-Adressen för en **hämta** slutpunktsförfrågan är:

```JSON
https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<YOUR-KEY>&q=<user-utterance>
```

1. Slutpunkten för offentliga IoT-appen är i det här formatet: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_KEY>&q=turn on the bedroom light`

    Kopiera URL och ersätta din nyckel för värdet för `<YOUR_KEY>`.

2. Klistra in URL:en i ett webbläsarfönster och tryck på Retur. Webbläsaren visar ett JSON-resultat som anger att LUIS identifierar den `HomeAutomation.TurnOn` avsikt som det främsta syftet och `HomeAutomation.Room` entitet med värdet `bedroom`.

    ```JSON
    {
      "query": "turn on the bedroom light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOn",
        "score": 0.809439957
      },
      "entities": [
        {
          "entity": "bedroom",
          "type": "HomeAutomation.Room",
          "startIndex": 12,
          "endIndex": 18,
          "score": 0.8065475
        }
      ]
    }
    ```

3. Ändra värdet för parametern `q=` i URL: en till `turn off the living room light`, och tryck på Retur. Resultatet anger nu att LUIS identifierat den `HomeAutomation.TurnOff` avsikt som det främsta syftet och `HomeAutomation.Room` entitet med värdet `living room`. 

    ```JSON
    {
      "query": "turn off the living room light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.984057844
      },
      "entities": [
        {
          "entity": "living room",
          "type": "HomeAutomation.Room",
          "startIndex": 13,
          "endIndex": 23,
          "score": 0.9619945
        }
      ]
    }
    ```
