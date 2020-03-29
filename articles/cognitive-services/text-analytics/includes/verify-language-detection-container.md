---
title: Verifiera behållarförekomsten för språkidentifiering
titleSuffix: Azure Cognitive Services
description: Lär dig hur du verifierar behållarinstansen för språkidentifiering.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: f4e0770bc052044a408f2c4088f2bd5ead225aa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "70968618"
---
### <a name="verify-the-language-detection-container-instance"></a>Verifiera behållarförekomsten för språkidentifiering

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

1. Ställ in **showStats** till `true`.

1. Välj **Kör** för att bestämma textens inställning.

    Modellen som är paketerad i behållaren genererar en poäng som sträcker sig från 0 till 1, där 0 är negativt sentiment och 1 är positivt sentiment.

    JSON-svaret som returneras innehåller sentiment för den uppdaterade textinmatningen:

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

Vi kan nu korrelera dokumenten i svaret nyttolast JSON uppgifter till `id`den ursprungliga begäran nyttolast dokument med motsvarande . Varje dokument behandlas oberoende av olika `characterCount` statistiska `transactionCount`uppgifter, till exempel och . Dessutom har varje resulterande `detectedLanguages` dokument `name`matrisen med , `iso6391Name`och `score` för varje språk som identifieras. När flera språk identifieras `score` används det för att bestämma det mest sannolika språket.