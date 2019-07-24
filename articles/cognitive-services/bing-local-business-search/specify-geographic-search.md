---
title: 'Använd geografiska gränser för att filtrera resultat från Bing-API: et för lokal affärs sökning'
titleSuffix: Azure Cognitive Services
description: 'Använd den här artikeln för att lära dig hur du filtrerar Sök Resultat från Bing-API: et för lokal sökning.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: e47a2ab8db17089773fd9a439b6dff225d6a8a29
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423309"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Använd geografiska gränser för att filtrera resultat från Bing-API: et för lokal affärs sökning

I Bing-API: et för lokal Business search kan du ange gränser för det specifika geografiska område som du vill söka efter `localCircularView` med `localMapView` parametrarna eller. Se till att du bara använder en parameter i dina frågor. 

Om en sökterm innehåller en explicit geografisk plats, används det lokala API: t för lokal handel automatiskt för att ange gränser för Sök resultaten. Om Sök termen till exempel är `sailing in San Diego` `San Diego` , kommer att användas som plats och andra angivna platser i frågeparametrar eller användar rubriker ignoreras. 

Om en geografisk plats inte identifieras i sökordet, och ingen geografisk plats har angetts med hjälp av frågeparametrar, försöker Bing-API: et för lokal sökning att fastställa plats från begärans `X-Search-ClientIP` eller `X-Search-Location` rubrikerna. Om ingen rubrik anges fastställer API: t antingen klientens IP-adress för begäran eller GPS-koordinater för mobila enheter.

## <a name="localcircularview"></a>localCircularView

`localCircularView` Parametern skapar ett cirkulärt geografiskt område runt en uppsättning latitud-/longitud-koordinater, som definieras av en RADIUS. När du använder den här parametern kommer svar från Bing-API: et för lokal sökning bara att inkludera platser i den här `localMapView` cirkeln, till skillnad från parametern som kan innehålla platser något utanför Sök området.

Om du vill ange ett cirkulärt geografiskt sökområde väljer du en latitud och en longitud som ska fungera som centrum för cirkeln och en radie i meter. Den här parametern kan sedan läggas till i en frågesträng, till exempel: `q=Restaurants&localCircularView=47.6421,-122.13715,5000`.

Slutför fråga:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

`localMapView` Parametern anger ett rektangulärt geografiskt område att söka i med två uppsättningar koordinater för att ange dess sydöstra och Northwest-hörn. När du använder den här parametern kan svar från Bing-API: et för lokal sökning inkludera platser inom och precis utanför det angivna området, `localCircularView` till skillnad från parametern, som bara omfattar platser inom Sök området.

Om du vill ange ett rektangulärt sökområde väljer du två uppsättningar med latitud-och longitud-koordinater som ska fungera som sydöstra och Northwest-hörn i kanten. Se till att definiera de sydöstra koordinaterna först, som i följande exempel: `localMapView=47.619987,-122.181671,47.6421,-122.13715`.

Slutför fråga:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>Nästa steg
- [Lokal affärs sökning Java snabb start](quickstarts/local-search-java-quickstart.md)
- [Snabb start för C# lokal affärs sökning](quickstarts/local-quickstart.md)
- [Snabb start för lokal Business search-nod](quickstarts/local-search-node-quickstart.md)
- [Snabb start för att söka i lokalt företag](quickstarts/local-search-python-quickstart.md)
