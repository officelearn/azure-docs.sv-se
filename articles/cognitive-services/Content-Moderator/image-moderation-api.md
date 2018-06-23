---
title: Azure innehåll kontrollant - avbildningen Avbrottsmoderering | Microsoft Docs
description: Använd avbildningen avbrottsmoderering för att måttlig olämpliga bilder
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/20/2018
ms.author: sajagtap
ms.openlocfilehash: c7cbc343c6e9113642d0ac79f4a4d60a404e8171
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355077"
---
# <a name="image-moderation"></a>Bildmoderering

Använda innehåll kontrollant datorn stödd avbildningen avbrottsmoderering och [mänsklig granska verktyget](Review-Tool-User-Guide/human-in-the-loop.md) till måttlig avbildningar för vuxna och dyr innehåll. Skanna bilder för textinnehåll extrahera texten och identifiera ytor. Du kan matcha bilder mot anpassade listor och vidta ytterligare åtgärder.

## <a name="evaluating-for-adult-and-racy-content"></a>Utvärderar för vuxna och dyr innehåll

Den **utvärdera** åtgärd returnerar en poäng förtroende mellan 0 och 1. Den returnerar även booleska data lika med true eller false. Dessa värden förutsäga om bilden innehåller potentiella innehåll för vuxna eller dyr. När du anropar API: et med bilden (fil eller URL), innehåller returnerade svaret följande information:

    "ImageModeration": {
      .............
      "adultClassificationScore": 0.019196987152099609,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.032390203326940536,
      "isImageRacyClassified": false,
      ............
      ],

> [!NOTE]

> - `isImageAdultClassified` representerar potentiella förekomst av avbildningar som kan uppfattas som webbplatser explicit eller vuxna i vissa situationer.
> - `isImageRacyClassified` representerar potentiella förekomst av avbildningar som kan uppfattas som webbplatser något eller mogen i vissa situationer.
> - Poängen är mellan 0 och 1. Ju högre poäng, desto högre modellen är att förutsäga att kategorin kan användas. Den här förhandsgranskningen är beroende av en statistisk modell i stället för manuellt kodade resultat. Vi rekommenderar att du testar med ditt eget innehåll för att avgöra hur varje kategori passar dina behov.
> - Booleska värden är true eller false beroende på den interna poängen tröskelvärden. Kunder bör bedöma om du vill använda det här värdet eller besluta om anpassade tröskelvärden baserat på deras innehåll principer.
>

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Identifiera text med OCR (OCR)

Den **OCR (OCR)** åtgärden beräknar förekomsten av textinnehåll i en bild och extraherar för text avbrottsmoderering, bland annat. Du kan ange språk. Om du inte anger ett språk standard identifieringen till engelska.

Svaret innehåller följande information:
- Den ursprungliga texten.
- Identifierade textelement med deras förtroende resultat.

Exempel extrahera:

    "TextDetection": {
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      },
      .........
      "language": "eng",
      "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
      "candidates": []
    },


## <a name="detecting-faces"></a>Identifiering av ytor

Identifiera ytor hjälper till att identifiera personligt identifierbar information (PII), till exempel ytor i bilder. Du kan identifiera potentiella ytor och antalet potentiella ytor i varje avbildning.

Svaret innehåller den här informationen:

- Antal ytor
- Lista över platser av ytor upptäcktes

Exempel extrahera:


    "FaceDetection": {
       ......
      "result": true,
      "count": 2,
      "advancedInfo": [
      .....
      ],
      "faces": [
        {
          "bottom": 598,
          "left": 44,
          "right": 268,
          "top": 374
        },
        {
          "bottom": 620,
          "left": 308,
          "right": 532,
          "top": 396
        }
      ]
    }

## <a name="creating-and-managing-custom-lists"></a>Skapa och hantera anpassade listor

I många online communities, när användare ladda upp bilder eller annan typ av innehåll, kan stötande objekt hämta delad flera gånger under följande dagar, veckor och månader. Kostnaderna för att genomsöka och filtrera bort samma bild eller marginellt ändrade versioner av bilden från flera platser upprepade gånger kan vara dyrt och felbenägna.

I stället för att kontrollera flera gånger för samma avbildning, du lägger till stötande avbildningar din egen lista över blockerade innehållet. På så sätt kan systemet innehåll avbrottsmoderering jämför inkommande bilder mot din anpassade listor och stoppar vidare bearbetning.

> [!NOTE]
> Det finns en övre gräns för **5 bild visar** med varje lista till **inte överstiga 10 000 bilder**.
>

Innehåll kontrollanten ger en fullständig [avbildningen listan Management API](try-image-list-api.md) med åtgärder för att hantera en lista över anpassade avbildningar. Börja med den [bild visar en lista över API konsolen](try-image-list-api.md) och använda REST API-kodexempel. Se även den [avbildningen listan .NET quickstart](image-lists-quickstart-dotnet.md) om du är bekant med Visual Studio och C#.

## <a name="matching-against-your-custom-lists"></a>Matchning mot din anpassade listor

Matcha åtgärden tillåter fuzzy matchning av inkommande bilder mot någon av dina egna listor, skapas och hanteras med hjälp av åtgärderna lista.

Om en matchning hittas returnerar åtgärden identifierare och avbrottsmoderering taggar bildens matchade. Svaret innehåller den här informationen:

- Matcha poäng (mellan 0 och 1)
- Matchade bild
- Bildtaggen (som tilldelas under föregående avbrottsmoderering)
- Bild etiketter

Exempel extrahera:

    {
    ..............,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    ....
    }

## <a name="human-review-tool"></a>Verktyg för mänsklig granskning

Mer nuanced fall använder innehåll kontrollanten [granska verktyget](Review-Tool-User-Guide/human-in-the-loop.md) och dess API för att avbrottsmoderering resultat och innehållet i granskningen för din mänsklig kontrollanter. De granska datorn tilldelade-taggar och bekräfta sina slutliga beslut.

![Granska bild för mänsklig moderatorer](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Nästa steg

Testkör den [bild Avbrottsmoderering API konsolen](try-image-api.md) och använda REST API-kodexempel. Se även den [bild avbrottsmoderering .NET quickstart](image-moderation-quickstart-dotnet.md) om du är bekant med Visual Studio och C#.
