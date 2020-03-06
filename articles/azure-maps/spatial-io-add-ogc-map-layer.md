---
title: Lägga till ett Open Geospatial Consortium (OGC) kart skikt | Microsoft Azure Maps
description: Lär dig hur du lägger till ett OGC kart lager på kartan och hur du använder de olika alternativen i OgcMapLayer-klassen.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: c59376344d2df7e9e9c76e630a4462b26343d187
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78371017"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Lägg till ett kart skikt från Open Geospatial Consortium (OGC)

`atlas.layer.OgcMapLayer`-klassen kan täcka WMTS-bilder (Web mappnings tjänster) och-på kartan. WMS är ett standard protokoll som har utvecklats av OGC för att betjäna tillhörda kart bilder via Internet. Bild-geografi är en process för att associera en avbildning till en geografisk plats. WMTS är också ett standard protokoll som har utvecklats av OGC. Den är utformad för att betjäna förrenderade och refererade kart paneler.

I följande avsnitt beskrivs de funktioner för webb mappnings tjänsten som stöds av klassen `OgcMapLayer`.

**Webb mappnings tjänst (WMS)**

- Versioner som stöds: `1.0.0`, `1.1.0`, `1.1.1`och `1.3.0`
- Tjänsten måste ha stöd för `EPSG:3857` projektions system, eller så måste tjänsten kunna hantera omprojektioner.
- GetFeatureInfo kräver att tjänsten stöder `EPSG:4326` eller hanterar omprojektioner. 
- Åtgärder som stöds:

    | | |
    | :-- | :-- |
    | GetCapabilities | Hämtar metadata om tjänsten med funktioner som stöds |
    | GetMap | Hämtar en kart bild för en angiven region |
    | GetFeatureInfo | Hämtar `feature_info`, som innehåller underliggande data om funktionen |

**Webb kart panels tjänst (WMTS)**

- Versioner som stöds: `1.0.0`
- Panelerna måste vara kvadratiska, som `TileWidth == TileHeight`.
- Boknings system som stöds: `EPSG:3857` eller `GoogleMapsCompatible` 
- TileMatrix-identifieraren måste vara ett heltals värde som motsvarar en zoomnings nivå på kartan. På en Azure-karta är zoomnings nivån ett värde mellan `"0"` och `"22"`. Därför stöds `"0"`, men `"00"` stöds inte.
- Åtgärder som stöds:

    | | |
    | :-- | :-- |
    | GetCapabilities | Hämtar de åtgärder och funktioner som stöds |
    | GetTile | Hämtar bilder för en viss panel |

## <a name="overlay-an-ogc-map-layer"></a>Täcka över ett OGC kart skikt

`url` kan vara bas-URL: en för tjänsten eller en fullständig URL med frågan för att hämta funktionerna i tjänsten. Beroende på den information som anges kan WFS-klienten försöka med flera standard-URL-format för att fastställa hur du ska få åtkomst till tjänsten första gången.

Följande kod visar hur du lägger till ett OGC kart skikt på kartan.

<br/>

<iframe height='700' scrolling='no' title='Exempel på OGC Map-lager' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se exemplet pennan <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC Map Layer</a> genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Alternativ för OGC kart skikt

Exemplet nedan visar de olika alternativen för OGC-kart skikt. Du kan klicka på knappen kod penna i det övre högra hörnet för att redigera kod pennan.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för OGC kart skikt' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för pen <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>OGC Map Layer</a> genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>OGC webb kart tjänst Utforskare

Följande verktyg översätter bilder från webb kart tjänsterna (WMS) och webb kart panels tjänsterna (WMTS) som lager. Du kan välja vilka lager i tjänsten som återges på kartan. Du kan också Visa associerade förklaringar för dessa lager.

<br/>

<iframe height='700' scrolling='no' title='OGC webb kart tjänst Utforskare' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>service Explorer för pen OGC-webbkartor</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Du kan också ange kart inställningarna för att använda en proxy-tjänst. Med proxy-tjänsten kan du läsa in resurser som finns i domäner som inte har CORs aktiverat.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [OgcMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Se följande artiklar, som innehåller kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Ansluta till en WFS-tjänst](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Utnyttja kärn åtgärder](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Information om data format som stöds](spatial-io-supported-data-format-details.md)
