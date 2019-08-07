---
title: Video Indexer scener, bilder och nyckel bild rutor – Azure
titlesuffix: Azure Media Services
description: Det här avsnittet ger en översikt över Video Indexer scener, bilder och nyckel rutor.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: cdabc1b6bfed519098f656710ef49a946e676cf2
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815651"
---
# <a name="scenes-shots-and-keyframes"></a>Scener, klipp och nyckelbilder

Video Indexer har stöd för att segmentera videor till temporala enheter baserat på strukturella och semantiska egenskaper. Den här funktionen gör det möjligt för kunder att enkelt bläddra i, hantera och redigera sitt video innehåll baserat på varierande granularitet. Till exempel, baserat på scener, dum par och nyckel rutor, som beskrivs i det här avsnittet.   

![Scener, klipp och nyckelbilder](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Scen identifiering  
 
Video Indexer anger när en scen ändras i video baserat på visuella tips. En scen visar en enskild händelse och den består av en serie med efterföljande bilder, som är semantiskt relaterade. En scen miniatyr är den första nyckel rutan i den underliggande bilden. Video Indexer segmenterar en video i scener utifrån färg samstämmighet över flera bilder och hämtar start-och slut tid för varje scen. Scen identifiering anses vara en utmanande uppgift eftersom det innebär att du kan kvantifiera semantiska aspekter av videor.

> [!NOTE]
> Gäller för videor som innehåller minst tre scener.

## <a name="shot-detection"></a>Bildidentifiering

Video Indexer bestämmer när en bild ändras i videon baserat på visuella tips, genom att spåra både plötsligt och gradvisa över gångar i färgschemat för intilliggande bild rutor. Bildens metadata innehåller en start-och slut tid, samt listan över nyckel rutor som ingår i bilden. Bilderna är i flera bild rutor tagna från samma kamera på samma tid.

## <a name="keyframe-detection"></a>Identifiering av nyckel rutor

Markerar de ramar som bäst representerar bilden. Nyckel rutor är de representativa bild rutor som valts från hela videon baserat på de egenskaper som är markerade (till exempel kontrast och stabilhet). Video Indexer hämtar en lista över nyckel Rute-ID: n som en del av bildens metadata, baserat på vilka kunder som kan extrahera nyckel Rute miniatyren. 

Nyckel rutor är associerade med bilder i JSON-utdata. 

## <a name="next-steps"></a>Nästa steg

[Granska Video Indexer utdata som genereras av API: et](video-indexer-output-json-v2.md#scenes)
