---
title: Snabbstart för Bing Custom Search Python-klientbibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ec0ffdcf86e67a7126a3100c1e20b6e5c3474e35
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78252886"
---
Kom igång med klientbiblioteket Bing Custom Search för Python. Följ dessa steg för att installera paketet och prova exempelkoden för grundläggande uppgifter. Med API:et för anpassad sökning på Bing kan du skapa skräddarsydda, annonsfria sökupplevelser för ämnen som du bryr dig om. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py).

Använd klientbiblioteket Bing Custom Search för Python för att:
* Hitta sökresultat på webben, från din anpassade Bing-sökningsinstans.

[Exempel](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customsearch?view=azure-python) |  | [på](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/) referensdokumentation | [bibliotekskodpaket](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch)[(PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/)


## <a name="prerequisites"></a>Krav

- En instans av anpassad Bing-sökning. Se [Snabbstart: Skapa din första förekomst av anpassad bing-sökning](../../quick-start.md) för mer information.
- Python[ 2.x eller 3.x](https://www.python.org/) 
- [Bing Custom Search SDK för Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>Installera Python-klientbiblioteket

Installera klientbiblioteket för anpassad sökning i Bing med följande kommando.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Skapa ett nytt program

Skapa en ny Python-fil i din favoritredigerare eller IDE och lägg till följande import.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Skapa en sökklient och skicka en begäran

1. Skapa en variabel för din prenumerationsnyckel och slutpunkt.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. Skapa en `CustomSearchClient`förekomst `CognitiveServicesCredentials` av med ett objekt med prenumerationsnyckeln. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. Skicka en sökbegäran med `client.custom_instance.search()`. Lägg till söktermen `query` i parametern och ange `custom_config` ditt anpassade konfigurations-ID för att använda sökinstansen. Du kan hämta ditt ID från [portalen För anpassad sökning](https://www.customsearch.ai/)i Bing genom att klicka på fliken **Produktion.**

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Visa sökresultaten

Om några sökresultat för webbsidor hittades får du den första och skriver ut dess namn, webbadress och totala webbsidor som hittades.

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
> [Skapa en webbapp för anpassad sökning](../../tutorials/custom-search-web-page.md)
