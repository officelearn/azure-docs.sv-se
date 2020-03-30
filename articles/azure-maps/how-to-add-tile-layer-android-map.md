---
title: Lägg till ett panellager i Android-kartor | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du återger ett panellager på en karta med Hjälp av Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: f98598bd1307bb1b46ff23814780c5f809b9ac90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335566"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Lägga till ett panellager på en karta med Azure Maps Android SDK

Den här artikeln visar hur du renderar ett panellager på en karta med Hjälp av Azure Maps Android SDK. Med panellager kan du lägga över avbildningar ovanpå Azure Maps-baskartpaneler. Mer information om Azure Maps plattsättningssystem finns i [dokumentationen för zoomningsnivåer och panelrutnät.](zoom-levels-and-tile-grid.md)

Ett panellager läses in i paneler från en server. Dessa bilder kan förrenas och lagras som alla andra bilder på en server, med hjälp av en namngivningskonvention som panellagret förstår. Eller så kan dessa bilder återges med en dynamisk tjänst som genererar bilderna nära realtid. Det finns tre olika namngivningskonventioner för paneltjänster som stöds av klassen Azure Maps TileLayer:

* X, Y, Zoom notation - Baserat på zoomnivån är x kolumnen och y är radens placering av panelen i panelrutnätet.
* Quadkey notation - Kombination x, y, zooma information till ett enda strängvärde som är en unik identifierare för en panel.
* Begränsningsram - Begränsningsramkoordinater kan användas för `{west},{south},{east},{north}` att ange en bild i det format som ofta används av [WMS (Web Mapping Services).](https://www.opengeospatial.org/standards/wms)

> [!TIP]
> En TileLayer är ett bra sätt att visualisera stora datauppsättningar på kartan. Inte bara kan ett panellager genereras från en bild, men vektordata kan också återges som ett panellager också. Genom att återge vektordata som ett panellager behöver kartkontrollen bara läsa in panelerna som kan vara mycket mindre i filstorlek än de vektordata de representerar. Denna teknik används av många som behöver göra miljontals rader med data på kartan.

Panel-URL:en som skickas till ett panellager måste vara en http/https-URL till en TileJSON-resurs eller en URL-mall för panelen som använder följande parametrar: 

* `{x}`- X-placering på plattan. Också `{y}` behov `{z}`och .
* `{y}`- Y-position på plattan. Också `{x}` behov `{z}`och .
* `{z}`- Zooma nivå av kakel. Också `{x}` behov `{y}`och .
* `{quadkey}`- Tile quadkey-identifierare baserat på namngivningskonventionen för Bing Maps-panelsystemet.
* `{bbox-epsg-3857}`- En markeringsramssträng `{west},{south},{east},{north}` med formatet i EPSG 3857 Spatial Reference System.
* `{subdomain}`- En platshållare för underdomänvärdena, om underdomänvärdet anges.

## <a name="prerequisites"></a>Krav

För att slutföra processen i den här artikeln måste du installera [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) för att läsa in en karta.


## <a name="add-a-tile-layer-to-the-map"></a>Lägga till ett panellager på kartan

 Det här exemplet visar hur du skapar ett panellager som pekar på en uppsättning paneler. Dessa plattor använder "x, y, zoom" plattsättningssystem. Källan till detta kakel lager är ett väder radar overlay från [Iowa Environmental Mesonet i Iowa State University](https://mesonet.agron.iastate.edu/ogc/). 

Du kan lägga till ett panellager på kartan genom att följa stegen nedan.

1. Redigera **res > layout > activity_main.xml** så det ser ut som en nedan:

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

2. Kopiera följande kodavsnitt nedan till metoden **onCreate()** för klassen. `MainActivity.java`

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
    
    Kodavsnittet ovan hämtar först en Azure Maps-kartkontrollinstans med hjälp av motringningsmetoden **onReady().** Det skapar sedan `TileLayer` ett objekt och skickar en formaterad `tileUrl` **xyz** kakel URL till alternativet. Lagrets opacitet är inställd `0.8` på och eftersom panelerna från paneltjänsten som används är 256 bildpunkter skickas den `tileSize` här informationen in i alternativet. Bricklagret skickas sedan in i lagerhanteraren för kartor.

    När du har lagt till `MainActivity.java` kodavsnittet ovan bör du se ut som det nedan:
    
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

Om du kör ditt program nu bör du se en rad på kartan som visas nedan:

<center>

![Android kartlinje](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>Nästa steg

Se följande artikel om du vill veta mer om olika sätt att ange kartformat

> [!div class="nextstepaction"]
> [Ändra kartstilar i Android-kartor](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)