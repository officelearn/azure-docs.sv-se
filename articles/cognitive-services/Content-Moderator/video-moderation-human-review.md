---
title: Videokontrollant med mänsklig granskning – Content Moderator
titleSuffix: Azure Cognitive Services
description: Använd datorbaserade videokontrollanter och gransknings verktyget för att måttligt olämpligt innehåll
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 0c031a890efc7fad7e5d9caefce3b0e66c515d90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "81404242"
---
# <a name="video-moderation-with-human-review"></a>Videokontrollant med mänsklig granskning

Använd Content Moderator datorbaserade [videokontrollanter](video-moderation-api.md) och [gransknings verktyg](Review-Tool-User-Guide/human-in-the-loop.md) för att få till gång till måttliga videor och avskrifter för vuxen (Explicit) och vågat (förslag) innehåll för att få bästa möjliga resultat för ditt företag.

## <a name="video-trained-classifier-preview"></a>Video-utbildad klassificerare (för hands version)

Den maskinbaserade video klassificeringen uppnås antingen med bildtränade modeller eller video intränade modeller. Till skillnad från bildtränade video klassificerare är Microsofts vuxna och vågat video klassificerare tränad med videor. Den här metoden ger bättre matchning av kvalitet.

## <a name="shot-detection"></a>Bild identifiering

När du utökar klassificerings informationen hjälper ytterligare video information till större flexibilitet vid analys av videor. I stället för att bara placera bild rutorna ger Microsofts video redigerings tjänst information på bild nivå. Nu har du möjlighet att analysera dina videor på den bild nivå och på den andra nivån.

## <a name="key-frame-detection"></a>Identifiering av nyckel rutor

I stället för att lägga till ramar med jämna mellanrum, identifierar videoinspelnings tjänsten och matar bara ut potentiellt slutförda (lämpliga) ramar. Funktionen möjliggör effektiv ram-generering för barns och vågat analys på radnivå.

Följande utdrag visar ett partiellt svar med potentiella bilder, nyckel ramar och vuxen och vågat resultat:

```json
"fragments":[  
  {  
    "start":0,
    "duration":18000
  },
  {  
    "start":18000,
    "duration":3600,
    "interval":3600,
    "events":[  
      [  
        {  
          "reviewRecommended":false,
          "adultScore":0.00001,
          "racyScore":0.03077,
          "index":5,
          "timestamp":18000,
          "shotIndex":0
        }
      ]
    ]
  },
  {  
    "start":18386372,
    "duration":119149,
    "interval":119149,
    "events":[  
      [  
        {  
          "reviewRecommended":true,
          "adultScore":0.00000,
          "racyScore":0.91902,
          "index":5085,
          "timestamp":18386372,
          "shotIndex":62
        }
      ]
    ]
```

## <a name="visualization-for-human-reviews"></a>Visualisering för mänsklig granskning

För fler nyanserade-fall behöver företag en mänsklig gransknings lösning för att återge videon, dess ramar och tilldelade taggar. De mänskliga moderatorerna som visar videor och ramar får en komplett vy över insikter, ändrar Taggar och skickar sina beslut.

![Standardvy för video gransknings verktyg](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Player-vy för granskning på video nivå

Binärfiler på video nivå är möjligt med en Videos pelar vy som visar potentiella vuxna och vågat-ramar. De mänskliga granskarna navigerar i videon med olika hastighets alternativ för att undersöka scener. De bekräftar sina beslut genom att växla taggarna.

![vy för video gransknings verktyg](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Bild Rute visning för detaljerad granskning

En detaljerad video granskning för bildruta-för-bildruta-analyser görs möjlig med en bildruta-baserad vy. Mänsklig granskare granskar och väljer en eller flera ramar och växlar taggar för att bekräfta sina beslut. Ett valfritt nästa steg är bortredigering av de stötande bild rutorna eller innehållet.

![bild Rute vy för video gransknings verktyg](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Avskriftsmoderering

Videor har vanligt vis röst över som behöver moderatorn även för stötande tal. Du använder Azure Media Indexer tjänsten för att konvertera tal till text och använda Content Moderators gransknings-API för att skicka avskriften för text redigering i gransknings verktyget.

![avskrifts vy för video gransknings verktyg](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Nästa steg

- Kom igång med snabb starten för [videoinspelning](video-moderation-api.md).
- Lär dig hur du genererar [video granskningar](video-reviews-quickstart-dotnet.md) för dina mänskliga granskare från dina kontrollerade utdata.
- Lägg till [video avskrifts granskningar](video-transcript-reviews-quickstart-dotnet.md) i dina video granskningar.
- Kolla in den detaljerade självstudien om hur du utvecklar en [komplett video redigerings lösning](video-transcript-moderation-review-tutorial-dotnet.md).