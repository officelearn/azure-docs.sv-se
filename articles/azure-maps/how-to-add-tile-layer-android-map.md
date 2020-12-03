---
title: Lägg till ett panel lager till en karta med Azure Maps Android SDK
description: Lär dig hur du lägger till ett panel lager till en karta. Se ett exempel som använder Microsoft Azure mappar Android SDK för att lägga till ett väderleks överlägg i en karta.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 22618a28f1a87e68c19467aedf639e96ec2fb91e
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532684"
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

* `{x}` -X position i rutan. Du måste också ha `{y}` och `{z}` .
* `{y}` -Y position i panelen. Du måste också ha `{x}` och `{z}` .
* `{z}` -Zoomnings nivå i panelen. Du måste också ha `{x}` och `{y}` .
* `{quadkey}` -Panel quadkey identifierare baserat på Bing Maps-panelens system namngivnings konvention.
* `{bbox-epsg-3857}` – En sträng med avgränsnings rutor med formatet `{west},{south},{east},{north}` i EPSG 3857 rums referens system.
* `{subdomain}` – En plats hållare för under domänens värden, om värdet under domän är angivet.

## <a name="prerequisites"></a>Krav

För att slutföra processen i den här artikeln måste du installera [Azure Maps Android SDK](./how-to-use-android-map-control-library.md) för att läsa in en karta.


## <a name="add-a-tile-layer-to-the-map"></a>Lägg till ett panel lager på kartan

 Det här exemplet visar hur du skapar ett panel lager som pekar på en uppsättning paneler. Dessa paneler använder systemet "x, y, zoom". Källan till det här panel lagret är ett väderleks överlägg av [Iowa-miljön för Mesonet i Iowa State University](https://mesonet.agron.iastate.edu/ogc/). 

Du kan lägga till ett panel lager till kartan genom att följa stegen nedan.

1. Redigera **> för res > activity_main.xml** så att det ser ut som på bilden nedan:

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

2. Kopiera följande kodfragment till **onCreate ()-** metoden för `MainActivity.java` klassen.

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
    
    Kodfragmentet ovan hämtar först en Azure Maps kart kontroll instans med återanrops metoden **onReady ()** . Det skapar sedan ett `TileLayer` objekt och skickar en formaterad **XYZ** -URL till `tileUrl` alternativet. Lager opaciteten är inställt på `0.8` och eftersom panelerna från panel tjänsten används är 256 pixel paneler överförs den här informationen till `tileSize` alternativet. Panel lagret skickas sedan till Maps Layer Manager.

    När du har lagt till kodfragmentet ovan `MainActivity.java` bör det se ut som det som visas nedan:
    
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

![Android-kart linje](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>Nästa steg

I följande artikel finns mer information om hur du anger kart format

> [!div class="nextstepaction"]
> [Ändra kart format i Android Maps](./set-android-map-styles.md)