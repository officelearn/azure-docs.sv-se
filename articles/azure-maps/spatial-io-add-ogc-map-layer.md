---
title: Lägg till ett kartlager för OGC (Open Geospatial Consortium) | Microsoft Azure Maps
description: Lär dig hur du överlagrar ett OGC-kartlager på kartan och hur du använder de olika alternativen i klassen OgcMapLayer.
author: philmea
ms.author: philmea
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b753ecfc07cfb3806838f8a05dbe33ef0bb92730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334286"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Lägga till ett kartlager från OGC (Open Geospatial Consortium)

Klassen `atlas.layer.OgcMapLayer` kan överlagra WMS-bilder (Web Map Services) och WMTS-bilder (Web Map Tile Services) på kartan. WMS är ett standardprotokoll utvecklat av OGC för att betjäna georeferenced kartbilder över internet. Bildgeoreferencing är processerna för att associera en bild till en geografisk plats. WMTS är också ett standardprotokoll utvecklat av OGC. Den är utformad för att servera pre-renderade och georefererade kartplattor.

I följande avsnitt beskrivs de webbkarttjänstfunktioner som stöds av `OgcMapLayer` klassen.

**Webbkarttjänst (WMS)**

- Versioner som `1.0.0` `1.1.0`stöds: , , `1.1.1`och`1.3.0`
- Tjänsten måste stödja `EPSG:3857` projektionssystemet eller hantera omprojektioner.
- GetFeatureInfo kräver tjänsten `EPSG:4326` för att stödja eller hantera reprojections. 
- Åtgärder som stöds:

    | | |
    | :-- | :-- |
    | GetCapabilities | Hämtar metadata om tjänsten med de funktioner som stöds |
    | Hämtamap | Hämtar en kartbild för ett angivet område |
    | FåFeatureInfo | Hämtar `feature_info`, som innehåller underliggande data om funktionen |

**Webbkarta kakel tjänst (WMTs)**

- Versioner som stöds:`1.0.0`
- Plattor måste vara fyrkantiga, så att `TileWidth == TileHeight`.
- CRS stöds: `EPSG:3857` eller`GoogleMapsCompatible` 
- TileMatrix-identifieraren måste vara ett heltalsvärde som motsvarar en zoomnivå på kartan. På en azure-karta är zoomnivån `"0"` `"22"`ett värde mellan och . Så `"0"` stöds, men `"00"` stöds inte.
- Åtgärder som stöds:

    | | |
    | :-- | :-- |
    | GetCapabilities | Hämtar de åtgärder och funktioner som stöds |
    | GetTile (upp till andra) | Hämtar bilder för en viss panel |

## <a name="overlay-an-ogc-map-layer"></a>Överlagra ett OGC-kartlager

Den `url` kan vara bas-URL för tjänsten eller en fullständig URL med frågan för att få funktioner för tjänsten. Beroende på vad som ges kan WFS-klienten prova flera standard-URL-format för att avgöra hur du först kommer åt tjänsten.

Följande kod visar hur du överlagrar ett OGC-kartlager på kartan.

<br/>

<iframe height='700' scrolling='no' title='Exempel på OGC-kartlager' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se exemplet med pen <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC-kartlagret</a> från Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Alternativ för OGC-kartlager

Nedanstående exempel visar de olika OGC-kartlageralternativen. Du kan klicka på kodpen pennan knappen längst upp till höger för att redigera kodpennan.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för OGC-kartlager' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för Pen <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>OGC-kartlager</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>Utforskare för Webbmappningstjänsten för OGC

Följande verktyg överlagrar bilder från WMS (Web Map Services) och WMTS (Web Map Tile Services) som lager. Du kan välja vilka lager i tjänsten som ska återges på kartan. Du kan också visa associerade förklaringar för dessa lager.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='Utforskare för Webbmappningstjänsten för OGC' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se utforskaren för Pen <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC</a> Web<a href='https://codepen.io/azuremaps'>@azuremaps</a>Map Service av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Du kan också ange vilka kartinställningar som ska användas för att använda en proxytjänst. Med proxytjänsten kan du läsa in resurser som finns på domäner som inte har CORS aktiverat.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [OgcMapLayer (OgcMapLayer)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Se följande artiklar, som innehåller kodexempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Ansluta till en WFS-tjänst](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Utnyttja kärnverksamheten](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Information om dataformat som stöds](spatial-io-supported-data-format-details.md)
