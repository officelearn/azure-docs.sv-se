---
title: 'Snabbstart: Anropa anpassad sökning i Bing slutpunkten med hjälp av Python SDK | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Använd Bing anpassade Search SDK för Python för att få anpassade sökresultat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: aahi
ms.openlocfilehash: c4c5059bc57ea33357145f6b119456dc6c5bdb7b
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57571823"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>Snabbstart: Anropa anpassad sökning i Bing slutpunkten med hjälp av Python-SDK 

Använd den här snabbstarten om du vill begära sökresultat från din Bing Custom Search-instans med hjälp av Python-SDK. Även om Anpassad Bing-sökning har ett REST API som är kompatibelt med de flesta programmeringsspråk så tillhandahåller SDK för Anpassad Bing-sökning ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) med ytterligare felhantering och anteckningar.

## <a name="prerequisites"></a>Förutsättningar

- En instans av anpassad Bing-sökning. Gå till [Snabbstart: Skapa din första instans av anpassad Bing-sökning](quick-start.md) för mer information.
- Python[ 2.x eller 3.x](https://www.python.org/) 
- Den [Bing Custom Search SDK för Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>Installera Python SDK

Installera SDK för Bing Custom Search med följande kommando.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Skapa ett nytt program

Skapa en ny Python-fil i din favoritredigerare eller IDE och Lägg till följande importer.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Skapa en sökning-klient och skicka en begäran

1. Skapa en variabel för din prenumerationsnyckel.

    ```python
    subscription_key = 'your-subscription-key'
    ```

2. Skapa en instans av `CustomSearchClient`med hjälp av en `CognitiveServicesCredentials` objekt med prenumerationsnyckeln. 

    ```python
    client = CustomSearchClient(CognitiveServicesCredentials(subscription_key))
    ```

3. Skicka en sökbegäran med `client.custom_instance.search()`. Lägg till din sökterm som ska den `query` parametern och ange `custom_config` till din anpassade konfigurations-ID för att använda Sökinstans. Du kan hämta ditt ID från den [Bing Custom Search-portalen](https://www.customsearch.ai/), genom att klicka på den **produktion** fliken.

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Visa sökresultaten

Om alla webbsida sökresultat hittades, skaffa den första och Skriv ut namn, URL: en och totala webbsidor hittades.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp för anpassad sökning](./tutorials/custom-search-web-page.md)
