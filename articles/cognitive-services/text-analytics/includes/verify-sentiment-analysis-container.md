---
title: Verifiera behållarinstansen för sentimentanalys
titleSuffix: Azure Cognitive Services
description: Lär dig hur du verifierar behållarinstansen för sentimentanalys.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c9b5411c044bb45d284cac0d30705c2b3d40ccd0
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876471"
---
### <a name="verify-the-sentiment-analysis-container-instance"></a>Verifiera behållarinstansen för sentimentanalys

1. Välj fliken **Översikt** och kopiera IP-adressen.
1. Öppna en ny webbläsarflik och ange IP-adressen. Skriv `http://<IP-address>:5000 (http://55.55.55.55:5000`till exempel ). Behållarens startsida visas, vilket gör att du vet att behållaren körs.

    ![Visa behållarhemsidan för att kontrollera att den körs](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Välj länken **Service API Description** för att gå till behållarens Swagger-sida.

1. Välj någon av **POST API:erna** och välj Prova **det**. Parametrarna visas, vilket inkluderar det här exemplet indata:

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

1. Ersätt indata med följande JSON-innehåll:

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

1. Ställ in **showStats** till `true`.

1. Välj **Kör** för att bestämma textens inställning.

    Modellen som är paketerad i behållaren genererar en poäng som sträcker sig från 0 till 1, där 0 är negativt sentiment och 1 är positivt sentiment.

    JSON-svaret som returneras innehåller sentiment för den uppdaterade textinmatningen:

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

Vi kan nu korrelera dokumentet `id` av svarsnyttolastens JSON-data till det ursprungliga nyttolastdokumentet `id`. Poängen på mer `0.98` än indikerar en mycket positiv känsla.