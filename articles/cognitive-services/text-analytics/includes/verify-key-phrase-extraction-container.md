---
title: Verifiera behållarinstansförekomst för extrahering av nyckelfraser
titleSuffix: Azure Cognitive Services
description: Lär dig hur du verifierar behållarinstansen För extrahering av nyckelfraser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c177517ec18d7526b1cc09da74e35cb5434766d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876472"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>Verifiera behållarinstansförekomst för extrahering av nyckelfraser

1. Välj fliken **Översikt** och kopiera IP-adressen.
1. Öppna en ny webbläsarflik och ange IP-adressen. Skriv `http://<IP-address>:5000 (http://55.55.55.55:5000`till exempel ). Behållarens startsida visas, vilket gör att du vet att behållaren körs.

    ![Visa behållarhemsidan för att kontrollera att den körs](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Välj länken **Service API Description** för att gå till behållarens Swagger-sida.

1. Välj någon av **POST API:erna** och välj Prova **det**. Parametrarna visas, vilket inkluderar det här exemplet indata:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
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

    Modellen som är paketerad i behållaren genererar en poäng som sträcker sig från 0 till 1, där 0 är negativ och 1 är positiv.

    JSON-svaret som returneras innehåller sentiment för den uppdaterade textinmatningen:

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
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

Vi kan nu korrelera dokumentet `id` av svarsnyttolastens JSON-data till det ursprungliga nyttolastdokumentet `id`. Det resulterande `keyPhrases` dokumentet har en matris som innehåller en lista över nyckelfraser som har extraherats från motsvarande indatadokument. Dessutom finns det olika statistik, `characterCount` `transactionCount` till exempel och för varje resulterande dokument.