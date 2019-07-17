---
title: Kontrollera behållarinstansen Attitydanalys
titleSuffix: Azure Cognitive Services
description: Lär dig mer om att verifiera sentiment analysis container-instans.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f69d573e9e70a505018e94cca354f363097cc1b8
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229180"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Kontrollera behållarinstansen Attitydanalys

1. Välj den **översikt** fliken och kopiera den IP-adressen.
1. Öppna en ny webbläsarflik och ange IP-adressen. Ange till exempel `http://<IP-address>:5000 (http://55.55.55.55:5000`). Startsida för behållarens visas, att behållaren körs.

    ![Visa behållaren startsidan för att kontrollera att den körs](../media/how-tos/container-instance/swagger-docs-on-container.png).

1. Välj den **API tjänstbeskrivning** länk som leder till behållarsidan swagger.

1. Välj någon av de **POST** API: er och välj **prova**.  Parametrarna är visas, inklusive det här exemplet indata:

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

1. Ange **showStats** till true.

1. Välj **kör** fastställa känslan i texten.

    Den modell som är paketerat i behållaren genererar en poäng mellan 0 och 1, där 0 är negativt och 1 är positivt.

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

Vi kan nu jämföra dokumentet `id` av svarets nyttolast JSON-data till det ursprungliga begäran nyttolast dokumentet `id`. Vi kan se ett resultat på mer än `.98`, som anger en starkt positiv attityd.