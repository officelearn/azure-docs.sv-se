---
title: Video Indexer scener, bilder och nyckel bild rutor
titleSuffix: Azure Media Services
description: Det här avsnittet ger en översikt över Video Indexer scener, bilder och nyckel rutor.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: b24778434596f583be44572612c856fa4e0cecde
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860242"
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

## <a name="editorial-shot-type-detection"></a>Identifiering av redaktionella typer av tagning

Den bildtyp som är associerad med en enskild bild i insikts-JSON representerar en redaktionell typ. Du kanske tycker att de här typerna av bildtyp är användbara när du redigerar videor i klipp, släp vagnar eller när du söker efter en speciell typ av nyckel bild ruta för konstnärliga bruk. De olika typerna bestäms baserat på analys av den första nyckel rutan för varje bild. Bilderna identifieras av skala, storlek och plats för de ytor som visas i den första nyckel rutan. 

Intagningens storlek och skala bestäms utifrån avståndet mellan kameran och de ytor som visas i ramen. Med dessa egenskaper identifierar Video Indexer följande typer av bild:

* Bred: visar en hel persons brödtext.
* Medel: visar en persons övre brödtext och ansikte.
* Stäng: visar främst en persons ansikte.
* Extrem närbild: visar en persons ansikte som fyller skärmen. 

Olika typer av tagningar kan också bestämmas genom platsen för ämnes tecknen med avseende på ramens centrum. Den här egenskapen definierar följande bild typer i Video Indexer:

* Vänster ansikte: en person visas på vänster sida av ramen.
* Mitten ansikte: en person visas i den centrala regionen i ramen.
* Höger sida: en person visas till höger i ramen.
* Utomhus: en person visas i en inställning för utomhus.
* I inomhus: en person visas i en intill-inställning.

Ytterligare egenskaper:

* Två bilder: visar två personers ansikten av medel storlek.
* Flera ansikten: fler än två personer.

## <a name="next-steps"></a>Nästa steg

[Granska Video Indexer utdata som genereras av API: et](video-indexer-output-json-v2.md#scenes)
