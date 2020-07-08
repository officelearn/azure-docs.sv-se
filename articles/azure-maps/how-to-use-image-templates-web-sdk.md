---
title: Bildmallar i Azure Maps Web SDK | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du använder bildmallar med HTML-markörer och olika lager i Microsoft Azure Maps-webbsdk.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 28fee67ccfc1e67d89d0151c8e14bd7c0b688749
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85207104"
---
# <a name="how-to-use-image-templates"></a>Så här använder du avbildningsmallar

Avbildningar kan användas med HTML-markörer och olika skikt i Azure Maps Web SDK:

 - Symbol lager kan återge punkter på kartan med en bild ikon. Symboler kan också återges längs en linje Sök väg.
 - Polygon-lager kan återges med en fyllnings mönster bild. 
 - HTML-markörer kan återge punkter med hjälp av bilder och andra HTML-element.

För att säkerställa bästa prestanda med lager läser du in avbildningarna i bild Sprite-resursen för avbildningar före åter givning. [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions), i SymbolLayer, förinstallerar ett par markör bilder i en fåtal med färgerna i kart bilden Sprite som standard. Dessa markör bilder och fler är tillgängliga som SVG-mallar. De kan användas för att skapa bilder med anpassade skalor eller som en primär och sekundär-färg för kunden. Det finns totalt 42 bildmallar har angetts: 27 symbol ikoner och 15 polygon fyllnings mönster.

Bildmallar kan läggas till i kart avbildningen Sprite-resurser med hjälp av `map.imageSprite.createFromTemplate` funktionen. Den här funktionen tillåter att upp till fem parametrar skickas in.

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

`id`Är en unik identifierare som du skapar. `id`Är tilldelad till bilden när den läggs till i Maps-bilden Sprite. Använd den här identifieraren i skikten för att ange vilken bild resurs som ska renderas. `templateName`Anger vilken avbildnings mal len som ska användas. `color`Alternativet anger bildens primära färg och `secondaryColor` alternativen anger bildens sekundära färg. `scale`Alternativet skalar bildmallen innan den tillämpas på bilden Sprite. När avbildningen tillämpas på bilden Sprite, konverteras den till en PNG. För att säkerställa en tydlig åter givning är det bättre att skala upp bildmallen innan du lägger till den i spriten, än att skala den i ett lager.

Den här funktionen läser in bilden asynkront i bilden Sprite. Därför returneras ett löfte som du kan vänta på att den här funktionen slutförs.

Följande kod visar hur du skapar en avbildning från en av de inbyggda mallarna och använder den med ett symbol lager.

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>Använda en bildmall med ett symbol lager

När en bildmall har lästs in i kart bilden Sprite, kan den återges som en symbol i ett symbol lager genom att referera till bild resurs-ID: t i `image` alternativet för `iconOptions` .

I följande exempel återges ett symbol lager med hjälp av `marker-flat` bild mal len med en primär blågrön-färg och en vit sekundär färg. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Symbol lager med inbyggd ikon mall" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Penn <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>symbol lagret med den inbyggda Icon-mallen</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Använda en bildmall längs en linje Sök väg

När en bildmall har lästs in i kart bilden Sprite, kan den återges längs sökvägen för en linje genom att lägga till en lin Est ring till en data källa och använda ett symbol lager med ett `lineSpacing` alternativ och genom att referera till bild resursens ID i `image` alternativet för `iconOptions` . 

Följande exempel återger en rosa linje på kartan och använder ett symbol lager med hjälp av `car` bild mal len med en blå klarblå blå primär färg och en vit sekundär färg. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linje skikt med inbyggd ikon mall" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Penn <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>linje lagret med den inbyggda Icon-mallen</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Om bild mal len pekar uppåt ställer du in `rotation` ikon alternativet för symbol skiktet på 90 om du vill att det ska peka i samma riktning som linjen.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Använda en bildmall med ett polygon-lager

När en bildmall har lästs in i kart bilden Sprite, kan den återges som ett fyllnings mönster i ett polygon lager genom att referera till bild resurs-ID: t i `fillPattern` lagrets alternativ.

I följande exempel återges ett polygon-lager med hjälp av `dot` bild mal len med en röd primär färg och en transparent sekundär färg.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Fyll polygon med inbyggd ikon mall" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Penn <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>Fyllnings polygonen med den inbyggda Icon-mallen</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Om du anger en sekundär färg för fyllnings mönster blir det enklare att se den underliggande kartan men det primära mönstret kommer fortfarande att visas. 

## <a name="use-an-image-template-with-an-html-marker"></a>Använda en bildmall med en HTML-markör

En bildmall kan hämtas med hjälp av `altas.getImageTemplate` funktionen och används som innehåll i en HTML-markör. Mallen kan skickas till `htmlContent` alternativ för markören och sedan anpassas med `color` `secondaryColor` alternativen, och `text` .

I följande exempel används `marker-arrow` mallen med en röd primär färg, en rosa sekundär färg och ett text värde på 00.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="HTML-markör med inbyggd ikon mall" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se den pennan <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>-HTML-markören med inbyggd Icon-mall</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Bildmallar kan användas utanför kartan. GetImageTemplate-funciton returnerar en SVG-sträng med plats hållare; `{color}`, `{secondaryColor}`, `{scale}`, `{text}`. Ersätt dessa plats hållarnas värden för att skapa en giltig SVG-sträng. Du kan sedan antingen lägga till SVG-strängen direkt i HTML DOM eller konvertera den till en data-URI och infoga den i en bildtagg. Ett exempel:
> ```JavaScript
> //Retrieve an SVG template and replace the placeholder values.
> var svg = atlas.getImageTemplate('marker').replace(/{color}/, 'red').replace(/{secondaryColor}/, 'white').replace(/{text}/, '').replace(/{scale}/, 1);
>
> //Convert to data URI for use in image tags.
> var dataUri = 'data:image/svg+xml;base64,' + btoa(svg);
> ```

## <a name="create-custom-reusable-templates"></a>Skapa anpassade åter användnings bara mallar

Om programmet använder samma ikon med olika ikoner eller om du skapar en modul som lägger till ytterligare bildmallar, kan du enkelt lägga till och hämta dessa ikoner från Azure Maps Web SDK. Använd följande statiska funktioner i `atlas` namn området.

| Name | Returtyp | Beskrivning | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Lägger till en anpassad SVG-bildmall i Atlas-namnområdet. |
|  `getImageTemplate(templateName: string, scale?: number)`| sträng | Hämtar en SVG-mall efter namn. |
| `getAllImageTemplateNames()` | sträng [] |  Hämtar en SVG-mall efter namn. |

SVG-bildmallar stöder följande plats hållarnas värden:

| Platshållare | Beskrivning |
|-|-|
| `{color}` | Den primära färgen. | 
| `{secondaryColor}` | Den sekundära färgen. | 
| `{scale}` | SVG-bilden konverteras till en PNG-bild när den läggs till i kart bilden Sprite. Den här plats hållaren kan användas för att skala en mall innan den konverteras så att den återges tydligt. | 
| `{text}` | Den plats där texten ska renderas när den används med en HTML-markör. |

I följande exempel visas hur du tar en SVG-mall och lägger till den i Azure Maps Web SDK som en återanvändbar ikon-mall. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Lägg till mall för anpassad ikon i Atlas-namnrymd" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>mallen Lägg till anpassad ikon i Atlas-namnrymden</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Lista över bildmallar

I den här tabellen visas alla bildmallar som är tillgängliga i Azure Maps Web SDK. Mallnamnet är ovanför varje bild. Som standard är den primära färgen blå och den sekundära färgen är vit. Om du vill göra den sekundära färgen lättare att se på en vit bakgrund, har följande bilder den sekundära färg uppsättningen till svart.

**Mallar för symbol ikon**

|||||
|:-:|:-:|:-:|:-:|
| vänsterindrag | markör-tjock | markör-cirkel | markör-Flat |
|![markör ikon](./media/image-templates/marker.png)|![markör-tjock ikon](./media/image-templates/marker-thick.png)|![markör-cirkel-ikon](./media/image-templates/marker-circle.png)|![markör-Flat ikon](./media/image-templates/marker-flat.png)|
||||
| markör-fyrkant | markör-kvadratiskt-kluster | markör-pil | markör-boll-PIN-kod | 
|![markör-kvadratisk ikon](./media/image-templates/marker-square.png)|![markör-kvadratisk-kluster ikon](./media/image-templates/marker-square-cluster.png)|![markör-pil-ikon](./media/image-templates/marker-arrow.png)|![ikon för markör-boll-fäst punkt](./media/image-templates/marker-ball-pin.png)|
||||
| markör-kvadratiskt rundad | markör-kvadratiskt avrundat kluster | flag | flagga – triangel |
| ![markör – fyrkantigt rundad ikon](./media/image-templates/marker-square-rounded.png) | ![markör-kvadratiskt avrundat kluster ikon](./media/image-templates/marker-square-rounded-cluster.png) | ![flagg ikon](./media/image-templates/flag.png) | ![flagga – triangel-ikon](./media/image-templates/flag-triangle.png) |
||||
| triangel | triangel-tjock | triangel-pil upp | triangel-pil-vänster |
| ![triangel-ikon](./media/image-templates/triangle.png) | ![triangel-tjock ikon](./media/image-templates/triangle-thick.png) | ![triangel-pil upp-ikon](./media/image-templates/triangle-arrow-up.png) | ![triangel-pil-vänster-ikon](./media/image-templates/triangle-arrow-left.png) |
||||
| sexhörning | sexhörning – tjock | sexhörning, rundad | sexhörning – rundad – tjock |
| ![sexhörnings ikon](./media/image-templates/hexagon.png) | ![sexhörning – tjock ikon](./media/image-templates/hexagon-thick.png) | ![sexhörning, rundad ikon](./media/image-templates/hexagon-rounded.png) | ![sexhörning – rundad – tjock ikon](./media/image-templates/hexagon-rounded-thick.png) |
||||
| fästa | PIN-Round | rundad ruta | rundad – fyrkant – tjock |
| ![fästikon](./media/image-templates/pin.png) | ![PIN-rundad ikon](./media/image-templates/pin-round.png) | ![rundad kvadratisk ikon](./media/image-templates/rounded-square.png) | ![rundad fyrkant – tjock ikon](./media/image-templates/rounded-square-thick.png) |
||||
| pil upp | pil upp-tunn | car ||
| ![pil upp-ikonen](./media/image-templates/arrow-up.png) | ![pil upp-tunn ikon](./media/image-templates/arrow-up-thin.png) | ![bil-ikon](./media/image-templates/car.png) | |

**Mönster för fyllnings mönster för polygon**

|||||
|:-:|:-:|:-:|:-:|
| lera | rotations kontroll | cirkulär | cirklar-avstånd |
| ![kontroll ikon](./media/image-templates/checker.png) | ![rotations ikon för kontroll](./media/image-templates/checker-rotated.png) | ![ikon för cirklar](./media/image-templates/circles.png) | ![ikonen cirklar – utrymmes ikon](./media/image-templates/circles-spaced.png) |
|||||
| diagonalt – linjer upp | diagonalt – linjer ned | diagonalt – ränder-upp | diagonalt – rand nedåt |
| ![diagonalt – linjer upp-ikon](./media/image-templates/diagonal-lines-up.png) | ![Diagonal – streck nedåt-ikon](./media/image-templates/diagonal-lines-down.png) | ![Diagonal – ränder-up-ikon](./media/image-templates/diagonal-stripes-up.png) | ![Diagonal – rand ned-ikon](./media/image-templates/diagonal-stripes-down.png) |
|||||
| rutnät-linjer | roterat-rutnät-linjer | roterat rutnät-ränder | x-fyllning |
| ![rutnät-linje ikon](./media/image-templates/grid-lines.png) | ![ikonen roterat rutnät-linjer](./media/image-templates/rotated-grid-lines.png) | ![ikon för roterat rutnät-ränder](./media/image-templates/rotated-grid-stripes.png) | ![x-fyllnings ikon](./media/image-templates/x-fill.png) |
|||||
| Sick –-Sack | Sick –-Sack-lodrätt | bild |  |
| ![Sick--Sack-ikon](./media/image-templates/zig-zag.png) | ![Sick--Sack-lodrät ikon](./media/image-templates/zig-zag-vertical.png) | ![ikon för punkter](./media/image-templates/dots.png) | |

**Förinstallerade bild ikoner**

Kartan förinstallerar en uppsättning ikoner i Maps-bildspriten med hjälp av `marker` -, `pin` -och- `pin-round` mallarna. Dessa ikon namn och deras färg värden visas i tabellen nedan.

| ikon namn | color | secondaryColor |
|-----------|-------|----------------|
| `marker-black` | `#231f20` | `#ffffff` |
| `marker-blue` | `#1a73aa` | `#ffffff` |
| `marker-darkblue` | `#003963` | `#ffffff` |
| `marker-red` | `#ef4c4c` | `#ffffff` |
| `marker-yellow` | `#f2c851` | `#ffffff` |
| `pin-blue` | `#2072b8` | `#ffffff` |
| `pin-darkblue` | `#003963` | `#ffffff` |
| `pin-red` | `#ef4c4c` | `#ffffff` |
| `pin-round-blue` | `#2072b8` | `#ffffff` |
| `pin-round-darkblue` | `#003963` | `#ffffff` |
| `pin-round-red` | `#ef4c4c` | `#ffffff` |


## <a name="try-it-now-tool"></a>Prova nu-verktyget

Med följande verktyg kan du återge de olika inbyggda Bildmallarna på olika sätt och anpassa de primära och sekundära färgerna och skalan.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Alternativ för ikonnamn" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se mall- <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>alternativen</a> för Penn ikonen efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [Atlas-namnrymd](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

I följande artiklar finns fler kod exempel där du kan använda bildmallar:

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](map-add-shape.md)

> [!div class="nextstepaction"]
> [Lägg till HTML-tillverkare](map-add-bubble-layer.md)
