---
title: Python-Snabbstart för API för Bing Visual Search | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Visar hur du överför en bild till Bing Visual Search-API och få tillbaka insikter om avbildningen.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 96bd94e37c75d10726245fbcea7044d4ae2ed07e
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070383"
---
# <a name="your-first-bing-visual-search-query-in-python"></a>Din första Bing Visual Search-fråga i Python

Bing Visual Search API returnerar information om en avbildning som du anger. Du kan ange avbildningen med hjälp av URL till bild, ett insights token, eller genom att överföra en avbildning. Information om alternativen finns i [vad är Bing Visual Search API?](../overview.md) Den här artikeln visar att ladda upp en avbildning. Ladda upp en avbildning kan vara användbart i mobila scenarier där du kan ta en bild av en välkänd landmärken och få tillbaka information om den. Insikterna kan exempelvis omfatta kunskap om landmärken. 

Om du laddar upp en lokal avbildning visas nedan formulärdata måste du inkludera i brödtexten i INLÄGGET. Formulärdata måste innehålla Content-Disposition-huvudet. Dess `name` parametern måste anges till ”bild” och `filename` parameter kan anges till valfri sträng. Innehållet i formuläret är den binära filen på avbildningen. Maximal avbildningens storlek kan du överföra är 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Den här artikeln innehåller ett enkelt konsolprogram som skickar en begäran om Bing Visual Search-API och visar JSON-sökresultat. Det här programmet är skrivet i Python är API: et en RESTful-webb-tjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON. 

## <a name="prerequisites"></a>Förutsättningar

Du behöver [Python 3](https://www.python.org/) att köra den här koden.

Den här snabbstarten kan du använda en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) prenumerationsnyckel eller en betald prenumeration-nyckel.

## <a name="running-the-walkthrough"></a>Kör den här genomgången

Följ dessa steg om du vill köra det här programmet:

1. Skapa ett nytt Python-projekt i din favorit-IDE eller redigerare.
2. Skapa en fil med namnet visualsearch.py och Lägg till koden som visas i den här snabbstarten.
3. Ersätt den `SUBSCRIPTION_KEY` värdet med din prenumerationsnyckel.
3. Ersätt den `imagePath` värdet med sökvägen för att ladda upp avbildningen.
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

[Få insyn om en avbildning med hjälp av en token för insikter](../use-insights-token.md)  
[Bing Visual Search bild uppladdning självstudien](../tutorial-visual-search-image-upload.md)
[Bing Visual Search-självstudiekursen som ensidesapp](../tutorial-bing-visual-search-single-page-app.md)  
[Bing Visual Search-översikt](../overview.md)  
[Prova](https://aka.ms/bingvisualsearchtryforfree)  
[Hämta en kostnadsfri utvärderingsversion åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual Search API-referens](https://aka.ms/bingvisualsearchreferencedoc)
