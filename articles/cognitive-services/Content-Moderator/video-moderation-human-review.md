---
title: Azure innehåll kontrollant - Video avbrottsmoderering | Microsoft Docs
description: Använd datorn stödd video avbrottsmoderering och mänsklig granska verktyg för att måttlig olämpligt innehåll
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/20/2018
ms.author: sajagtap
ms.openlocfilehash: fb26c9af55381c80a3f520b1a0068d8f72c91061
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "35351531"
---
# <a name="video-moderation"></a>Videomoderering

Använd innehållet kontrollant dator-stödd [video avbrottsmoderering](video-moderation-api.md) och [mänsklig granska verktyget](Review-Tool-User-Guide/human-in-the-loop.md) måttlig videor och betyg för vuxna (explicit) och dyr (något) innehåll att få bästa resultat för ditt företag.

## <a name="video-trained-classifier"></a>Video-utbildade klassificerare

Dator-stödd video klassificering uppnås antingen med bild tränats modeller eller video tränade modeller. Till skillnad från avbildningen tränats video klassificerare tränas Microsofts vuxna och dyr video klassificerare med videor. Den här metoden resulterar i bättre matchning kvalitet.

## <a name="shot-detection"></a>Som identifiering

Vid presentation klassificering information hjälper ytterligare video intelligence med mer flexibilitet för att analysera videor. I stället för att mata ut precis ramar innehåller Microsoft-video avbrottsmoderering tjänsten nivå som visar information för. Nu har du alternativet för att analysera dina videor på nivån som och RAM-nivå.
 
## <a name="key-frame-detection"></a>Nyckelbilden identifiering

I stället för att mata ut ramar med jämna mellanrum tjänsten video avbrottsmoderering identifierar och matar ut ramar som bara kan vara fullständig (bra). Funktionen kan effektivt ram-generering för ram-nivå vuxna och dyr analys.

Följande utdrag visar ett partiellt svar med potentiella bilderna och viktiga ramar vuxna och dyr resultat:

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


## <a name="visualization-for-human-reviews"></a>Visualisering för mänsklig granskningar

För mer nuanced fall företag behöver en mänsklig granska lösning för att återge videon dess ramar och datorn som tilldelats taggar. Hämta en fullständig överblick över insikter mänsklig moderatorer granska videor och ramar, ändra taggar och skicka sina beslut.

![standardvyn för video granska verktyget](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Player vyn för granskning av video-nivå

Video-nivå binära beslut fattas möjligt med en videospelare vy som visar möjliga vuxna och dyr ramar. Mänsklig granskare navigera video med olika alternativ för hastighet för att granska i bakgrunden. De bekräfta sina beslut genom att använda taggar.

![Visar video granska player](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Visa ramar för detaljerad granskning

En detaljerad video granskning för analys av bildruta för bildruta möjliggörs med ram-baserade. Mänsklig granskare granska och välj en eller flera ramar och växla mellan taggar om du vill bekräfta sina beslut. Ett valfritt nästa steg är bortredigering stötande ramar eller innehåll.

![Visa video granska verktyget ramar](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Betyg avbrottsmoderering

Videor har vanligtvis röst över som behöver avbrottsmoderering samt för stötande tal. Du kan använda tjänsten Azure Media Indexer för att konvertera tal till text och använda innehåll kontrollant granska API för att skicka utskrifter för text avbrottsmoderering i Granska-verktyget.

![Visar video granska betyg](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Nästa steg

Kom igång med den [video avbrottsmoderering quickstart](video-moderation-api.md). 

Lär dig hur du skapar [video granskar](video-reviews-quickstart-dotnet.md) för granskarna mänsklig från din kontrollerad utdata.

Lägg till [video betyg granskar](video-transcript-reviews-quickstart-dotnet.md) till din video granskningar.

Kolla in detaljerad vägledning om hur du utvecklar en [slutföra video avbrottsmoderering lösning](video-transcript-moderation-review-tutorial-dotnet.md). 
