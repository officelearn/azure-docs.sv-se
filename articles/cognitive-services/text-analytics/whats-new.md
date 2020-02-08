---
title: Vad är nytt i API för textanalys
titleSuffix: Text Analytics - Azure Cognitive Services
description: Den här artikeln innehåller information om nya versioner och funktioner för Azure Cognitive Services Textanalys.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: e3fbff7a5919d4d43656f3112cb24f2017191348
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086800"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Vad är nytt i API:et för textanalys?

API för textanalys uppdateras regelbundet. Den här artikeln innehåller information om nya versioner och funktioner för att hålla dig uppdaterad med den senaste utvecklingen.

## <a name="named-entity-recognition-v3-public-preview---february-2020"></a>Namngiven enhets igenkänning v3 offentlig för hands version – februari 2020

Ytterligare entitetstyper är nu tillgängliga i den namngivna enhets igenkännings tjänsten (NER) v3 offentlig för hands version eftersom vi expanderar identifieringen av allmänna och personliga informations enheter i text. Den här uppdateringen introducerar [modell version](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`, som innehåller:

* Igenkänning av följande allmänna entitetstyper (endast engelska):
    * PersonType
    * Produkt
    * Händelse
    * GPE (politisk entitet) som en undertyp under plats
    * Kvalifikation

* Igenkänning av följande entitets typer för personlig information (endast engelska):
    * Person
    * Organisation
    * Ålder som underordnad kvantitet
    * Datum som en undertyp under DateTime
    * E-post 
    * Telefonnummer (endast USA)
    * URL
    * IP-adress

> [!div class="nextstepaction"]
> [Läs mer om namngiven enhets igenkänning v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Oktober 2019

#### <a name="named-entity-recognition-ner"></a>Igenkänning av namngivna enheter (NER)

* En [ny slut punkt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) för att identifiera entitets typer för personlig information (endast engelska)

* Separata slut punkter för [entitets igenkänning](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) och [entitet länkning](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Modell versions](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2019-10-01`, som innehåller:
    * Utökad identifiering och kategorisering av entiteter som finns i text. 
    * Igenkänning av följande nya enhets typer:
        * Telefonnummer
        * IP-adress

Enhets länkning stöder engelska och spanska. Språk stöd för NER varierar beroende på enhets typen.

#### <a name="sentiment-analysis-v3-public-preview"></a>Attitydanalys v3 offentlig för hands version

* En [ny slut punkt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) för att analysera sentiment.
* [Modell versions](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) `2019-10-01`, som innehåller:

    * Betydande förbättringar av precisionen och detaljerna i API: ns text kategorisering och poängsättning.
    * Automatisk etikettering för olika sentiment i text.
    * Sentiment analys och utdata på en dokument-och menings nivå. 

Den stöder engelska (`en`), japanska (`ja`) Kinesisk (förenklad) (`zh-Hans`), traditionell kinesiska (`zh-Hant`), franska (`fr`), italienska (`it`), spanska (`es`), nederländska (`nl`), portugisiska (`pt`) och tyska (`de`) och är tillgänglig i följande regioner: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`och `West US 2`. 

> [!div class="nextstepaction"]
> [Läs mer om Attitydanalys v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Nästa steg

* [Vad är API för textanalys?](overview.md)  
* [Exempel på användar scenarier](text-analytics-user-scenarios.md)
* [Sentiment-analys](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Språk identifiering](how-tos/text-analytics-how-to-language-detection.md)
* [Enhets igenkänning](how-tos/text-analytics-how-to-entity-linking.md)
* [Extrahering av nyckel fraser](how-tos/text-analytics-how-to-keyword-extraction.md)
