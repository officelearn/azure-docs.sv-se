---
title: Känslo-API Python Snabbstart | Microsoft Docs
description: Hämta information och exempel på kod för att snabbt komma igång med Känslo-API med Python i kognitiva Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/05/2018
ms.author: anroth
ms.openlocfilehash: ff1f6b2ddc872d0ee63d9885b04b1f007bc86e33
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351630"
---
# <a name="emotion-api-python-quickstart"></a>Känslo-API Python Snabbstart

> [!IMPORTANT]
> Förhandsgranskning av video API upphörde 30 oktober 2017. Prova den nya [Video indexeraren API Preview](https://azure.microsoft.com/services/cognitive-services/video-indexer/) enkelt extrahera insikter från videor och förbättra innehållsidentifiering upplevelser, till exempel sökresultat med hjälp av tal, ytor, tecken och känslor. [Läs mer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Den här genomgången innehåller information och kodexempel som hjälper dig att snabbt komma igång med den [Känslo-API känner igen metoden](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) med Python att identifiera emotikoner uttryckt genom en eller flera personer i en bild. 

Du kan köra det här exemplet som en Jupyter-anteckningsbok på [MyBinder](https://mybinder.org) genom att klicka på Starta Binder badge: [ ![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=EmotionAPI.ipynb)


## <a name="prerequisite"></a>Krav
Hämta din kostnadsfria prenumerationen nyckel [här](https://azure.microsoft.com/try/cognitive-services/)

## <a name="running-the-walkthrough"></a>Kör den här genomgången
Om du vill fortsätta med den här genomgången ersätta `subscription_key` med API-nyckel som du fick tidigare.


```python
subscription_key = None
assert subscription_key
```

Kontrollera att tjänst-URL som motsvarar den region som du använde när du ställer in API-nyckeln. Om du använder en utvärderingsversion nyckel, behöver du inte göra några ändringar.


```python
emotion_recognition_url = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
```

Den här genomgången använder avbildningar som lagras på disk. Du kan också använda avbildningar som är tillgängliga via en offentligt tillgänglig URL. Mer information finns i [REST API-dokumentation](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa).

Eftersom bilddata skickas som en del av frågans brödtext, Lägg märke till att du måste ange den `Content-Type` sidhuvud till `application/octet-stream`. Om du skickar in en avbildning via en URL, Kom ihåg att ange rubriken för:
```python
header = {'Ocp-Apim-Subscription-Key': subscription_key }
```
Skapa en ordlista med URL:
```python
data = {'url': image_url}
```
och klara att för att den `requests` med hjälp av:
```python
requests.post(emotion_recognition_url, headers=headers, json=image_data)
```

Först hämta några exempel avbildningar från den [Känslo-API](https://azure.microsoft.com/services/cognitive-services/emotion/) plats.


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



Returnerade JSON-objekt innehåller ytor som har identifierats tillsammans med identifierade emotikoner omgivande rutorna. Varje känslo är associerad med ett värde mellan 0 och 1 där fler poäng är mer indikation på en känsla än en lägre poäng. 

Följande rader med kod identifierade emotikoner på ytor på avbildningen med hjälp av `matplotlib` bibliotek. Om du vill minska oreda visas endast de översta tre emotikoner.


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

Den `annotate_image` funktionen visas nästa kan användas för att överlagra emotikoner ovanpå en avbildningsfil anges dess sökväg i filsystemet. Den är baserad på koden för anrop till Känslo-API som visades tidigare.


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

Slutligen den `annotate_image` funktionen kan anropas på en avbildningsfil som visas i följande rad:


```python
annotate_image("images/emotion_2.jpg")
```
