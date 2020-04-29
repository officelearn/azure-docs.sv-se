---
title: 'Snabb start: Hämta bild insikter med hjälp av REST API och python-Visuell sökning i Bing'
titleSuffix: Azure Cognitive Services
description: Ta reda på hur du laddar upp en bild till API:et för visuell sökning i Bing och får information om den.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: b56f6743b642904349797ac5b6167194f7916b45
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75446590"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Snabb start: Hämta bild insikter med hjälp av Visuell sökning i Bing REST API och python

Använd den här snabb starten för att göra ditt första anrop till API för visuell sökning i Bing och visa resultatet. Det här python-programmet laddar upp en avbildning till API: et och visar den information som returneras. Även om det här programmet är skrivet i python är API: et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

## <a name="prerequisites"></a>Krav

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Initiera programmet

1. Skapa en ny python-fil i din favorit-IDE eller-redigerare och Lägg `import` till följande-instruktion:

    ```python
    import requests, json
    ```

2. Skapa variabler för din prenumerationsnyckel, slutpunkt och sökvägen till den bild som du ska ladda upp. `BASE_URI`kan vara den globala slut punkten nedan eller den [anpassade slut domänen](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs:

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
    När du laddar upp en lokal avbildning måste formulär data innehålla `Content-Disposition` sidhuvudet. Du måste ange `name` parametern till "image" och du kan ange `filename` parametern till valfri sträng. Innehållet i formuläret är en bilds binära data. Den maximala bild storlek som du kan ladda upp är 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

3. Skapa ett Dictionary-objekt för att lagra din begärans huvud information. Bind din prenumerations nyckel till strängen `Ocp-Apim-Subscription-Key`enligt nedan:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Skapa en annan ord lista som innehåller din avbildning, som öppnas och laddas upp när du skickar begäran:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Tolka JSON-svaret

1. Skapa en metod som `print_json()` anropas för att utföra API-svar och skriv ut JSON:

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Skicka begäran

1. Använd `requests.post()` för att skicka en begäran till API:et för webbsökning i Bing. Inkludera strängen för din slutpunkt, huvud och filinformation. Skriv `response.json()` ut `print_json()`med:

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
> [Skapa en Visuell sökning webb program med en enda sida](../tutorial-bing-visual-search-single-page-app.md)
