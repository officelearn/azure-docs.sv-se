---
title: Bildmallar i Azure Maps Web SDK | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du använder bildmallar med HTML-markörer och olika lager i Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: ee8e8ee4ca64de0390b6fa34e36fb4d06348a8ac
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804817"
---
# <a name="how-to-use-image-templates"></a>Så här använder du avbildningsmallar

Avbildningar kan användas med HTML-markörer och olika lager i Azure Maps web SDK:

 - Symbollager kan återge punkter på kartan med en bildikon. Symboler kan också återges längs en linjebana.
 - Polygonlager kan återges med en fyllningsmönsterbild. 
 - HTML-markörer kan återge punkter med hjälp av bilder och andra HTML-element.

För att säkerställa god prestanda med lager, ladda bilderna i kartan bild sprite resurs innan rendering. [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions), av SymbolLayer, förladdar ett par markör bilder i en handfull färger i kartan bilden sprite, som standard. Dessa markörbilder med mera finns som SVG-mallar. De kan användas för att skapa bilder med anpassade skalor eller användas som en primär och sekundär sekundär färg för kunden. Totalt finns det 42 bildmallar som: 27 symbolikoner och 15 polygonfyllningsmönster.

Bildmallar kan läggas till i kartbilden sprite resurser med hjälp av `map.imageSprite.createFromTemplate` funktionen. Denna funktion gör att upp till fem parametrar kan skickas in;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

Det `id` är en unik identifierare som du skapar. Den `id` tilldelas bilden när den läggs till i kartorna bilden sprite. Använd den här identifieraren i lagren för att ange vilken bildresurs som ska återges. Anger `templateName` vilken bildmall som ska användas. Alternativet `color` anger bildens primära färg `secondaryColor` och alternativen anger bildens sekundära färg. Alternativet `scale` skalar bildmallen innan du använder den på bildsprit. När bilden används på bilden sprite, konverteras den till en PNG. För att säkerställa skarpa rendering, är det bättre att skala upp bildmallen innan du lägger till den i sprite, än att skala upp den i ett lager.

Den här funktionen läser asynkront in bilden i bildspritet. Således returnerar det ett löfte som du kan vänta på att den här funktionen ska slutföras.

Följande kod visar hur du skapar en bild från en av de inbyggda mallarna och använder den med ett symbollager.

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

## <a name="use-an-image-template-with-a-symbol-layer"></a>Använda en bildmall med ett symbollager

När en bildmall har lästs in i kartbilden kan den återges som en symbol i ett symbollager genom att referera till `image` bildresurs-ID:t i alternativet `iconOptions`.

I följande exempel återges ett `marker-flat` symbollager med hjälp av bildmallen med en kricka primär färg och en vit sekundär färg. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Symbollager med inbyggd ikonmall" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>pennsymbollagret med inbyggd ikonmall</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>från Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Använda en bildmall längs en linjesökväg

När en bildmall har lästs in i kartbilden kan den återges längs banan för en linje genom att lägga `lineSpacing`till en LineString i en datakälla `image` och använda `iconOptions`ett symbollager med ett alternativ och genom att referera till ID:t för bildresursen i alternativet th . 

Följande exempel återger en rosa linje på kartan och `car` använder ett symbollager med hjälp av bildmallen med en dodger blå primär färg och en vit sekundär färg. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linjelager med inbyggd ikonmall" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>Pennlinjens lager med inbyggd ikonmall</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Om bildmallen pekar uppåt `rotation` ställer du in ikonalternativet för symbollagret till 90 om du vill att det ska peka i samma riktning som linjen.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Använda en bildmall med ett polygonlager

När en bildmall har lästs in i kartbilden kan den återges som ett fyllningsmönster i ett polygonlager genom att referera till `fillPattern` bildresurs-ID:t i lagrets alternativ.

Följande exempel återger ett polygonlager med `dot` hjälp av bildmallen med en röd primär färg och en genomskinlig sekundär färg.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Fyll polygon med inbyggd ikonmall" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Pen <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>Fill polygon med inbyggd ikonmall</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Om du ställer in den sekundära färgen på fyllningsmönstren blir det lättare att se den underliggande kartan fortfarande ger det primära mönstret. 

## <a name="use-an-image-template-with-an-html-marker"></a>Använda en bildmall med en HTML-markör

En bildmall kan hämtas `altas.getImageTemplate` med hjälp av funktionen och användas som innehållet i en HTML-markör. Mallen kan skickas `htmlContent` till alternativet för markören och `color` `secondaryColor`sedan `text` anpassas med alternativen , och .

I följande exempel `marker-arrow` används mallen med en röd primär färg, en rosa sekundär färg och ett textvärde på "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="HTML-markör med inbyggd ikonmall" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>html-markör för</a> penna med inbyggd<a href='https://codepen.io/azuremaps'>@azuremaps</a>ikonmall från Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-custom-reusable-templates"></a>Skapa anpassade återanvändbara mallar

Om ditt program använder samma ikon med olika ikoner eller om du skapar en modul som lägger till ytterligare bildmallar kan du enkelt lägga till och hämta dessa ikoner från Azure Maps web SDK. Använd följande statiska `atlas` funktioner på namnområdet.

| Namn | Returtyp | Beskrivning | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Lägger till en anpassad SVG-bildmall i atlasnamnområdet. |
|  `getImageTemplate(templateName: string, scale?: number)`| sträng | Hämtar en SVG-mall efter namn. |
| `getAllImageTemplateNames()` | sträng[] |  Hämtar en SVG-mall efter namn. |

SVG-bildmallar stöder följande platshållarvärden:

| Platshållare | Beskrivning |
|-|-|
| `{color}` | Den primära färgen. | 
| `{secondaryColor}` | Den sekundära färgen. | 
| `{scale}` | SVG-bilden konverteras till en png-bild när den läggs till i kartbilden sprite. Den här platshållaren kan användas för att skala en mall innan den konverteras för att säkerställa att den återges tydligt. | 
| `{text}` | Platsen som text ska återges när den används med en HTML-markör. |

I följande exempel visas hur du tar en SVG-mall och lägger till den i Azure Maps web SDK som en återanvändbar ikonmall. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Lägga till anpassad ikonmall i atlasnamnområdet" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>ikonen Lägg till anpassad ikon i</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>Pen i Atlas namespace by Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Lista över bildmallar

I den här tabellen visas alla bildmallar som för närvarande är tillgängliga i Azure Maps web SDK. Mallnamnet ligger ovanför varje bild. Som standard är den primära färgen blå och den sekundära färgen vit. För att göra den sekundära färgen lättare att se på en vit bakgrund har följande bilder den sekundära färgen inställd på svart.

**Symbolikonmallar**

|||||
|:-:|:-:|:-:|:-:|
| Markör | markör-tjock | markör-cirkel | markör-platt |
|![ikon för markör](./media/image-templates/marker.png)|![markörtjock ikon](./media/image-templates/marker-thick.png)|![ikon för markörcirkel](./media/image-templates/marker-circle.png)|![ikon för markör-platt](./media/image-templates/marker-flat.png)|
||||
| markör-kvadrat | markör-kvadrat-kluster | markör-pil | markör-boll-stift | 
|![ikon för markörruta](./media/image-templates/marker-square.png)|![Ikon för markör-kvadratkluster](./media/image-templates/marker-square-cluster.png)|![ikon för markörpil](./media/image-templates/marker-arrow.png)|![ikon för markör-bollstift](./media/image-templates/marker-ball-pin.png)|
||||
| markör-kvadrat-rundad | markör-kvadrat-rundade-kluster | flag | flagga-triangel |
| ![markör-kvadrat-rundad ikon](./media/image-templates/marker-square-rounded.png) | ![ikon för markör-kvadrat-rundade kluster](./media/image-templates/marker-square-rounded-cluster.png) | ![flagga ikon](./media/image-templates/flag.png) | ![ikon för flagga-triangel](./media/image-templates/flag-triangle.png) |
||||
| triangel | triangel-tjock | triangel-pil-upp | triangel-pil till vänster |
| ![triangelikon](./media/image-templates/triangle.png) | ![triangeltjock ikon](./media/image-templates/triangle-thick.png) | ![Ikon för triangelpil-upp](./media/image-templates/triangle-arrow-up.png) | ![Ikon för triangelpil-vänster](./media/image-templates/triangle-arrow-left.png) |
||||
| Hexagon | hexagon-tjock | hexagon-rundade | hexagon-rundad tjock |
| ![ikonen hexagon](./media/image-templates/hexagon.png) | ![hexagon-tjock ikon](./media/image-templates/hexagon-thick.png) | ![ikon för hexagonrundad](./media/image-templates/hexagon-rounded.png) | ![hexagon-rundad tjock ikon](./media/image-templates/hexagon-rounded-thick.png) |
||||
| fästa | pin-runda | rundad kvadrat | rundad kvadrat-tjock |
| ![fästikon](./media/image-templates/pin.png) | ![Ikon för pin-round](./media/image-templates/pin-round.png) | ![ikon för rundade kvadrater](./media/image-templates/rounded-square.png) | ![avrundad kvadrat tjock ikon](./media/image-templates/rounded-square-thick.png) |
||||
| pil-upp | pil-upp-tunn | car ||
| ![ikon för pil-upp](./media/image-templates/arrow-up.png) | ![ikon för pil-upp-tunn](./media/image-templates/arrow-up-thin.png) | ![bilikon](./media/image-templates/car.png) | |

**Polygonfyllningsmönstermallar**

|||||
|:-:|:-:|:-:|:-:|
| Checker | checker-roterad | Cirklar | cirklar-utspridda |
| ![ikonen checker](./media/image-templates/checker.png) | ![checker-roterad ikon](./media/image-templates/checker-rotated.png) | ![ikon för cirklar](./media/image-templates/circles.png) | ![ikon för cirklar-avstånd](./media/image-templates/circles-spaced.png) |
|||||
| diagonal-linjer-upp | diagonal-linjer nedåt | diagonal-ränder-upp | diagonal-ränder-ner |
| ![ikon för diagonaluppredning](./media/image-templates/diagonal-lines-up.png) | ![ikon för diagonallinjer](./media/image-templates/diagonal-lines-down.png) | ![ikon för diagonalrandig ränder](./media/image-templates/diagonal-stripes-up.png) | ![ikon för diagonalrandiga ränder](./media/image-templates/diagonal-stripes-down.png) |
|||||
| rutnätslinjer | roterade rutnätslinjer | rotat-grid-ränder | x-fyllning |
| ![ikon för rutnätslinjer](./media/image-templates/grid-lines.png) | ![Ikon för roterade rutnätslinjer](./media/image-templates/rotated-grid-lines.png) | ![ikon för rotat-grid-ränder](./media/image-templates/rotated-grid-stripes.png) | ![x-fyll ikon](./media/image-templates/x-fill.png) |
|||||
| zig-zag | zig-zag-vertikal | Prickar |  |
| ![zig-zag-ikon](./media/image-templates/zig-zag.png) | ![zig-zag-vertikal ikon](./media/image-templates/zig-zag-vertical.png) | ![ikonen punkter](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>Prova nu verktyg

Med följande verktyg kan du återge de olika inbyggda bildmallarna på olika sätt och anpassa de primära och sekundära färgerna och skalan.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Alternativ för ikonmall" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>mallalternativen för pennikonen</a> i Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [BildSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [atlas namnområde](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

Se följande artiklar för fler kodexempel där bildmallar kan användas:

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](map-add-shape.md)

> [!div class="nextstepaction"]
> [Lägg till HTML-tillverkare](map-add-bubble-layer.md)
