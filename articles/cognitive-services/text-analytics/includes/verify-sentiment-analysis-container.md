---
title: Verifiera Attitydanalys container instance
titleSuffix: Azure Cognitive Services
description: Lär dig hur du verifierar Attitydanalys container-instansen.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: cbc5ad63dd944eb53d3a8052e75744cb5c3709ea
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051192"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Verifiera Attitydanalys container instance

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

1. Ersätt indatamängden med följande JSON-innehåll:

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

1. Ange **showStats** till `true`.

1. Välj **Kör** för att fastställa sentiment för texten.

    Modellen som är paketerad i behållaren genererar en poäng som sträcker sig från 0 till 1, där 0 är negativt sentiment och 1 är ett positivt sentiment.

    Det JSON-svar som returneras innehåller sentiment för den uppdaterade text ingången:

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

Vi kan nu korrelera dokumentet `id` med JSON-data för svars nytto Last till det ursprungliga nytto Last dokumentet `id`för begäran. Poängen på mer än `0.98` anger ett mycket positivt sentiment.