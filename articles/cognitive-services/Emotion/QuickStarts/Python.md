---
title: 'Snabbstart: Känsloigenkänning i ansikten i en bild – Känslo-API, Python'
description: Hämta information och kodexempel som hjälper dig att snabbt komma igång med Känslo-API med Python.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: quickstart
ms.date: 02/05/2018
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: f0bcc88b60e0a9b93856aa32a10b9c0ad898ce95
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240706"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Snabbstart: Skapa en app för att känna igen känslor i ansikten på en bild.

> [!IMPORTANT]
> Känslo-API:et blir inaktuellt den 15 februari 2019. Funktionen för känsloigenkänning är nu allmänt tillgänglig som en del av [ansikts-API:et](https://docs.microsoft.com/azure/cognitive-services/face/). 

Den här genomgången innehåller information och kodexempel som hjälper dig att snabbt komma igång med hjälp av [Känslo-API:et metoden Recognize](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) med Python för att känna igen känslor som uttrycks av en eller flera personer i en bild.

Du kan köra det här exemplet som en Jupyter Notebook på [MyBinder](https://mybinder.org) genom att klicka på ikonen för att starta Binder: [![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=EmotionAPI.ipynb)


## <a name="prerequisite"></a>Krav
Hämta din kostnadsfria prenumerationsnyckel [här](https://azure.microsoft.com/try/cognitive-services/)

## <a name="running-the-walkthrough"></a>Köra genomgången
För at fortsätta med den här genomgången ersätter du `subscription_key` med den API-nyckel som du hämtade tidigare.


```python
subscription_key = None
assert subscription_key
```

Kontrollera att tjänst-URL:en motsvarar den region som du använde när du konfigurerade API-nyckeln. Om du använder en utvärderingsnyckel behöver du inte göra några ändringar.


```python
emotion_recognition_url = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
```

Den här genomgången använder bilder som lagras på disk. Du kan även använda bilder som är tillgängliga via en offentligt tillgänglig URL. Mer information finns i [dokumentationen om REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa).

Observera att eftersom bilddata skickas som en del av begärandetexten så behöver du ange `Content-Type`-huvudet till `application/octet-stream`. Om du skickar in en bild via en URL bör du komma ihåg att ange huvudet till:
```python
header = {'Ocp-Apim-Subscription-Key': subscription_key }
```
skapa en ordlista som innehåller URL:en:
```python
data = {'url': image_url}
```
och skicka den till `requests`-biblioteket med hjälp av:
```python
requests.post(emotion_recognition_url, headers=headers, json=image_data)
```

Först laddar du ned några exempelbilder från platsen för [Känslo-API](https://azure.microsoft.com/services/cognitive-services/emotion/).


```bash
%%bash
mkdir -p images
curl -Ls https://aka.ms/csnb-emotion-1 -o images/emotion_1.jpg
curl -Ls https://aka.ms/csnb-emotion-2 -o images/emotion_2.jpg
```


```python
image_path = "images/emotion_1.jpg"
image_data = open(image_path, "rb").read()
```


```python
import requests
headers  = {'Ocp-Apim-Subscription-Key': subscription_key, "Content-Type": "application/octet-stream" }
response = requests.post(emotion_recognition_url, headers=headers, data=image_data)
response.raise_for_status()
analysis = response.json()
analysis
```




    [{'faceRectangle': {'height': 162, 'left': 130, 'top': 141, 'width': 162},
      'scores': {'anger': 9.29041e-06,
       'contempt': 0.000118981574,
       'disgust': 3.15619363e-05,
       'fear': 0.000589638,
       'happiness': 0.06630674,
       'neutral': 0.00555004273,
       'sadness': 7.44669524e-06,
       'surprise': 0.9273863}}]



Det returnerade JSON-objektet innehåller avgränsningsfälten för de ansikten som identifierades samt de identifierade känslorna. Varje känsla har en poäng mellan 0 och 1, där en högre poäng är en starkare indikation för en känsla än en lägre poäng.

Följande kodrader identifierade känslorna i ansikten på bilden med hjälp av `matplotlib`-biblioteket. För att minska informationsmängden visas endast de tre främsta känslorna.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Rectangle
from io import BytesIO
from PIL import Image

plt.figure(figsize=(5,5))

image  = Image.open(BytesIO(image_data))
ax     = plt.imshow(image, alpha=0.6)

for face in analysis:
    fr = face["faceRectangle"]
    em = face["scores"]
    origin = (fr["left"], fr["top"])
    p = Rectangle(origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
    ax.axes.add_patch(p)
    ct = "\n".join(["{0:<10s}{1:>.4f}".format(k,v) for k, v in sorted(list(em.items()),key=lambda r: r[1], reverse=True)][:3])
    plt.text(origin[0], origin[1], ct, fontsize=20)    
_ = plt.axis("off")
```

Funktionen `annotate_image` som visas härnäst kan användas för att överlägga känslor ovanpå en bildfil givet dess sökväg i filsystemet. Den bygger på koden för att anropa till Känslo-API som visades tidigare.


```python
def annotate_image(image_path):    
    image_data = open(image_path, "rb").read()
    headers  = {'Ocp-Apim-Subscription-Key': subscription_key, "Content-Type": "application/octet-stream" }
    response = requests.post(emotion_recognition_url, headers=headers, data=image_data)
    response.raise_for_status()
    analysis = response.json()

    plt.figure()

    image  = Image.open(image_path)
    ax     = plt.imshow(image, alpha=0.6)

    for face in analysis:
        fr = face["faceRectangle"]
        em = face["scores"]
        origin = (fr["left"], fr["top"])
        p = Rectangle(origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
        ax.axes.add_patch(p)
        ct = "\n".join(["{0:<10s}{1:>.4f}".format(k,v) for k, v in sorted(list(em.items()),key=lambda r: r[1], reverse=True)][:3])
        plt.text(origin[0], origin[1], ct, fontsize=20, va="bottom")    
    _ = plt.axis("off")
```

Slutligen kan funktionen `annotate_image` anropas på en bildfil på det sätt som visas på följande rad:


```python
annotate_image("images/emotion_2.jpg")
```
