---
title: Video måtta med mänsklig granskning - Content Moderator
titleSuffix: Azure Cognitive Services
description: Använd maskinstödd videomodering och granskningsverktyget för att moderera olämpligt innehåll
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 0c031a890efc7fad7e5d9caefce3b0e66c515d90
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404242"
---
# <a name="video-moderation-with-human-review"></a>Video måtta med mänsklig granskning

Använd Content Moderator s maskinstödd [video moderering](video-moderation-api.md) och [granskning verktyg](Review-Tool-User-Guide/human-in-the-loop.md) för att moderera videor och utskrifter för vuxna (explicit) och racy (suggestiva) innehåll för att få bästa resultat för ditt företag.

## <a name="video-trained-classifier-preview"></a>Videoutbildad klassificerare (förhandsgranskning)

Maskinassisterad videoklassificering uppnås antingen med bildutbildade modeller eller videoutbildade modeller. Till skillnad från bildutbildade videoklassificerare tränas Microsofts videoklassificerare för vuxna och racy med videor. Den här metoden resulterar i bättre matchkvalitet.

## <a name="shot-detection"></a>Bildigetering

Vid utdata från klassificeringsinformationen hjälper ytterligare videointelligens till mer flexibilitet vid analys av videor. Istället för att mata ut bara ramar, Microsofts video moderering tjänst ger skott-nivå information också. Du har nu möjlighet att analysera dina videor på skottnivå och bildrutenivå.

## <a name="key-frame-detection"></a>Identifiering av nyckelramar

I stället för att mata ut bildrutor med jämna mellanrum identifierar och utdata endast eventuellt kompletta (bra) bildrutor. Funktionen möjliggör effektiv ramgenerering för vuxen- och racyanalys på ramnivå.

Följande utdrag visar ett partiellt svar med potentiella bilder, nyckelbildrutor och vuxen- och racypoäng:

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

## <a name="visualization-for-human-reviews"></a>Visualisering för mänskliga recensioner

För mer nyanserade fall behöver företag en lösning för mänsklig granskning för att göra videon, dess bildrutor och maskintilldelade taggar. De mänskliga moderatorerna som granskar videor och ramar får en fullständig bild av insikter, ändrar taggar och skickar in sina beslut.

![Standardvy för videogranskningsverktyg](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Spelarvy för granskning på videonivå

Binära beslut på videonivå möjliggörs med en videospelarvy som visar potentiella vuxna och racy-ramar. De mänskliga granskarna navigera videon med olika hastighetsalternativ för att undersöka scenerna. De bekräftar sina beslut genom att växla taggarna.

![videogranskningsverktygsspelarspelare](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Bildrutpanelvy för detaljerade recensioner

En detaljerad videogranskning för bildruta för bildruta analys möjliggörs med en rambaserad vy. De mänskliga granskarna granskar och väljer en eller flera bildrutor och växlar taggar för att bekräfta sina beslut. Ett valfritt nästa steg är bortredigering av stötande ramar eller innehåll.

![videogranskningsverktyg ramar vy](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Avskriftsmoderering

Videor har vanligtvis voice over som behöver måtta samt för stötande tal. Du använder Azure Media Indexer-tjänsten för att konvertera tal till text och använda Content Moderators gransknings-API för att skicka avskriften för textmoderation i granskningsverktyget.

![videogranskningsverktygskopior](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Nästa steg

- Kom igång med [snabbstarten för videomodering](video-moderation-api.md).
- Läs om hur du skapar [videorecensioner](video-reviews-quickstart-dotnet.md) för dina mänskliga granskare från din modererade utdata.
- Lägg till [recensioner av videoavskrifter](video-transcript-reviews-quickstart-dotnet.md) i dina videorecensioner.
- Kolla in den detaljerade handledning om hur man utvecklar en [komplett video moderering lösning](video-transcript-moderation-review-tutorial-dotnet.md).