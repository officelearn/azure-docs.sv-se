---
title: 'Självstudie: Identifiera och rama in ansikten i en bild – Ansiktsigenkänning, Python'
titleSuffix: Azure Cognitive Services
description: Lär dig mer om att använda Ansikts-API med Python SDK för att identifiera ansikten i en bild.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 6cc3ac25d2196c0275b445503b79b9ac06a791d3
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127745"
---
# <a name="tutorial-detect-and-frame-faces-with-the-face-api-and-python"></a>Självstudie: Känna igen och rama in ansikten med API för ansiktsigenkänning och Python 

I den här självstudien får du lära dig att anropa API för ansiktsigenkänning via Python SDK för att identifiera ansikten i en bild.

## <a name="prerequisites"></a>Nödvändiga komponenter

I självstudiekursen måste du göra följande:

- Installera Python 2.7 + eller Python 3.5 +.
- Installera pip.
- Installera Python SDK för API för ansiktsigenkänning på följande sätt:

```bash
pip install cognitive_face
```

- Hämta en [prenumerationsnyckel](https://azure.microsoft.com/try/cognitive-services/) för Microsoft Cognitive Services. Du kan använda din primära eller sekundära nyckel i den här självstudien. (Observera att du måste ha en giltig prenumerationsnyckel om du vill använda API:er för ansiktsigenkänning).

## <a name="detect-a-face-in-an-image"></a>Identifiera ett ansikte i en bild

```python
import cognitive_face as CF

KEY = '<Subscription Key>'  # Replace with a valid subscription key (keeping the quotes in place).
CF.Key.set(KEY)

BASE_URL = 'https://westus.api.cognitive.microsoft.com/face/v1.0/'  # Replace with your regional Base URL
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)
```

Nedan visas ett exempelresultat. Det är en `list` av identifierade ansikten. Varje objekt i listan är en `dict`-instans där `faceId` är ett unikt ID för det identifierade ansiktet och `faceRectangle` beskriver positionen för det identifierade ansiktet. Ett ansikts-ID upphör att gälla efter 24 timmar.

```python
[{u'faceId': u'68a0f8cf-9dba-4a25-afb3-f9cdf57cca51', u'faceRectangle': {u'width': 89, u'top': 66, u'height': 89, u'left': 446}}]
```

## <a name="draw-rectangles-around-the-faces"></a>Rita rektanglar runt varje ansikte

Du kan använda json-koordinaterna från föregående kommando för att rita rektanglar på bilden för att visa varje ansikte visuellt. Du måste `pip install Pillow` för att använda avbildningsmodulen `PIL`.  Lägg till följande överst i filen:

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

Sedan när `print(faces)`, inkluderar du följande i ditt skript:

```python
#Convert width height to a point in a rectangle
def getRectangle(faceDictionary):
    rect = faceDictionary['faceRectangle']
    left = rect['left']
    top = rect['top']
    bottom = left + rect['height']
    right = top + rect['width']
    return ((left, top), (bottom, right))

#Download the image from the url
response = requests.get(img_url)
img = Image.open(BytesIO(response.content))

#For each face returned use the face rectangle and draw a red box.
draw = ImageDraw.Draw(img)
for face in faces:
    draw.rectangle(getRectangle(face), outline='red')

#Display the image in the users default image browser.
img.show()
```

## <a name="further-exploration"></a>Ytterligare utforskning

Den här självstudien innehåller ett exempel på ett grafiskt användargränssnitt som hjälper dig att utforska API för ansiktsigenkänning ytterligare. För att köra den installerar du först [wxPython](https://wxpython.org/pages/downloads/) och kör kommandona nedan.

```bash
git clone https://github.com/Microsoft/Cognitive-Face-Python.git
cd Cognitive-Face-Python
python sample
```

## <a name="summary"></a>Sammanfattning

I den här självstudien har du lärt dig det grundläggande sättet att använda API för ansiktsigenkänning för att anropa Python SDK. Mer information om API-information finns i anvisningar och [API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related-topics"></a>Relaterade ämnen

- [Komma igång med API för ansiktsigenkänning i CSharp](FaceAPIinCSharpTutorial.md)
- [ Komma igång med API för ansiktsigenkänning i Java för Android](FaceAPIinJavaForAndroidTutorial.md)
