---
title: Ansluta till en tjänst för webb funktions tjänsten (WFS) | Microsoft Azure Maps
description: Lär dig hur du ansluter till en WFS-tjänst och frågar WFS-tjänsten med hjälp av Azure Maps Web SDK och den spatiala IO-modulen.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 18ac583837c7cb8b2dabbfa6f7d7210c8afe3fcb
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402766"
---
# <a name="connect-to-a-wfs-service"></a>Ansluta till en WFS-tjänst

En webb funktions tjänst (WFS) är en webb tjänst för att fråga spatialdata som har ett standardiserat API, som har definierats av Open Geospatial Consortium (OGC). `WfsClient`-klassen i den spatiala IO-modulen gör det möjligt för utvecklare att ansluta till en WFS-tjänst och fråga efter data från tjänsten.

Följande funktioner stöds av klassen `WfsClient`:

- Versioner som stöds: `1.0.0`, `1.1.0`och `2.0.0`
- Filter operatorer som stöds: binära jämförelser, logik, matematik, värde och `bbox`.
- Begär Anden görs endast med `HTTP GET`.
- Åtgärder som stöds:

    | | |
    | :-- | :-- |
    | GetCapabilities | Genererar ett Metadatadokumentet med giltiga WFS-åtgärder och-parametrar |
    | GetFeature | Returnerar ett urval av funktioner från en data Källa |
    | DescribeFeatureType | Returnerar de funktions typer som stöds |

## <a name="using-the-wfs-client"></a>Använda WFS-klienten

`atlas.io.ogc.WfsClient`-klassen i den spatiala i/o-modulen gör det enkelt att fråga en WFS-tjänst och konvertera Svaren till netjson-objekt. Detta interjson-objekt kan sedan användas för andra mappnings syfte.

Följande kod frågar en WFS-tjänst och återger de returnerade funktionerna på kartan.

<br/>

<iframe height='700' scrolling='no' title='Enkelt WFS-exempel' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se det <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>enkla WFS-exemplet</a> genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Filter som stöds

Specifikationen för WFS-standarden använder OGC-filter. Filtren nedan stöds av WFS-klienten, förutsatt att den tjänst som anropas också stöder dessa filter. Anpassade filter strängar kan skickas till `CustomFilter`-klassen.

**Logiska operatorer**

- `And`
- `Or`
- `Not`

**Värde operatorer**

- `GmlObjectId`
- `ResourceId`

**Matematik operatorer**

- `Add`
- `Sub`
- `Mul`
- `Div`

**Jämförelse operatorer**

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

Följande kod visar hur du använder olika filter med WFS-klienten.

<br/>

<iframe height='500' scrolling='no' title= 'WFS filter exempel' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>exemplen för WFS filter</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>WFS service Explorer

I följande kod används WFS-klienten för att utforska WFS Services. Välj ett egenskaps typ lager i tjänsten och se den associerade förklaringen.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'WFS service Explorer' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>service Explorer</a> för pen-WFS (@azuremaps) genom att Azure Maps (<a href='https://codepen.io/azuremaps'> </a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Du kan också använda en proxyserver för att läsa in resurser som finns i domäner som inte är CORs-aktiverade. Du måste först definiera en variabel som ska innehålla proxy-tjänstens URL och ange alternativet `proxyService` för WFS-klienten. Om du vill rendera ett alternativ för proxy-tjänsten för användaren lägger du till en användarindata i användar gränssnittet. Läs in tjänstens URL när du klickar på den. Följande kodfragment visar hur du använder proxy-tjänsten.

```JavaScript

//A variable to hold the URL of the proxy service
var proxyServiceUrl = window.location.origin + 'CorsEnabledProxyService.ashx?url=';

//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: (document.getElementById('useProxyService').checked) ? proxyServiceUrl : null
});

function proxyOptionChanged() {
    if (currentServiceUrl) {
        loadClient(currentServiceUrl);
    }
}

```

HTML-kodfragmentet nedan motsvarar JavaScript-koden ovan:

```html
<!-- use the proxy service -->
<input id="useProxyService" type="checkbox" onclick="proxyOptionChanged()"/>
```

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [WfsClient](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Utnyttja kärn åtgärder](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Information om data format som stöds](spatial-io-supported-data-format-details.md)
