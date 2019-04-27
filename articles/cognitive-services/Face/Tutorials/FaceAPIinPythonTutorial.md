---
title: 'Snabbstart: Identifiera och rama in ansikten i en bild med Python SDK'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten skapar du ett enkelt Python-skript som använder ansikts-API för att identifiera och rama in ansikten i en fjärrbild.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: sbowles
ms.openlocfilehash: e4b762d6f36f8682162160be6f42b8691e4b2ca3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815253"
---
# <a name="quickstart-create-a-python-script-to-detect-and-frame-faces-in-an-image"></a>Snabbstart: Skapa ett Python-skript för att identifiera och rama in ansikten i en bild

I den här snabbstarten skapar du ett enkelt Python-skript som använder ansikts-API för Azure, via Python SDK, för att identifiera ansikten i en fjärrbild. Appen visar en utvald bild och ritar en ram runt varje identifierat ansikte.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="prerequisites"></a>Nödvändiga komponenter

- En ansikts-API-prenumerationsnyckel. Du kan hämta nycklar för en kostnadsfri utvärderingsprenumeration från [Testa Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Följ instruktionerna i [Skapa ett konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på tjänsten Ansikts-API och få din nyckel.
- [Python 2.7+ eller 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/)-verktyget

## <a name="get-the-face-sdk"></a>Hämta SDK för ansiktsigenkänning

Installera Pythons SDK för ansiktsigenkänning genom att öppna kommandotolken och köra följande kommando:

```shell
pip install cognitive_face
```

## <a name="detect-faces-in-an-image"></a>Identifiera ansikten i en bild

Skapa ett nytt Python-skript som heter _FaceQuickstart.py_ och lägg till följande kod. Det här är huvudfunktionerna för ansiktsigenkänning. Du måste ersätta `<Subscription Key>` med värdet för din nyckel. Du kanske även måste ändra värdet för `BASE_URL` så att rätt regionsidentifierare används för din nyckel (i [dokumentet om Ansikts-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) finns en lista över alla regionsslutpunkter). Nycklar för kostnadsfri utvärderingsprenumeration genereras i regionen **westus** (USA, västra). Du kan också ange `img_url` till URL:en för en bild du vill använda.

Skriptet identifierar ansikten genom att anropa metoden **cognitive_face.face.detect**, som omsluter REST API för [identifiering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) och returnerar en lista över ansikten.

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

### <a name="try-the-app"></a>Prova appen

Kör appen med kommandot `python FaceQuickstart.py`. Du bör få ett textsvar i konsolfönstret, till exempel följande:

```shell
[{'faceId': '26d8face-9714-4f3e-bfa1-f19a7a7aa240', 'faceRectangle': {'top': 124, 'left': 459, 'width': 227, 'height': 227}}]
```

Det här är en lista över identifierade ansikten. Varje objekt i listan är en **dict**-instans där `faceId` är ett unikt ID för det identifierade ansiktet och `faceRectangle` beskriver positionen för det identifierade ansiktet. 

> [!NOTE]
> Ansikts-ID:n upphör att gälla efter 24 timmar, så du måste lagra ansiktsinformation uttryckligen om du vill behålla den långsiktigt.

## <a name="draw-face-rectangles"></a>Rita ansiktsrektanglar

Du kan använda koordinaterna från föregående kommando för att rita rektanglar på bilden för att visa varje ansikte visuellt. Du måste installera Pillow (`pip install pillow`) för att kunna använda Pillow Image-modulen. Högst upp i *FaceQuickstart.py* lägger du till följande kod:

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

Längs ned i skriptet lägger du sedan till följande kod. Det skapar en enkel funktion för parsning av rektangelkoordinaterna och använder Pillow till att rita rektanglar på den ursprungliga bilden. Sedan visar den bilden i ditt standardprogram för bildvisning.

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

## <a name="run-the-app"></a>Kör appen

Du kan uppmanas att välja ett standardprogram för bildvisning först. Då bör du se en bild som följande. Du bör även se rektangeldata utskrivna i konsolfönstret.

![En ung kvinna med den röd rektangel ritad runt ansiktet](../images/face-rectangle-result.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig den grundläggande processen för att använda Python SDK för Ansikts-API och skapat ett skript för att identifiera och rama in ansikten i en bild. Härnäst får du utforska användningen av Python SDK i ett mer avancerat exempel. Gå till Cognitive Face Python-exemplet på GitHub, klona det i din projektmapp och följ instruktionerna i _README.md_-filen.

> [!div class="nextstepaction"]
> [Cognitive Face Python-exempel](https://github.com/Microsoft/Cognitive-Face-Python)
