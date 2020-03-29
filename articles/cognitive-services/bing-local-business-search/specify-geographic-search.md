---
title: Använd geografiska gränser för att filtrera resultat från API:et för lokal företagssökning i Bing
titleSuffix: Azure Cognitive Services
description: Använd den här artikeln om du vill lära dig hur du filtrerar sökresultat från API:et för sökning i Bing lokalt företag.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 213457bc583494bbe039269b96b25990f7d0a961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "69906249"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Använd geografiska gränser för att filtrera resultat från API:et för lokal företagssökning i Bing

Med API:et för lokal företagssökning i Bing kan du ange gränser för `localCircularView` `localMapView` det specifika geografiska område som du vill söka efter med hjälp av parametrarna eller fråga. Var noga med att bara använda en parameter i dina frågor. 

Om en sökterm innehåller en explicit geografisk plats används det API för lokalt företag i Bing automatiskt för att ange gränser för sökresultaten. Om söktermen till `sailing in San Diego`exempel `San Diego` är , kommer den att användas som plats och alla andra angivna platser i frågeparametrarna eller användarhuvudena ignoreras. 

Om en geografisk plats inte identifieras i söktermen och ingen geografisk plats anges med frågeparametrarna, försöker API:et för sökning i Bing försöka fastställa plats från `X-Search-ClientIP` begärans eller `X-Search-Location` rubriker. Om inget av rubrikerna anges avgör API:et plats från antingen klient-IP-adressen för begäran eller GPS-koordinater för mobila enheter.

## <a name="localcircularview"></a>lokalaCircularView

Parametern `localCircularView` skapar ett cirkulärt geografiskt område runt en uppsättning latitud-/longitudkoordinater, definierade av en radie. När du använder den här parametern innehåller svar från API:et `localMapView` för lokal företagssökning endast platser i den här cirkeln, till skillnad från parametern som kan innehålla platser något utanför sökområdet.

Om du vill ange ett cirkulärt geografiskt sökområde väljer du en latitud och longitud som ska fungera som mitten av cirkeln och en radie i meter. Den här parametern kan sedan läggas till i `q=Restaurants&localCircularView=47.6421,-122.13715,5000`en frågesträng, till exempel: .

Fullständig fråga:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>lokalKartaVisa

Parametern `localMapView` anger ett rektangulärt geografiskt område som ska sökas, med hjälp av två uppsättningar koordinater för att ange dess sydöstra och nordvästra hörn. När du använder den här parametern kan svar från API:et för lokal `localCircularView` företagssökning innehålla platser inom och strax utanför det angivna området, till skillnad från parametern, som bara innehåller platser inom sökområdet.

Om du vill ange ett rektangulärt sökområde väljer du två uppsättningar med latitud-/longitudkoordinater som ska fungera som gränsens sydöstra och nordvästra hörn. Var noga med att definiera de sydostliga koordinaterna först, som i följande exempel: `localMapView=47.619987,-122.181671,47.6421,-122.13715`.

Fullständig fråga:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>Nästa steg
- [Snabbstart för lokal företagssökning](quickstarts/local-search-java-quickstart.md)
- [Snabbstart för lokal företagssökning C#](quickstarts/local-quickstart.md)
- [Snabbstart för söknod för lokalt företag](quickstarts/local-search-node-quickstart.md)
- [Snabbstart för Lokal företagssökning i Python](quickstarts/local-search-python-quickstart.md)
