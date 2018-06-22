---
title: Ansikts API Python kursen | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Lär dig använda Ansikts-API med Python SDK för att identifiera mänsklig ytor i en bild i kognitiva Services.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 408f9759262d6ae2193df3193ee1c306d384afe6
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308796"
---
# <a name="getting-started-with-face-api-in-python-tutorial"></a>Komma igång med framsidan API i Python självstudiekursen

I kursen får du lära dig att anropa Ansikts-API: N via Python SDK, för att identifiera mänsklig ytor i en bild.

## <a name="prerequisites"></a> Nödvändiga komponenter

Om du vill använda den här kursen behöver du göra följande:

- Installera Python 2.7 + eller Python 3.5 +.
- Installera pip.
- Installera Python SDK för Ansikts-API: N på följande sätt:

```bash
pip install cognitive_face
```

- Hämta en [prenumeration nyckeln](https://azure.microsoft.com/try/cognitive-services/) för kognitiva Microsoft-tjänster. Du kan använda din primära eller den sekundära nyckeln i den här självstudiekursen. (Observera att du måste ha en giltig prenumeration för att använda alla Ansikts-API: er).

## <a name="sdk-example"></a> Identifiera ett ansikte i en bild

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

Nedan visas ett exempel resultat. Det är en `list` av identifierade ytor. Varje objekt i listan är en `dict` -instans där `faceId` är ett unikt ID för de identifierade står inför och `faceRectangle` beskriver positionen för de identifierade står inför. Ett ID står inför upphör att gälla i 24 timmar.

```python
[{u'faceId': u'68a0f8cf-9dba-4a25-afb3-f9cdf57cca51', u'faceRectangle': {u'width': 89, u'top': 66, u'height': 89, u'left': 446}}]
```

## <a name="draw-rectangles-around-the-faces"></a>Rita rektanglar runt ytor

Du kan med json-koordinater som du har fått från det föregående kommandot för att Rita rektanglar på bilden för att visuellt representerar varje yta. Du behöver `pip install Pillow` att använda den `PIL` avbildning modulen.  Lägg till följande längst upp i filen:

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

Sedan när `print(faces)`, bland annat följande i skriptet:

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

## <a name='further'></a> Vidare

Den här kursen ger ett GUI-exempel för att hjälpa dig att utforska vidare Ansikts-API. Om du vill köra den först installera [wxPython](https://wxpython.org/) kör kommandon nedan.

```bash
git clone https://github.com/Microsoft/Cognitive-Face-Python.git
cd Cognitive-Face-Python
python sample
```

## <a name="summary"></a> Sammanfattning

I den här kursen har du lärt dig den grundläggande processen för att använda Ansiktsigenkännings-API: N via anropar Python SDK. Mer information om API-information finns i anvisningar och [API-referens för](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related"></a> Närliggande information

- [Komma igång med CSharp riktade API](FaceAPIinCSharpTutorial.md)
- [Komma igång med Ansikts-API i Java för Android](FaceAPIinJavaForAndroidTutorial.md)
