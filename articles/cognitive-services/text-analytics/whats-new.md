---
title: Nyheter i API:et för textanalys
titleSuffix: Text Analytics - Azure Cognitive Services
description: Den här artikeln innehåller information om nya versioner och funktioner för Azure Cognitive Services Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 162e60ac8d33dc5d1951a58b0a9643b668608d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77188806"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Vad är nytt i API:et för textanalys?

Api:et för textanalys uppdateras löpande. För att hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln dig information om nya versioner och funktioner.

## <a name="february-2020"></a>Februari 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>SDK-stöd för Text Analytics API v3 Offentlig förhandsversion

Som en del av den [enhetliga Azure SDK-versionen](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)är Text Analytics API v3 SDK nu tillgängligt som en offentlig förhandsversion för följande programmeringsspråk:
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (nod.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)

> [!div class="nextstepaction"]
> [Läs mer om Text Analytics API v3 SDK](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Namngiven entitetsigenkänning v3 offentlig förhandsversion

Ytterligare entitetstyper är nu tillgängliga i den namngivna entitetsigenkänningstjänsten (NER) v3 public preview service när vi utökar identifieringen av allmän och personlig information som entiteter som finns i text. Den här uppdateringen introducerar [modellversion](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`, som innehåller:

* Erkännande av följande typer av allmänna enheter (endast på engelska):
    * PersonType (PersonType)
    * Produkt
    * Händelse
    * Geopolitisk entitet (GPE) som en undertyp under Plats
    * Skicklighet

* Erkännande av följande typer av personinformationsenhet (endast på engelska):
    * Person
    * Organisation
    * Ålder som undertyp under Kvantitet
    * Datum som undertyp under DateTime
    * E-post 
    * Telefonnummer (endast USA)
    * URL
    * IP-adress

> [!div class="nextstepaction"]
> [Läs mer om namngiven entitetsigenkänning v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Oktober 2019

#### <a name="named-entity-recognition-ner"></a>Namngiven entitetsigenkänning (NER)

* En [ny slutpunkt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) för att känna igen enhetstyper för personlig information (endast på engelska)

* Separata slutpunkter för [entitetsredovisning](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) och [entitetslänkning](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Modellversion](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2019-10-01`, som innehåller:
    * Utökad identifiering och kategorisering av entiteter som finns i text. 
    * Erkännande av följande nya enhetstyper:
        * Telefonnummer
        * IP-adress

Entitetslänkning stöder engelska och spanska. NER-språkstöd varierar beroende på entitetstypen.

#### <a name="sentiment-analysis-v3-public-preview"></a>Sentiment Analysis v3 offentlig förhandsvisning

* En [ny slutpunkt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) för att analysera sentiment.
* [Modellversion](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) `2019-10-01`, som innehåller:

    * Betydande förbättringar i noggrannheten och detaljerna i API:ets textkategorisering och bedömning.
    * Automatisk märkning för olika känslor i text.
    * Sentimentanalys och utdata på dokument- och meningsnivå. 

Den stöder`en`engelska (`ja`), japanska`zh-Hans`( ),`zh-Hant`förenklad`fr`kinesiska (`it`),`es`traditionell kinesiska`nl`(`pt`), franska`de`( ), italienska ( ), `Australia East` `Central Canada`spanska `Central US` `East Asia`( `East US` `East US 2`), `North Europe` `Southeast Asia`nederländska `South Central US` `UK South`( `West Europe`), `West US 2`portugisiska ( ) och tyska ( ), och finns i följande regioner: , , , , , , , , , , , , och . 

> [!div class="nextstepaction"]
> [Läs mer om Sentimentanalys v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Nästa steg

* [Vad är API för textanalys?](overview.md)  
* [Exempel på användarscenarier](text-analytics-user-scenarios.md)
* [Sentimentanalys](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Språkidentifiering](how-tos/text-analytics-how-to-language-detection.md)
* [Erkännande av entitet](how-tos/text-analytics-how-to-entity-linking.md)
* [Extraktion av nyckelfraser](how-tos/text-analytics-how-to-keyword-extraction.md)
