---
title: 'Snabbstart: SDK för entitetssökning i Bing, Python'
titlesuffix: Azure Cognitive Services
description: Konfiguration av konsolprogrammet för SDK för entitetssökning i Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 50620eba35be136e38d5b1f8c3083f9ddf189d9e
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757856"
---
# <a name="quickstart-bing-entity-search-sdk-with-python"></a>Snabbstart: SDK för entitetssökning i Bing med Python

Använd den här snabbstarten om du vill börja söka efter entiteter med SDK för entitetssökning i Bing för Python. Även om entitetssökning i Bing har ett REST API som är kompatibelt med de flesta programmeringsspråk så tillhandahåller SDK:n ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Nödvändiga komponenter

* Python [2.x eller 3.x](https://www.python.org/)

* [SDK för entitetssökning i Bing för Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Vi rekommenderar att du använder en virtuell Python-miljö. Du kan installera och initiera den virtuella miljön med venv-modulen. Du måste installera virtualenv för Python 2.7 med:

```Console
python -m venv mytestenv
```

Installera SDK för entitetssökning i Bing med:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny Python-fil i valfri IDE eller redigeringsprogram och lägg till följande importinstruktioner. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchAPI
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Skapa en variabel för din prenumerationsnyckel och instantiera klienten genom att skapa ett nytt `CognitiveServicesCredentials`-objekt med den.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    client = EntitySearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Skicka en sökbegäran och ta emot ett svar

1. Skicka en sökbegäran till entitetssökning i Bing med `client.entities.search()` och en sökfråga. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Om entiteter returnerades konverterar du `entity_data.entities.value` till en lista och skriver ut det första resultatet.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](../tutorial-bing-entities-search-single-page-app.md)

* [Vad är API:et för entitetssökning i Bing?](../overview.md )