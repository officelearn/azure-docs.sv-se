---
title: 'Snabb start: Hämta bild insikter med hjälp av REST API och python-Visuell sökning i Bing'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du laddar upp en avbildning med hjälp av API för visuell sökning i Bing och python och få insikter om avbildningen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.custom: devx-track-python
ms.openlocfilehash: 5c43df5880c1d54fa8e4f86acaa0b3456d778374
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499052"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Snabb start: Hämta bild insikter med hjälp av Visuell sökning i Bing REST API och python

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Använd den här snabb starten för att göra ditt första anrop till API för visuell sökning i Bing. Det här python-programmet laddar upp en avbildning till API: et och visar den information som returneras. Även om det här programmet är skrivet i python är API: et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

## <a name="prerequisites"></a>Förutsättningar

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Initiera programmet

1. Skapa en ny python-fil i din favorit-IDE eller-redigerare och Lägg till följande- `import` instruktion:

    ```python
    import requests, json
    ```

2. Skapa variabler för din prenumerationsnyckel, slutpunkt och sökvägen till den bild som du ska ladda upp. För värdet för `BASE_URI` kan du använda den globala slut punkten i följande kod eller använda den [anpassade slut domän](../../../cognitive-services/cognitive-services-custom-subdomains.md) slut punkten som visas i Azure Portal för din resurs.

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
3. När du laddar upp en lokal avbildning måste formulär data innehålla `Content-Disposition` sidhuvudet. Ange dess `name` parameter till "image" och ange `filename` parametern till fil namnet för din avbildning. Innehållet i formuläret är en bilds binära data. Den maximala bild storlek som du kan ladda upp är 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

4. Skapa ett Dictionary-objekt för att lagra din begärans huvud information. Bind din prenumerations nyckel till strängen `Ocp-Apim-Subscription-Key` .

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

5. Skapa en annan ord lista som innehåller din avbildning, som öppnas och överförs när du skickar begäran.

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Tolka JSON-svaret

Skapa en metod `print_json()` som kallas för att acceptera API-svaret och skriv ut JSON.

```python
def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
```

## <a name="send-the-request"></a>Skicka begäran

Använd `requests.post()` för att skicka en begäran till API:et för webbsökning i Bing. Inkludera strängen för din slutpunkt, huvud och filinformation. Skriv ut `response.json()` med `print_json()` .

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