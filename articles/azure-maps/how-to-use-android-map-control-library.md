---
title: Hur du använder Android kartkontroll i Azure Maps | Microsoft Docs
description: Använd kartkontroll för Android i Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 57cc585d621c71872a4b7658c74f581c8998b245
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341087"
---
# <a name="how-to-use-azure-maps-android-sdk"></a>Hur du använder Android SDK för Azure Maps

Azure Maps Android SDK är ett vektor maps-bibliotek för Android. Den här artikeln vägleder dig genom processen med att installera Android SDK för Azure Maps, läser in en karta och placera en PIN-kod på den.

## <a name="prerequisites-to-get-started"></a>Förutsättningar för att komma igång

### <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto 

Om du vill följa stegen i den här guiden, måste du först se [hantera konton och nycklar](how-to-manage-account-keys.md) du skapar och hanterar prenumerationen konto med S1 prisnivå.

### <a name="download-android-studio"></a>Ladda ned Android studio

Du kan ladda ned [Android Studio](https://developer.android.com/studio/) kostnadsfritt från Google. För att kunna installera Android SDK för Azure Maps, måste du först ladda ned Android Studio och skapa ett projekt med en tom aktivitet.

## <a name="create-a-project-in-android-studio"></a>Skapa ett projekt i Android Studio

Du behöver skapa ett nytt projekt med en tom aktivitet. Följ stegen nedan för att skapa ett nytt Android Studio-projekt:

1. Under *väljer du projektet*, kontrollera ”Telefoner och surfplattor” som formfaktorn som programmet ska köras på.
2. Klicka på *tom aktivitet* under formfaktorn och klicka på **nästa**.
3. Under *konfigurera ditt projekt*väljer `API 21: Android 5.0.0 (Lollipop)` som lägsta möjliga SDK. Det här är den lägsta version som stöds av Android SDK för Azure Maps.
4. Acceptera standardvärdet `Activity Name` och `Layout Name` och klicka på **Slutför**

Se [Android Studio-dokumentation](https://developer.android.com/studio/intro/) mer att installera Android Studio och skapa ett nytt projekt.

![Skapa ett nytt projekt](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Konfigurera en virtuell enhet

Android Studio kan du konfigurera en virtuell Android-enhet på datorn. Som kan hjälpa dig för att testa ditt program när du utvecklar. Du ställer in en virtuell enhet klickar du på Android Virtual Device (AVD) Manager-ikonen längst upp till höger på skärmen projekt. Klicka sedan på den **Skapa virtuell enhet** knappen. Du kan också få till manager via **Verktyg > Android > AVD Manager** i verktygsfältet. Från den **telefoner** kategori, väljer **Nexus 5 X** och klicka på **nästa**.

Läs mer om hur du konfigurerar en AVD i den [Android Studio-dokumentation](https://developer.android.com/studio/run/managing-avds).

![Android-emulatorn](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-azure-maps-android-sdk"></a>Installera Azure Maps Android SDK

Följ stegen nedan för att installera Azure Maps Android SDK innan du gå vidare för att skapa ditt program. 

1. Lägg till följande till den **alla projekt**, databaser genom att blockera i din **build.gradle** fil.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Uppdatera din **App/build.gradle** och Lägg till följande:

    1. Lägg till följande Android blocket:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. Uppdatera din blocket med beroenden och Lägg till följande:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. Konfigurera behörigheter genom att lägga till följande till din **AndroidManifest.xml**

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. Redigera **res > layout > activity_main.xml**, så att det ser ut som nedan XML-filen:
    
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
            app:mapcontrol_cameraTargetLat="47.64"
            app:mapcontrol_cameraTargetLng="-122.33"
            app:mapcontrol_cameraZoom="12"
            />

    </FrameLayout>
    ```

5. Redigera **MainActivity.java** att skapa en karta visa aktivitet klass. Det bör se ut klassen nedan när du har redigerat:

    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

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

## <a name="import-classes"></a>Importera klasser

När du har slutfört stegen ovan, är förmodligen får du varningar från Android Studio på en del av texten i koden. För att lösa dessa varningar, importera de klasser som refereras i `MainActivity.java`.

Du kan importera de här klasserna automatiskt genom att trycka på `Alt` + `Enter`(`Option` + `Return` på Mac). 

Klicka på den **kör ”App”** knappen (eller `Control` + `R` på en Mac) att skapa ditt program.

![Klicka på Kör](./media/how-to-use-android-map-control-library/run-app.png)

Det tar några sekunder för android studio för att skapa programmet. När versionen har slutförts kan du testa ditt program i emulerade Android-enhet. Du kan se en karta som liknar den nedan.

![Android karta](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>Lägga till en markör på kartan

För att lägga till en markör på kartan, lägger du till `mapView.getMapAsync()` funktionen för att den `MainActivity.java`. Sista `MainActivity.java` bör se ut som följande:

```java
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
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

    MapControl mapControl;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        mapControl.getMapAsync(map -> {
            DataSource dataSource = new DataSource();
            dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

            SymbolLayer symbolLayer = new SymbolLayer(dataSource);
            symbolLayer.setOptions(iconImage("my-icon"));

            map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            map.sources.add(dataSource);
            map.layers.add(symbolLayer);
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

Köra ditt program och du bör se en markör på kartan som den som visas nedan.

![Android kartan PIN-kod](./media/how-to-use-android-map-control-library/android-map-pin.png)