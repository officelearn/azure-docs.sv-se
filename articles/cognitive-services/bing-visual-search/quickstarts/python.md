---
title: 'Snabbstart: Hämta information om bilder med hjälp av REST API:et för visuell sökning i Bing och Python'
titleSuffix: Azure Cognitive Services
description: Ta reda på hur du laddar upp en bild till API:et för visuell sökning i Bing och får information om den.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 7ec37b4c3bdeb924b3e35dbcb5d07a478611f631
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60510855"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Snabbstart: Hämta information om bilder med hjälp av REST API:et för visuell sökning i Bing och Python

Använd den här snabbstarten för att skapa ditt första anrop till Bing Visual Search API och granska resultaten. Det här Python-programmet laddar upp en bild-API: et och visar den information som returneras. Även om det här programmet är skrivet i Python är API: et en RESTful-webb-tjänst som är kompatibla med de flesta programmeringsspråk.

När du laddar upp en lokal avbildning formulärdata måste innehålla den `Content-Disposition` rubrik. Måste du ställa in dess `name` parametern till ”bild” och du kan ange den `filename` parametern till valfri sträng. Innehållet i formuläret inkludera binära data för avbildningen. Den maximala bildstorlek som du kan ladda upp är 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Initiera programmet

1. Skapa en ny Python-fil i din favorit-IDE eller redigerare och Lägg till följande `import` instruktionen:

    ```python
    import requests, json
    ```

2. Skapa variabler för din prenumerationsnyckel, slutpunkt och sökvägen till den avbildning som du laddar upp:

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. Skapa ett katalogobjekt för att lagra dina begärandehuvuden. Binda din prenumerationsnyckel till strängen `Ocp-Apim-Subscription-Key`, enligt nedan:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Skapa en annan ordlista så att den innehåller din avbildning, som öppnas och laddat upp när du skickar begäran:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Tolka JSON-svaret

1. Skapa en metod som kallas `print_json()` att ta i API-svaret och skriva ut JSON:

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Skicka begäran

1. Använd `requests.post()` för att skicka en begäran till API:et för webbsökning i Bing. Inkludera strängen för din slutpunkt, huvud och filinformation. Skriv ut `response.json()` med `print_json()`:

    ```python
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())
    
    except Exception as ex:
        raise ex
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enda sida i Visual Search webbapp](../tutorial-bing-visual-search-single-page-app.md)
