---
title: Bildmoderation - Innehållsmoderator
titleSuffix: Azure Cognitive Services
description: Använd Content Moderators maskinstödda bildmoderation och verktyget Granska för mänskliga produkter i slingan för att moderera bilder för barn- och racyinnehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 36777208dc8ac179f1aaf345c374a33001e3f8bd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404266"
---
# <a name="learn-image-moderation-concepts"></a>Lär dig begrepp för bildmoderering

Använd Content Moderators maskinstödda bildmoderation och [verktyget Granska för mänskliga produkter i slingan](Review-Tool-User-Guide/human-in-the-loop.md) för att moderera bilder för barn- och racyinnehåll. Sök igenom bilder efter textinnehåll och extrahera texten och identifiera ansikten. Du kan matcha bilder mot anpassade listor och vidta ytterligare åtgärder.

## <a name="evaluating-for-adult-and-racy-content"></a>Utvärdering för barnförbjudet och racyinnehåll

Åtgärden **Utvärdera** returnerar en konfidenspoäng mellan 0 och 1. Den returnerar också booleska data som är lika med sant eller falskt. Dessa värden förutsäger om bilden innehåller potentiellt innehåll för vuxna eller racy. När du anropar API:et med avbildningen (fil eller URL) innehåller det returnerade svaret följande information:

    "ImageModeration": {
      .............
      "adultClassificationScore": 0.019196987152099609,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.032390203326940536,
      "isImageRacyClassified": false,
      ............
      ],

> [!NOTE]
> 
> - `isImageAdultClassified` representerar den potentiella förekomsten av bilder som kan uppfattas som sexuellt explicita eller ämnade för vuxna.
> - `isImageRacyClassified` representerar den potentiella förekomsten av bilder som kan uppfattas som sexuellt laddade eller ämnade för vuxna.
> - Poängen är mellan 0 och 1. Ju högre poäng, desto högre modell förutspår att kategorin kan vara tillämplig. Den här förhandsversionen bygger på en statistisk modell i stället för manuellt kodade resultat. Vi rekommenderar att du testar med ditt eget innehåll för att avgöra hur varje kategori anpassar sig till dina krav.
> - De booleska värdena är antingen sanna eller falska beroende på de interna poängtrösklarna. Kunder bör bedöma om de ska använda det här värdet eller besluta om anpassade tröskelvärden baserat på deras innehållsprinciper.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Identifiera text med optisk teckenigenkänning (OCR)

**Ocr-åtgärden (Optical Character Recognition)** förutsäger förekomsten av textinnehåll i en bild och extraherar det för textmoderering, bland annat. Du kan ange språket. Om du inte anger något språk är identifieringen som standard på engelska.

Svaret innehåller följande information:
- Den ursprungliga texten.
- De identifierade textelementen med sina konfidenspoäng.

Exempel utdrag:

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


## <a name="detecting-faces"></a>Identifiera ansikten

Att upptäcka ansikten hjälper till att upptäcka personuppgifter som ansikten i bilderna. Du upptäcker potentiella ansikten och antalet potentiella ansikten i varje bild.

Ett svar innehåller denna information:

- Antalet ansikten
- Lista över platser för upptäckta ansikten

Exempel utdrag:


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

I många onlinegrupper kan stötande objekt delas flera gånger efter följande dagar, veckor och månader när användare har laddat upp bilder eller annan typ av innehåll. Kostnaderna för upprepade skanning och filtrering av samma bild eller till och med något modifierade versioner av bilden från flera ställen kan vara dyra och felbenägna.

I stället för att moderera samma bild flera gånger lägger du till stötande bilder i din anpassade lista över blockerat innehåll. På så sätt jämför ditt innehåll modereringssystem inkommande bilder med dina anpassade listor och stoppar ytterligare bearbetning.

> [!NOTE]
> Det finns en maxgräns på **5 bildlistor** där varje lista **inte får överstiga 10 000 bilder**.
>

Innehållsmoderatorn tillhandahåller ett komplett API för [bildlisthantering](try-image-list-api.md) med åtgärder för att hantera listor med anpassade avbildningar. Börja med [API-konsolen Image Lists](try-image-list-api.md) och använd rest-API-kodexemplen. Kolla också in [snabbstarten för bildlistan .NET](image-lists-quickstart-dotnet.md) om du är bekant med Visual Studio och C#.

## <a name="matching-against-your-custom-lists"></a>Matcha mot dina anpassade listor

Åtgärden Matchning möjliggör luddig matchning av inkommande bilder mot någon av dina anpassade listor, som skapats och hanterats med hjälp av liståtgärderna.

Om en matchning hittas returnerar åtgärden identifieraren och modereringstaggarna för den matchade bilden. Svaret innehåller denna information:

- Matchpoäng (mellan 0 och 1)
- Matchad bild
- Bildtaggar (som tilldelats under tidigare moderering)
- Bildetiketter

Exempel utdrag:

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

## <a name="review-tool"></a>Granskningsverktyg

För mer nyanserade fall, använd content moderator [review-verktyget](Review-Tool-User-Guide/human-in-the-loop.md) och dess API för att visa modereringsresultat och innehåll i granskningen för dina mänskliga moderatorer. De granskar de maskintilldelade taggarna och bekräftar sina slutliga beslut.

![Bildgranskning för mänskliga moderatorer](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Nästa steg

Testa [api-konsolen Image Moderation](try-image-api.md) och använd rest API-kodexemplen. Kolla också in avsnittet Bildmoderering i [snabbstarten för .NET SDK](dotnet-sdk-quickstart.md) om du är bekant med Visual Studio och C#.
