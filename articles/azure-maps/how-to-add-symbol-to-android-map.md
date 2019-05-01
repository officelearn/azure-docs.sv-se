---
title: Lägg till ett lager för symbolen till Android-kartor i Azure Maps | Microsoft Docs
description: Hur du lägger till symboler på en karta med hjälp av Android SDK för Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: add6e23d023753e217c102dc946837a71a64c781
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64871084"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Lägg till ett lager för symbolen på en karta med hjälp av Android SDK för Azure Maps

Den här artikeln visar hur du kan visa punkt data från en datakälla som ett lager för symbolen på en karta med hjälp av Azure Maps Android SDK.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill följa helt i den här artikeln, måste du installera [Android SDK för Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) att läsa in en karta.

## <a name="add-a-symbol-layer"></a>Lägga till ett symbolskikt

Följ stegen nedan om du vill lägga till en markör på kartan med symbol-lager:

1. Redigera **res** > **layout** > **activity_main.xml** så att det ser ut som följande XML:
    
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
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. Kopiera följande kodavsnitt i den **onCreate()** -metoden för din `MainActivity.java` klass.

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    Kodfragmentet ovan först hämtar en Azure Maps map control instans med hjälp av den **onReady()** motringningsmetoden. Det skapar sedan en datakälla objekt med den **DataSource** klassen och lägger till den på kartan. Den lägger sedan till en **funktionen** som innehåller en punkt geometri till den. En röd markörbildens konfigureras som ikon för symbolen. En **symbol layer** använder text eller ikoner för att rendera platsbaserad data omslutas i datakällan som symbolen på kartan. En symbol layer skapas och datakällan skickas till den att återge, och läggs sedan till den kartskikt.
    
    När du lägger till kodfragmentet ovan, din `MainActivity.java` bör se ut som i exemplet nedan:
    
    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.Feature;
    import com.mapbox.geojson.Point;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
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
    
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
            });
        }
    
        @Override
        public void onStart() {
            super.onStart();
            mapControl.onStart();
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
    
Nu om du kör ditt program bör du se en markör på kartan, som visas här:

<center>

![Android kartan PIN-kod](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>Nästa steg

Om du vill lägga till fler saker till kartan, se:

> [!div class="nextstepaction"]
> [Lägga till former i en Android-karta](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)