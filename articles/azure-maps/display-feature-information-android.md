---
title: Visa funktionsinformation i Azure Maps Android SDK | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du visar funktionsinformation på en karta med Hjälp av Microsoft Azure Maps Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 26f41a7fd88a3c2018592e89ae95e3b962c1a9e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911698"
---
# <a name="display-feature-information"></a>Visa funktionsinformation

Rumsliga data representeras ofta med hjälp av punkter, linjer och polygoner. Dessa data har ofta metadatainformation som är associerad med dem. En punkt kan till exempel representera platsen för en butik och metadata om den restaurangen kan vara dess namn, adress och typ av mat som den serverar. Dessa metadata kan läggas till som `JsonObject`egenskaper för dessa funktioner med hjälp av en . Följande kod skapar en enkel punkt `title` funktion med en egenskap som har ett värde av "Hello World!"

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Create a point feature with some properties and add it to the data source.
JsonObject properties = new JsonObject();
properties.addProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64), properties));
```

När en användare interagerar med en funktion på kartan kan händelser användas för att reagera på dessa åtgärder. Ett vanligt scenario är att visa ett meddelande som gjorts av metadataegenskaperna för en funktion som användaren interagerade med. Händelsen `OnFeatureClick` är den viktigaste händelsen som används för att identifiera när användaren knackade på en funktion på kartan. Det finns också `OnLongFeatureClick` en händelse. När du `OnFeatureClick` lägger till händelsen på kartan kan den begränsas till ett enda lager genom att skicka i ID:t för ett lager för att begränsa den till. Om inget lager-ID skickas in skulle det avfyra händelsen om du trycker på valfri funktion på kartan, oavsett vilket lager den finns i. Följande kod skapar ett symbollager för att återge punktdata på kartan, lägger sedan till en `OnFeatureClick` händelse och begränsar den till det här symbollagret.

```java
//Create a symbol and add it to the map.
SymbolLayer symbolLayer = new SymbolLayer(dataSource);
map.layers.add(symbolLayer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).properties().get("title").getAsString();

    //Do something with the message.
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>Visa ett popup-meddelande

Ett popup-meddelande är ett av de enklaste sätten att visa information för användaren och är tillgängligt i alla versioner av Android. Det stöder inte någon typ av användarindata och visas bara under en kort tid. Om du snabbt vill låta användaren veta något om vad de knackade på, kan ett popup-meddelande vara ett bra alternativ. Följande kod visar hur ett popup-meddelande `OnFeatureClick` kan användas med händelsen.

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

<center>

![Animering av en funktion som trycks och ett popup-meddelande visas](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

Förutom popup-meddelanden finns det många andra sätt att presentera metadataegenskaperna för en funktion, till exempel:

- [Snakbar widget](https://developer.android.com/training/snackbar/showing.html) - Snackbars ger lätt feedback om en operation. De visar ett kort meddelande längst ner på skärmen på mobilen och nere till vänster på större enheter. Snackbars visas framför alla andra element på skärmen och endast en kan visas åt gången.
- [Dialogrutor](https://developer.android.com/guide/topics/ui/dialogs) - En dialogruta är ett litet fönster som uppmanar användaren att fatta ett beslut eller ange ytterligare information. En dialogruta fyller inte skärmen och används normalt för modala händelser som kräver att användarna vidtar en åtgärd innan de kan fortsätta.
- Lägg till ett [fragment](https://developer.android.com/guide/components/fragments) i den aktuella aktiviteten.
- Navigera till en annan aktivitet eller vy.

## <a name="next-steps"></a>Nästa steg

Så här lägger du till mer data på kartan:

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Lägga till former på en Android-karta](how-to-add-shapes-to-android-map.md)
