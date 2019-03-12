---
title: Använda geografiska gränser för att filtrera resultaten från den lokala företag i Bing | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Använd den här artikeln om du vill lära dig att filtrera sökresultaten från den lokala företag i Bing.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 193d0b25276b90f6047943ded83f2000dd4d3436
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775721"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Använda geografiska gränser för att filtrera resultaten från den lokala företag i Bing

Den lokala företag i Bing kan du ange gränser på specifika geografiska område som du vill söka genom att använda den `localCircularView` eller `localMapView` Frågeparametrar. Glöm inte att använda endast en parameter i dina frågor. 

Om en sökterm innehåller en explicit geografisk plats, använda den lokala företag i Bing automatiskt den för att ange gränser för sökresultaten. Om sökordet är till exempel `sailing in San Diego`, sedan `San Diego` används när platsen och andra angivna platser i frågeparametrar eller användaren rubriker kommer att ignoreras. 

Om en geografisk plats inte har identifierats i söktermen, och ingen geografisk plats har angetts med hjälp av frågeparametrar, den lokala företag i Bing försöker fastställa platsen i begäran `X-Search-ClientIP` eller `X-Search-Location` rubriker. Om varken sidhuvudet har angetts, API: N avgör platsen från antingen klientens IP-Adressen för begäran eller GPS-koordinater för mobila enheter.

## <a name="localcircularview"></a>localCircularView

Den `localCircularView` parametern skapar ett cirkulärt geografiskt område runt en uppsättning koordinater för latitud/longitud, definieras av en radius. När du använder den här parametern svar från den lokala företag i Bing enbart att inkludera platser inom den här cirkel, till skillnad från den `localMapView` parameter som kan innehålla platser lite utanför området.

Om du vill ange en cirkulär geografiska sökområdet, väljer du en latitud och longitud som fungerar som mitten av cirkeln och en radie i mätare. Den här parametern kan sedan läggas till en frågesträng, till exempel: `q=Restaurants&localCircularView=47.6421,-122.13715,5000`.

Fullständig fråga:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

Den `localMapView` parametern anger ett rektangulärt geografiskt område kan söka, använder två uppsättningar med koordinater för att ange dess sydöstra Australien och nordvästra hörn. När du använder den här parametern svar från den lokala företag i Bing kan omfatta platser inom och utanför det angivna området, till skillnad från den `localCircularView` parameter som bara innehåller platser inom området.

Om du vill ange en rektangulär sökområdet, väljer du två uppsättningar med koordinater för latitud/longitud som fungerar som sydöst och nordvästra hörnen på gränsen. Se till att definiera sydöstra koordinaterna först, som i följande exempel: `localMapView=47.619987,-122.181671,47.6421,-122.13715`.

Fullständig fråga:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>Nästa steg
- [Lokala företag Search Java Snabbstart](quickstarts/local-search-java-quickstart.md)
- [Sök i lokala företag C# Snabbstart](quickstarts/local-quickstart.md)
- [Lokala företag Search Node-Quickstart](quickstarts/local-search-node-quickstart.md)
- [Lokala företag Search Python-Snabbstart](quickstarts/local-search-python-quickstart.md)
