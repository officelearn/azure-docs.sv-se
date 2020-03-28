---
title: 'Snabbstart: Få bildinsikter med REST API och Python - Bing Visual Search'
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446590"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Snabbstart: Få bildinsikter med hjälp av Bing Visual Search REST API och Python

Använd den här snabbstarten för att ringa ditt första samtal till API:et för visuell sökning i Bing och visa resultaten. Detta Python-program överför en avbildning till API:et och visar den information den returnerar. Även om det här programmet är skrivet i Python är API:et en RESTful Web-tjänst som är kompatibel med de flesta programmeringsspråk.

## <a name="prerequisites"></a>Krav

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Initiera programmet

1. Skapa en ny Python-fil i din favorit-IDE eller redigerare och lägg till följande `import` uttalande:

    ```python
    import requests, json
    ```

2. Skapa variabler för din prenumerationsnyckel, slutpunkt och sökvägen till den bild som du ska ladda upp. `BASE_URI`kan vara den globala slutpunkten nedan eller den [anpassade underdomänslutpunkten](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure-portalen för din resurs:

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
    När du laddar upp en lokal bild `Content-Disposition` måste formulärdata innehålla sidhuvudet. Du måste `name` ange parametern till "image", `filename` och du kan ställa in parametern på valfri sträng. Innehållet i formuläret innehåller den binära data i bilden. Den maximala bildstorleken du kan ladda upp är 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

3. Skapa ett ordlisteobjekt som innehåller huvudinformationen för begäran. Bind din prenumerationsnyckel `Ocp-Apim-Subscription-Key`till strängen, som visas nedan:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Skapa en annan ordlista för att innehålla din bild, som öppnas och laddas upp när du skickar begäran:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Tolka JSON-svaret

1. Skapa en `print_json()` metod som anropas för att ta in API-svaret och skriv ut JSON:

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Skicka begäran

1. Använd `requests.post()` för att skicka en begäran till API:et för webbsökning i Bing. Inkludera strängen för din slutpunkt, huvud och filinformation. Skriv `response.json()` `print_json()`ut med :

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
> [Skapa en ensidig visuell sökwebbapp](../tutorial-bing-visual-search-single-page-app.md)
