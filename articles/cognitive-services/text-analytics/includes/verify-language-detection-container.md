---
title: Verifiera Språkidentifiering container instance
titleSuffix: Azure Cognitive Services
description: Lär dig hur du verifierar Språkidentifiering container-instansen.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: f4e0770bc052044a408f2c4088f2bd5ead225aa3
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968618"
---
### <a name="verify-the-language-detection-container-instance"></a>Verifiera Språkidentifiering container instance

1. Välj fliken **Översikt** och kopiera IP-adressen.
1. Öppna en ny flik i webbläsaren och ange IP-adressen. Ange `http://<IP-address>:5000 (http://55.55.55.55:5000`till exempel). Behållarens start sida visas, där du kan se att behållaren körs.

    ![Visa start sidan för behållaren för att kontrol lera att den körs](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Välj länken **Service API-Beskrivning** för att gå till behållarens Swagger-sida.

1. Välj någon av **post** -API: erna och välj **prova**. Parametrarna visas, vilket inkluderar följande inmatade exempel:

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

1. Ange **showStats** till `true`.

1. Välj **Kör** för att fastställa sentiment för texten.

    Modellen som är paketerad i behållaren genererar en poäng som sträcker sig från 0 till 1, där 0 är negativt sentiment och 1 är ett positivt sentiment.

    Det JSON-svar som returneras innehåller sentiment för den uppdaterade text ingången:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

Vi kan nu korrelera dokumenten i nytto lasten för svars nytto Last till de ursprungliga nytto Last dokumenten enligt `id`deras motsvarande. Varje dokument behandlas oberoende av varandra som innehåller olika statistik `characterCount` , `transactionCount`till exempel och. Dessutom har `detectedLanguages` varje resulterande dokument matrisen `name`med, `iso6391Name`, och `score` för varje språk som identifieras. När flera språk identifieras används den `score` för att fastställa det mest sannolika språket.