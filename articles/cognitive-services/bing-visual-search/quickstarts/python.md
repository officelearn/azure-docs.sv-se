---
title: Python Snabbstart för Bing Visual sökning API | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Visar hur du överför en bild till Bing Visual Sök-API och få tillbaka insikter om bilden.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: a520466825eb429e45e0500b52bd7af502c0a38c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355074"
---
# <a name="your-first-bing-visual-search-query-in-python"></a>Första Bing Visual sökfrågan i Python

Bing Visual Sök API returnerar information om en avbildning som du anger. Du kan ange avbildningen med hjälp av URL-Adressen till bilden, en insikter token, eller genom att ladda upp en bild. Information om dessa alternativ finns [vad är Bing Visual Sök API?](../overview.md) Den här artikeln visar ladda upp en bild. Ladda upp en bild kan vara användbart i mobila scenarier där du kan ta en bild av en välkänd Landmärke och få tillbaka information om den. Insikter kan exempelvis omfatta kunskap om Landmärke. 

Om du överför en lokal image visas nedan formulärdata måste du inkludera i brödtexten i INLÄGGET. Formulärdata måste innehålla rubriken Content-Disposition. Dess `name` parametern måste anges till ”bild” och `filename` parameter kan anges till en sträng. Innehållet i formuläret är binär för bilden. Du kan ladda upp maximala bildstorleken är 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Den här artikeln innehåller ett enkelt konsolprogram som skickar en begäran i Bing Visual Sök-API och visar sökresultatet JSON. När det här programmet har skrivits i Python är en RESTful webbtjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON API: et. 

## <a name="prerequisites"></a>Förutsättningar

Du behöver [Python 3](https://www.python.org/) att köra den här koden.

Du kan använda för Snabbstart, en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) prenumeration nyckeln eller en betald prenumeration nyckel.

## <a name="running-the-walkthrough"></a>Kör den här genomgången

Följ dessa steg om du vill köra det här programmet:

1. Skapa ett nytt Python-projekt i din favorit IDE eller -redigeraren.
2. Skapa en fil med namnet visualsearch.py och Lägg till kod som visas i den här snabbstarten.
3. Ersätt den `SUBSCRIPTION_KEY` värdet med din prenumeration nyckel.
3. Ersätt den `imagePath` värdet med sökvägen till bilden som ska överföras.
4. Kör programmet.



Nedan visas hur du skickar meddelandet med flera delar formulärdata i Python.

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

[Få insikter om en avbildning med hjälp av en insights-token](../use-insights-token.md)  
[Bing Visual Sök sida app självstudiekursen](../tutorial-bing-visual-search-single-page-app.md)  
[Översikt över Bing Visual sökning](../overview.md)  
[Prova](https://aka.ms/bingvisualsearchtryforfree)  
[Hämta en kostnadsfri utvärderingsversion snabbtangent](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual Sök API-referens](https://aka.ms/bingvisualsearchreferencedoc)
