---
title: Ansluta till en WFS-tjänst (Web Feature Service) | Microsoft Azure Maps
description: Lär dig hur du ansluter till en WFS-tjänst och frågar sedan WFS-tjänsten med Azure Maps web SDK och spatial IO-modulen.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8b511395eb61e8845aaa11e5ca7a490dc461424d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334209"
---
# <a name="connect-to-a-wfs-service"></a>Ansluta till en WFS-tjänst

WFS (Web Feature Service) är en webbtjänst för att fråga rumsliga data som har ett standardiserat API som definieras av OGC (Open Geospatial Consortium). Klassen `WfsClient` i den rumsliga IO-modulen gör att utvecklare kan ansluta till en WFS-tjänst och fråga data från tjänsten.

Följande funktioner stöds av `WfsClient` klassen:

- Versioner som `1.0.0` `1.1.0`stöds: , och`2.0.0`
- Filteroperatorer som stöds: binära jämförelser, logik, matematik, värde och `bbox`.
- Förfrågningar görs `HTTP GET` endast med hjälp av.
- Åtgärder som stöds:

    | | |
    | :-- | :-- |
    | GetCapabilities | Genererar ett metadatadokument med giltiga WFS-åtgärder och parametrar |
    | FåFeature | Returnerar ett urval av funktioner från en datakälla |
    | BeskrivFeatureType | Returnerar de funktionstyper som stöds |

## <a name="using-the-wfs-client"></a>Använda WFS-klienten

Klassen `atlas.io.ogc.WfsClient` i den rumsliga IO-modulen gör det enkelt att fråga en WFS-tjänst och konvertera svaren till GeoJSON-objekt. Detta GeoJSON-objekt kan sedan användas för andra mappningsändamål.

Följande kod frågar en WFS-tjänst och återger de returnerade funktionerna på kartan.

<br/>

<iframe height='700' scrolling='no' title='Enkelt WFS-exempel' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se exemplet med <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>Penn simple WFS</a> från Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Filter som stöds

Specifikationen för WFS-standarden använder sig av OGC-filter. Filtren nedan stöds av WFS-klienten, förutsatt att tjänsten som anropas också stöder dessa filter. Anpassade filtersträngar kan skickas `CustomFilter` till klassen.

**Logiska operatorer**

- `And`
- `Or`
- `Not`

**Värdeoperatorer**

- `GmlObjectId`
- `ResourceId`

**Matematiska operatorer**

- `Add`
- `Sub`
- `Mul`
- `Div`

**Jämförelseoperatorer**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

Följande kod visar användningen av olika filter med WFS-klienten.

<br/>

<iframe height='500' scrolling='no' title= 'Exempel på WFS-filter' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se exempel på Pen <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>WFS-filter</a> från Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>WFS-tjänstutforskaren

Följande kod använder WFS-klienten för att utforska WFS-tjänster. Välj ett egenskapstypslagret i tjänsten och se den associerade förklaringen.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'WFS-tjänstutforskaren' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Pen <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS-tjänstutforskaren</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

För att komma åt WFS-tjänster som finns på icke-CORS-aktiverade slutpunkter `proxyService` kan en CORS-aktiverad proxytjänst skickas till alternativet för WFS-klienten enligt nedan. 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [WfsClient (WfsClient)](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Se följande artiklar för fler kodexempel att lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Utnyttja kärnverksamheten](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Information om dataformat som stöds](spatial-io-supported-data-format-details.md)
