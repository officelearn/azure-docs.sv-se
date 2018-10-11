---
title: 'Snabbstart: Skapa en visuell sökfråga, Python – Visuell sökning i Bing'
titleSuffix: Azure Cognitive Services
description: Visar hur du laddar upp en bild till API för visuell sökning i Bing och får tillbaka information om bilden.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 16d3d0ddf77e37e32cc50961a3870b820ac2748e
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884249"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>Snabbstart: Din första fråga i Visuell sökning i Bing i Python

API för visuell sökning i Bing returnerar information om en bild som du anger. Du kan ange bilden med hjälp av dess URL, en insiktstoken eller genom att ladda upp en bild. Information om alternativen finns i [Vad är API för visuell sökning i Bing?](../overview.md) Den här artikeln visar hur du laddar upp en bild. Att ladda upp en bild kan vara användbart i mobila scenarier, där du kan ta en bild av ett välkänt landmärke och få tillbaka information om det. Informationen kan exempelvis vara fakta om landmärket. 

Om du laddar upp en lokal bild måste du inkludera de formulärdata som visas nedan i brödtexten i POST. Formulärdatan måste innehålla huvudet för innehållsdispositionen. Parametern `name` måste anges till ”image” och parametern `filename` kan anges till valfri sträng. Innehållet i formuläret är binärt för bilden. Den maximala bildstorlek som du kan ladda upp är 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Artikeln innehåller ett enkelt konsolprogram som skickar en begäran till API för visuell sökning i Bing och visar JSON-sökresultatet. Det här programmet är skrivet i Python, men API:et är en RESTful-webbtjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON. 

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver [Python 3](https://www.python.org/) för att köra koden.

I snabbstarten kan du använda en prenumerationsnyckel för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) eller en betald prenumerationsnyckel.

## <a name="running-the-walkthrough"></a>Köra genomgången

Följ dessa steg om du vill köra programmet:

1. Skapa ett nytt Python-projekt i valfri IDE eller redigeringsprogram.
2. Skapa en fil med namnet visualsearch.py och lägg till den kod som visas i den här snabbstarten.
3. Ersätt värdet `SUBSCRIPTION_KEY` med din prenumerationsnyckel.
3. Ersätt värdet `imagePath` med sökvägen till den bild som ska laddas upp.
4. Kör programmet.



Nedan visas hur du skickar meddelandet med hjälp av multipart-formulärdata i Python.

```python
"""Bing Visual Search upload image example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json


BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

imagePath = '<pathtoyourimagetouploadgoeshere>'

file = {'image' : ('myfile', open(imagePath, 'rb'))}

def main():
    
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))



# Main execution
if __name__ == '__main__':
    main()
```


## <a name="next-steps"></a>Nästa steg

[Få insikter om en bild med hjälp av en insiktstoken](../use-insights-token.md)  
[Självstudie om bilduppladdning i Visuell sökning i Bing](../tutorial-visual-search-image-upload.md)
[Självstudie om ensidesapplikationer i Visuell sökning i Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Översikt för Visuell sökning i Bing](../overview.md)  
[Prova](https://aka.ms/bingvisualsearchtryforfree)  
[Skaffa en åtkomstnyckel för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Referens till API för visuell sökning i Bing](https://aka.ms/bingvisualsearchreferencedoc)
