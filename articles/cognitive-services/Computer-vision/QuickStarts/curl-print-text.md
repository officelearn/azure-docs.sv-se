---
title: 'Snabbstart: OCR med API:et för visuellt innehåll och cURL | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: I den här snabbstarten ska du extrahera tryckt text från en bild med hjälp av Visuellt innehåll och cURL i Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 3a9ebafa5e367c4fcd7dce63c54c9fdc14eb3812
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772000"
---
# <a name="quickstart-extract-printed-text-ocr---rest-curl"></a>Snabbstart: Extrahera tryckt text (OCR) – REST, cURL

I den här snabbstarten ska du extrahera tryckt text, även kallat optisk teckenläsning (OCR), från en bild med hjälp av Visuellt innehåll.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att använda Visuellt innehåll behöver du en prenumerationsnyckel. Mer information finns i avsnittet [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Hämta prenumerationsnycklar).

## <a name="ocr-request"></a>OCR-begäran

Med metoden [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) kan du identifiera tryckt text i en bild och extrahera de tecken som identifieras i en teckenström som kan användas på en dator.

För att köra exemplet följer du dessa steg:

1. Kopiera följande kod till ett redigeringsprogram.
1. Ersätt `<Subscription Key>` med en giltig prenumerationsnyckel.
1. Ändra URL:en för begäran (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) till den plats där du hämtade dina prenumerationsnycklar, om det behövs.
1. Du kan också ändra bilden (`{\"url\":\"...`) som ska analyseras om du vill.
1. Öppna ett kommandofönster på en dator där cURL är installerat.
1. Klistra in koden i fönstret och kör kommandot.

>[!NOTE]
>Du måste använda samma plats i REST-anropet som du använde för att hämta prenumerationsnycklarna. Om du till exempel hämtade prenumerationsnycklarna från regionen westus (USA, västra), ersätter du ”westcentralus” i URL:en nedan med ”westus”.

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/ocr?language=unk&detectOrientation=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

## <a name="ocr-response"></a>OCR-svar

Om åtgärden lyckades innehåller OCR-resultatet text, en avgränsningsruta för områden, linjer och ord, till exempel:

```json
{
  "language": "en",
  "orientation": "Up",
  "textAngle": 0,
  "regions": [
    {
      "boundingBox": "21,16,304,451",
      "lines": [
        {
          "boundingBox": "28,16,288,41",
          "words": [
            {
              "boundingBox": "28,16,288,41",
              "text": "NOTHING"
            }
          ]
        },
        {
          "boundingBox": "27,66,283,52",
          "words": [
            {
              "boundingBox": "27,66,283,52",
              "text": "EXISTS"
            }
          ]
        },
        {
          "boundingBox": "27,128,292,49",
          "words": [
            {
              "boundingBox": "27,128,292,49",
              "text": "EXCEPT"
            }
          ]
        },
        {
          "boundingBox": "24,188,292,54",
          "words": [
            {
              "boundingBox": "24,188,292,54",
              "text": "ATOMS"
            }
          ]
        },
        {
          "boundingBox": "22,253,297,32",
          "words": [
            {
              "boundingBox": "22,253,105,32",
              "text": "AND"
            },
            {
              "boundingBox": "144,253,175,32",
              "text": "EMPTY"
            }
          ]
        },
        {
          "boundingBox": "21,298,304,60",
          "words": [
            {
              "boundingBox": "21,298,304,60",
              "text": "SPACE."
            }
          ]
        },
        {
          "boundingBox": "26,387,294,37",
          "words": [
            {
              "boundingBox": "26,387,210,37",
              "text": "Everything"
            },
            {
              "boundingBox": "249,389,71,27",
              "text": "else"
            }
          ]
        },
        {
          "boundingBox": "127,431,198,36",
          "words": [
            {
              "boundingBox": "127,431,31,29",
              "text": "is"
            },
            {
              "boundingBox": "172,431,153,36",
              "text": "opinion."
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Utforska API:erna för visuellt innehåll som används för att analysera en bild, identifiera kändisar och landmärken, skapa en miniatyrbild och extrahera tryckt och handskriven text. Du kan snabbt experimentera med API:erna för visuellt innehåll genom att prova [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Utforska API:er för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
