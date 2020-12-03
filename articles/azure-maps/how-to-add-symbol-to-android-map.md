---
title: Lägg till ett symbol lager till en karta med Azure Maps Android SDK
description: Lär dig hur du lägger till en markör till en karta. Se ett exempel som använder Microsoft Azure mappar Android SDK för att lägga till ett symbol lager som innehåller punktbaserade data från en data källa.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/24/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 300a7968b2072459d6d7709e4d89388e1bcf59f3
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531215"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Lägg till ett symbol lager till en karta med Azure Maps Android SDK

Den här artikeln visar hur du återger punkt data från en data källa som ett symbol lager på en karta med hjälp av Azure Maps Android SDK.

## <a name="prerequisites"></a>Krav

1. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel.
3. Hämta och installera [Azure Maps Android SDK](./how-to-use-android-map-control-library.md).

## <a name="add-a-symbol-layer"></a>Lägga till ett symbolskikt

Följ stegen nedan om du vill lägga till en markör på kartan med symbol skiktet:

1. Redigera **res**  >  **layout**  >  **activity_main.xml** så att det ser ut som i följande XML:
    
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

2. Kopiera följande kodfragment till **onCreate ()-** metoden för din `MainActivity.java` klass.

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a red custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    När du har lagt till kodfragmentet ovan `MainActivity.java` bör det se ut som det som visas nedan:
    
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

När du kör programmet bör du se en markör på kartan, som du ser här:

![PIN-kod för Android-karta](./media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> Som standard optimerar symbol lager åter givningen av symboler genom att dölja symboler som överlappar varandra. När du zoomar in blir de dolda symbolerna synliga. Om du vill inaktivera den här funktionen och återge alla symboler hela tiden ställer du in `iconAllowOverlap` alternativet till `true` .

## <a name="next-steps"></a>Nästa steg

Information om hur du lägger till mer data i kartan finns i:

> [!div class="nextstepaction"]
> [Lägga till former i en Android-karta](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Visa funktionsinformation](display-feature-information-android.md)