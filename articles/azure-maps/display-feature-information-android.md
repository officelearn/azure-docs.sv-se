---
title: Visa funktions information i Azure Maps Android SDK | Microsoft Azure Maps
description: Lär dig hur du visar information när användare interagerar med kart funktioner. Använd Azure Maps Android SDK för att visa popup-meddelanden och andra typer av meddelanden.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: fabb4cd1e555a7a67a53bf2f5a99d93c87df436c
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532813"
---
# <a name="display-feature-information"></a>Visa funktionsinformation

Spatialdata representeras ofta med punkter, linjer och polygoner. Informationen innehåller ofta information om metadata. En punkt kan till exempel representera platsen för en butik och metadata om att restaurangens namn, adress och typ av livsmedel fungerar. Du kan lägga till dessa metadata som egenskaper för dessa funktioner med hjälp av en `JsonObject` . Följande kod skapar en enkel punkt funktion med en `title` egenskap som har värdet "Hello World!"

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

När en användare interagerar med en funktion på kartan kan händelser användas för att reagera på dessa åtgärder. Ett vanligt scenario är att visa ett meddelande om metadata-egenskaperna för en funktion som användaren interagerar med. `OnFeatureClick`Händelsen är den huvudsakliga händelsen som används för att identifiera när användaren tryckte på en funktion på kartan. Det finns också en `OnLongFeatureClick` händelse. När du lägger till `OnFeatureClick` händelsen i kartan kan den begränsas till ett enda lager genom att skicka i ID: t för ett lager för att begränsa det till. Om inget lager-ID skickas i, kan du trycka på en funktion på kartan, oavsett vilket lager det finns i, så att den här händelsen utlöses. Följande kod skapar ett symbol lager för att återge punkt data på kartan och lägger sedan till en `OnFeatureClick` händelse och begränsar den till det här symbol skiktet.

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

Ett popup-meddelande är ett av de enklaste sätten att visa information till användaren och är tillgänglig i alla versioner av Android. Den har inte stöd för någon typ av användarindata och visas bara under en kort tids period. Om du snabbt vill låta användaren veta vad de tryckte på, kan ett popup-meddelande vara ett bra alternativ. Följande kod visar hur ett popup-meddelande kan användas med `OnFeatureClick` händelsen.

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

![Animering av en funktion som knackas och ett popup-meddelande visas](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)

Förutom popup-meddelanden finns det många andra sätt att presentera egenskaperna för metadata för en funktion, till exempel:

- [Snakbar-widget](https://developer.android.com/training/snackbar/showing.html) – snackbars ger lätt feedback om en åtgärd. De visar ett kort meddelande längst ned på skärmen på mobilt och lägre till större enheter. Snackbars visas ovanför alla andra element på skärmen och bara en kan visas i taget.
- [Dialog rutor](https://developer.android.com/guide/topics/ui/dialogs) – en dialog ruta är ett litet fönster som efterfrågar användaren att fatta ett beslut eller ange ytterligare information. En dialog ruta fyller inte på skärmen och används vanligt vis för modala händelser som kräver att användarna vidtar en åtgärd innan de kan fortsätta.
- Lägg till ett [fragment](https://developer.android.com/guide/components/fragments) i den aktuella aktiviteten.
- Navigera till en annan aktivitet eller vy.

## <a name="next-steps"></a>Nästa steg

Lägga till mer data i kartan:

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Lägga till former i en Android-karta](how-to-add-shapes-to-android-map.md)
