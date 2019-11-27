---
title: 'Snabb start: Hämta bild insikter med hjälp av REST API och python-Visuell sökning i Bing'
titleSuffix: Azure Cognitive Services
description: Ta reda på hur du laddar du upp en bild till API för visuell sökning i Bing och får information om den.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 6fafc35d9d74927789fee3f3fea3014ff3be5717
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383184"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Snabb start: Hämta bild insikter med hjälp av Visuell sökning i Bing REST API och python

Använd den här snabb starten för att göra ditt första anrop till API för visuell sökning i Bing och visa resultatet. Det här python-programmet laddar upp en avbildning till API: et och visar den information som returneras. Även om det här programmet är skrivet i python är API: et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

När du laddar upp en lokal avbildning måste formulär data innehålla `Content-Disposition` rubriken. Du måste ange dess `name` parameter till "bild" och du kan ange parametern `filename` till valfri sträng. Innehållet i formuläret är en bilds binära data. Den maximala bild storlek som du kan ladda upp är 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Krav

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Initiera appen

1. Skapa en ny python-fil i din favorit-IDE eller-redigerare och Lägg till följande `import`-instruktion:

    ```python
    import requests, json
    ```

2. Skapa variabler för din prenumerations nyckel, slut punkt och sökvägen till den avbildning som du överför:

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. Skapa ett Dictionary-objekt för att lagra din begärans huvud information. Bind din prenumerations nyckel till sträng `Ocp-Apim-Subscription-Key`, som du ser nedan:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Skapa en annan ord lista som innehåller din avbildning, som öppnas och laddas upp när du skickar begäran:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Tolka JSON-svaret

1. Skapa en metod som heter `print_json()` som ska utföras i API-svaret och skriv ut JSON:

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
> [Skapa en Visuell sökning webb program med en enda sida](../tutorial-bing-visual-search-single-page-app.md)
