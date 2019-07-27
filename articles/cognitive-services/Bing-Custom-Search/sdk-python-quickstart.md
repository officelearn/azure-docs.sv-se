---
title: 'Snabbstart: Anropa din Anpassad sökning i Bing-slutpunkt med python SDK | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Använd den här snabb starten för att börja begära Sök Resultat från Anpassad sökning i Bing-instansen med python SDK.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: 7a9d0d6619878299768e1f17b4da3280ea1ec1fc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564608"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>Snabbstart: Anropa din Anpassad sökning i Bing slut punkt med python SDK 

Använd den här snabb starten för att börja begära Sök Resultat från Anpassad sökning i Bing-instansen med hjälp av python SDK. Även om Anpassad Bing-sökning har ett REST API som är kompatibelt med de flesta programmeringsspråk så tillhandahåller SDK för Anpassad Bing-sökning ett enkelt sätt att integrera tjänsten i dina program. Du hittar käll koden för det här exemplet på [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) med ytterligare fel hantering och anteckningar.

## <a name="prerequisites"></a>Förutsättningar

- En instans av anpassad Bing-sökning. Gå till [Snabbstart: Skapa din första instans av anpassad Bing-sökning](quick-start.md) för mer information.
- Python[ 2.x eller 3.x](https://www.python.org/) 
- [Anpassad sökning I Bing SDK för python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>Installera python SDK

Installera Anpassad sökning i Bing SDK med följande kommando.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Skapa ett nytt program

Skapa en ny python-fil i din favorit redigerare eller IDE och Lägg till följande importer.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Skapa en Sök klient och skicka en begäran

1. Skapa en variabel för din prenumerationsnyckel.

    ```python
    subscription_key = 'your-subscription-key'
    ```

2. Skapa en instans av `CustomSearchClient`med hjälp av `CognitiveServicesCredentials` ett objekt med prenumerations nyckeln. 

    ```python
    client = CustomSearchClient(CognitiveServicesCredentials(subscription_key))
    ```

3. Skicka en Sök förfrågan med `client.custom_instance.search()`. Lägg till din sökterm till `query` parametern och Ställ in `custom_config` på ditt anpassade konfigurations-ID för att använda din Sök instans. Du kan hämta ditt ID från [Anpassad sökning i Bing Portal](https://www.customsearch.ai/)genom att klicka på fliken **produktion** .

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Visa Sök resultaten

Om det finns Sök Resultat från en webb sida, hämta det första och skriv ut dess namn, URL och totalt antal webb sidor som hittas.

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
