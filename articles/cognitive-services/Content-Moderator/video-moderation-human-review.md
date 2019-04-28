---
title: Videomoderering med mänsklig granskning – Content Moderator
titlesuffix: Azure Cognitive Services
description: Använd datorstödd videomoderering och verktyg för mänsklig granskning för att hantera olämpligt innehåll
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/20/2018
ms.author: sajagtap
ms.openlocfilehash: 43a43ddcbfc656a3eb5a274e1bb63a473b7c89a2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098209"
---
# <a name="video-moderation-with-human-review"></a>Videomoderering med mänsklig granskning

Använd Content Moderator datorstödd [videomoderering](video-moderation-api.md) och [verktyg för mänsklig granskning](Review-Tool-User-Guide/human-in-the-loop.md) måttlig videor och betyg för vuxen (explicit) och olämpligt (något) innehåll att få bästa resultat för ditt företag.

## <a name="video-trained-classifier-preview"></a>Video-utbildade klassificerare (förhandsversion)

Maskinassisterad video klassificering uppnås antingen med bild som tränas modeller eller video tränade modeller. Till skillnad från avbildningen tränas video klassificerare tränas Microsofts vuxet och vågat innehåll video klassificerare med videor. Den här metoden resulterar i bättre matchning kvalitet.

## <a name="shot-detection"></a>Bildidentifiering

När du genererar klassificering information kan ytterligare video intelligens mer flexibilitet att analysera videor. I stället för att mata ut bara bildrutor innehåller Microsofts videomodereringen på servernivå som visar information för. Du har nu möjlighet att analysera dina videoklipp på som nivå och RAM-nivå.
 
## <a name="key-frame-detection"></a>Viktiga ramtyp

I stället för att mata ut bildrutor med jämna mellanrum videomodereringen identifierar och visar endast potentiellt fullständiga (bra) bildrutor. Funktionen kan effektivt ramens generation för ramens på servernivå vuxet och vågat innehåll analys.

Följande utdrag visar ett partiellt svar med potentiella skärmbilder och viktiga bildrutor vuxet och vågat innehåll poäng:

    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]


## <a name="visualization-for-human-reviews"></a>Visualisering för mänsklig granskning

För mer nyanserade fall företag behöver en lösning för mänsklig granskning för att återge videon, dess ramar och datorn tilldelade taggar. Få en fullständig vy över insikterna mänskliga moderatorer granskar videor och ramar, ändra taggar och skicka sina beslut.

![standardvyn för video granska verktyget](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Player vy för video på servernivå granskning

Video på servernivå binära beslut fattas möjligt med en videospelare vy som visar potentiellt vuxet eller olämpligt bildrutor. Mänsklig granskare navigera i videon med olika hastighet alternativ för att undersöka kulisserna. De bekräftar beslutsfattandet med taggar.

![Granska video player visar](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Bildrutor vy för detaljerad granskning

En detaljerad video granskning för bildruta för bildruta analys möjliggörs med en ram-baserad vy. Mänsklig granskare granskar och välj en eller flera bildrutor och växla taggar om du vill bekräfta sina beslut. Ett valfritt nästa steg är redigering av stötande bildrutor eller innehåll.

![Visa video granska verktyget bildrutor](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Avskriftsmoderering

Videor har vanligtvis Röst via som behöver moderering samt för stötande tal. Du kan använda tjänsten Azure Media Indexer för att omvandla tal till text och använder Content Moderator granska API för att skicka avskrift för textmoderering inom granskningsverktyget.

![Visar video granska avskrift](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Nästa steg

Kom igång med den [videomoderering snabbstarten](video-moderation-api.md). 

Lär dig hur du skapar [videon går igenom](video-reviews-quickstart-dotnet.md) för din mänsklig granskare från dina kontrollerad utdata.

Lägg till [videoavskriften granskar](video-transcript-reviews-quickstart-dotnet.md) på din video granskningar.

Kolla in detaljerad genomgång om hur du utvecklar en [slutföra videomoderering lösning](video-transcript-moderation-review-tutorial-dotnet.md). 
