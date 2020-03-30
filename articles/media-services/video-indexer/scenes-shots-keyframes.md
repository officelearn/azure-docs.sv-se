---
title: Video Indexer scener, bilder och nyckelrutor
titleSuffix: Azure Media Services
description: Det här avsnittet innehåller en översikt över videoindexerare scener, bilder och nyckelrutor.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: a833fd808049cfce95b182910e50e38d3c39f4e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245946"
---
# <a name="scenes-shots-and-keyframes"></a>Scener, klipp och nyckelbilder

Video Indexer stöder segmentering av videor i temporala enheter baserat på strukturella och semantiska egenskaper. Med den här funktionen kan kunder enkelt bläddra bland, hantera och redigera sitt videoinnehåll baserat på olika granulariteter. Till exempel baserat på scener, bilder och nyckelrutor som beskrivs i det här avsnittet.   

![Scener, klipp och nyckelbilder](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Scendetektering  
 
Video Indexerar när en scen ändras i video baserat på visuella tips. En scen visar en enda händelse och den består av en serie på varandra följande bilder, som är semantiskt relaterade. En scenminiatyr är den första nyckelbildrutan för det underliggande skottet. Video indexerar segmenterar en video i scener baserat på färgsammanhållning över på varandra följande bilder och hämtar början och sluttiden för varje scen. Scenidentifiering anses vara en utmanande uppgift eftersom det innebär att kvantifiera semantiska aspekter av videor.

> [!NOTE]
> Gäller videor som innehåller minst 3 scener.

## <a name="shot-detection"></a>Bildigetering

Video Indexer avgör när ett skott ändras i videon baserat på visuella ledtrådar, genom att spåra både plötsliga och gradvisa övergångar i färgschemat för intilliggande bildrutor. Bildens metadata innehåller en start- och sluttid, samt en lista över nyckelrutor som ingår i det skottet. Bilderna är på varandra följande bilder tagna från samma kamera samtidigt.

## <a name="keyframe-detection"></a>Identifiering av nyckelbildruta

Video Indexerar väljer de ramar som bäst representerar varje bild. Nyckelrutor är de representativa bildrutor som valts från hela videon baserat på estetiska egenskaper (till exempel kontrast och stabilhet). Video Indexer hämtar en lista över nyckelbildrutor som en del av bildens metadata, baserat på vilken kunder kan extrahera nyckelbildrutan som en högupplöst bild.  

### <a name="extracting-keyframes"></a>Extrahera nyckelrutor

Om du vill extrahera högupplösta nyckelrutor för videon måste du först ladda upp och indexera videon.

![Nyckelrutor](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>Med videoindexerarens webbplats

Om du vill extrahera nyckelrutor med videoindexerarens webbplats laddar du upp och indexerar videon. När indexeringsjobbet är klart klickar du på **knappen Hämta** och väljer **Artefakter (ZIP)**. Då hämtas mappen artefakter till datorn. 

![Nyckelrutor](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Packa upp och öppna mappen. I *mappen _KeyframeThumbnail* och du hittar alla nyckelrutor som extraherades från videon. 

#### <a name="with-the-video-indexer-api"></a>Med API:et för videoindexerare

Om du vill hämta nyckelrutor med videoindexerator-API:et laddar du upp och indexerar videon med hjälp av [uppladdningsvideosamtalet.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?) När indexeringsjobbet är klart ringer du [Hämta videoindex](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?). Detta ger dig alla de insikter som Video Indexer extraheras från ditt innehåll i en JSON fil.  

Du får en lista över nyckelbildrutor som en del av varje bilds metadata. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

Du måste nu köra var och en av dessa nyckelbildrutor i samtalet [Hämta miniatyrer.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?) Detta kommer att ladda ner var och en av keyframe bilder till din dator. 

## <a name="editorial-shot-type-detection"></a>Identifiering av redaktionell skotttyp

Nyckelrutor är associerade med bilder i utdata JSON. 

Skotttypen som är associerad med ett individuellt skott i de insikter JSON representerar dess redaktionella typ. Du kan hitta dessa bildtyp egenskaper användbara när du redigerar videor i klipp, trailers, eller när du söker efter en viss typ av nyckelbildruta för konstnärliga ändamål. De olika typerna bestäms baserat på analys av den första nyckelbildrutan för varje skott. Skott identifieras med skala, storlek och placering av ansikten som visas i deras första nyckelbildruta. 

Skottstorleken och fotoskalet bestäms baserat på avståndet mellan kameran och de ansikten som visas i ramen. Med hjälp av dessa egenskaper identifierar Video Indexer följande bildtyper:

* Bred: visar en hel persons kropp.
* Medium: visar en persons överkropp och ansikte.
* Närbild: visar främst en persons ansikte.
* Extrem närbild: visar en persons ansikte som fyller skärmen. 

Skotttyper kan också bestämmas av platsen för ämnestecken med avseende på mitten av ramen. Den här egenskapen definierar följande bildtyper i Video Indexer:

* Vänster ansikte: en person visas i den vänstra sidan av ramen.
* Mittysikt: en person visas i den centrala delen av ramen.
* Höger ansikte: en person visas i höger sida av ramen.
* Utomhus: en person visas i en utomhusmiljö.
* Inomhus: en person visas i en inomhusmiljö.

Ytterligare egenskaper:

* Två skott: visar två personers ansikten av medelstorlek.
* Flera ansikten: mer än två personer.


## <a name="next-steps"></a>Nästa steg

[Undersök videoindexeringsutdata som produceras av API:et](video-indexer-output-json-v2.md#scenes)
