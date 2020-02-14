---
title: Lägg till ett panel lager till Android Maps | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du återger ett panel lager på en karta med hjälp av Microsoft Azure Maps Android SDK.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4113f632e70bf1008c688066b51a27f1bc3c6345
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198266"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Lägg till ett panel lager till en karta med Azure Maps Android SDK

Den här artikeln visar hur du återger ett panel lager på en karta med hjälp av Azure Maps Android SDK. Med panel lager kan du placera bilder ovanpå Azure Maps bas kart paneler. Mer information om Azure Maps displacerings systemet finns i dokumentationen för [zoomnings nivåer och Brick rutnät](zoom-levels-and-tile-grid.md) .

Ett panel lager läses in i paneler från en server. Dessa bilder kan förrenderas och lagras på samma sätt som andra bilder på en server med hjälp av en namngivnings konvention som panel lagret förstår. Eller så kan de här avbildningarna återges med en dynamisk tjänst som genererar avbildningarna nära real tid. Det finns tre olika namngivnings konventioner för panel tjänster som stöds av Azure Maps TileLayer-klassen:

* X-, Y-, zoomnings-och zoomnings nivå, x är kolumnen och Y är panelens rad position i panel rutnätet.
* Quadkey notation – kombination x, y, zoomnings information till ett enda sträng värde som är en unik identifierare för en panel.
* Koordinater för avgränsnings rutor kan användas för att ange en bild i formatet `{west},{south},{east},{north}` som ofta används av [webb mappnings tjänster (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> En TileLayer är ett bra sätt att visualisera stora data uppsättningar på kartan. Ett panel lager kan inte bara genereras från en bild, men vektor data kan även återges som ett panel lager. Genom att återge vektor data som ett panel lager behöver kart kontrollen bara läsa in panelerna som kan vara mycket mindre i fil storlek än de vektor data de representerar. Den här tekniken används av många som behöver rendera miljon tals rader med data på kartan.

Panel-URL: en som skickas till ett panel lager måste vara en HTTP/HTTPS-URL till en TileJSON-resurs eller en panel-URL-mall som använder följande parametrar: 

* panelens `{x}`-X-position. Behöver också `{y}` och `{z}`.
* panelens `{y}`-Y-position. Behöver också `{x}` och `{z}`.
* panelens `{z}` zoomnings nivå. Behöver också `{x}` och `{y}`.
* quadkey-identifierare för `{quadkey}` panel baserat på Bing Maps-panelens system namngivnings konvention.
* `{bbox-epsg-3857}`-en sträng med en avgränsnings ruta med formatet `{west},{south},{east},{north}` i rums referens systemet EPSG 3857.
* `{subdomain}` – en plats hållare för under domänens värden, om värdet under domän är angivet.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra processen i den här artikeln måste du installera [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) för att läsa in en karta.


## <a name="add-a-tile-layer-to-the-map"></a>Lägg till ett panel lager på kartan

 Det här exemplet visar hur du skapar ett panel lager som pekar på en uppsättning paneler. Dessa paneler använder systemet "x, y, zoom". Källan till det här panel lagret är ett väderleks överlägg av [Iowa-miljön för Mesonet i Iowa State University](https://mesonet.agron.iastate.edu/ogc/). 

Du kan lägga till ett panel lager till kartan genom att följa stegen nedan.

1. Redigera **res > layout > activity_main. XML** så att det ser ut som på bilden nedan:

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >
    
        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:mapcontrol_centerLat="40.75"
            app:mapcontrol_centerLng="-99.47"
            app:mapcontrol_zoom="3"
            />
    
    </FrameLayout>
    ```

2. Kopiera följande kodfragment till metoden **onCreate ()** i `MainActivity.java`-klassen.

    ```Java
    mapControl.onReady(map -> {
        //Add a tile layer to the map, below the map labels.
        map.layers.add(new TileLayer(
            tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
            opacity(0.8f),
            tileSize(256)
        ), "labels");
    });
    ```
    
    Kodfragmentet ovan hämtar först en Azure Maps kart kontroll instans med återanrops metoden **onReady ()** . Sedan skapas ett `TileLayer`-objekt och en formaterad **XYZ** -panel skickas till `tileUrl` alternativet. Lager opaciteten är inställt på `0.8` och eftersom panelerna från panel tjänsten används är 256 pixel paneler överförs den här informationen till alternativet `tileSize`. Panel lagret skickas sedan till Maps Layer Manager.

    När du har lagt till kodfragmentet ovan bör `MainActivity.java` se ut så här:
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.TileLayer;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileSize;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileUrl;
        
    public class MainActivity extends AppCompatActivity {
    
        static{
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
    
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {

                //Add a tile layer to the map, below the map labels.
                map.layers.add(new TileLayer(
                    tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
                    opacity(0.8f),
                    tileSize(256)
                ), "labels");
            });    
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }    
    }
    ```

Om du kör programmet nu bör du se en linje på kartan som visas nedan:

<center>

](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center> för ![Android-mappning

## <a name="next-steps"></a>Nästa steg

I följande artikel finns mer information om hur du anger kart format

> [!div class="nextstepaction"]
> [Ändra kart format i Android Maps](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)