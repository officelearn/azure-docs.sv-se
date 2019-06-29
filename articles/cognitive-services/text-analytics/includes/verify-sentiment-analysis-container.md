---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
description: Lär dig mer om att verifiera sentiment analysis container-instans.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f68d9c7098f2b1ca782e2522c632c2e267b35336
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455143"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Kontrollera behållarinstansen Attitydanalys

1. Välj den **översikt** fliken och kopiera den IP-adressen.
1. Öppna en ny webbläsarflik och använda IP-adressen, till exempel `http://<IP-address>:5000 (http://55.55.55.55:5000`). Startsida för behållarens presenteras, du kan se behållaren körs.

    ![Visa behållaren startsidan för att kontrollera att den körs](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Välj den **API tjänstbeskrivning** länken för att gå till sidan behållare swagger.

1. Välj någon av de **POST** API: er och välj **prova**.  Parametrarna visas, inklusive till exempel anknytning:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Ersätt indata med följande JSON:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. Ange **showStats** till true.

1. Välj **kör** fastställa känslan i texten.

    Modellen paketeras i behållaren genererar en poäng mellan 0 och 1, där 0 är negativt och 1 är positivt.

    JSON-svar som returneras innehåller sentiment för uppdaterade textinmatningen:

    ```json
    {
      "documents": [
      {
        "id": "7",
        "score": 0.9826303720474243,
        "statistics": {
          "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

Vi kan nu jämföra dokumentet `id` svar-nyttolaster JSON till det ursprungliga begärd nyttolast dokumentet `id`, och se att det fanns ett resultat på över `.98` som anger en mycket positiv attityd.