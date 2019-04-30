---
title: Hur du använder Android kartkontrollen i Azure Maps | Microsoft Docs
description: Android kartkontrollen i Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 15706addbe6b7f6310223978130158c792a47c89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770389"
---
# <a name="how-to-use-the-azure-maps-android-sdk"></a>Hur du använder Azure Maps Android SDK

Android SDK för Azure Maps är ett vector map-bibliotek för Android. Den här artikeln vägleder dig genom processerna för installation av Android SDK för Azure Maps, läser in en karta och placera en PIN-kod på kartan.

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto

För att slutföra procedurerna i den här artikeln, måste du först [skapa ett Azure Maps-konto](how-to-manage-account-keys.md) i du prisnivån S1.

### <a name="download-android-studio"></a>Ladda ned Android Studio

Du måste ladda ned Android Studio och skapa ett projekt med en tom aktivitet innan du kan installera Android SDK för Azure Maps. Du kan [ladda ned Android Studio](https://developer.android.com/studio/) kostnadsfritt från Google. 

## <a name="create-a-project-in-android-studio"></a>Skapa ett projekt i Android Studio

Först måste du skapa ett nytt projekt med en tom aktivitet. Utför stegen nedan för att skapa ett Android Studio-projekt:

1. Under **väljer du projektet**väljer **Telefoner och surfplattor**. Programmet körs på den här formfaktorn.
2. På den **Telefoner och surfplattor** fliken **tom aktivitet**, och välj sedan **nästa**.
3. Under **konfigurera ditt projekt**väljer `API 21: Android 5.0.0 (Lollipop)` som lägsta möjliga SDK. Det här är den tidigaste version som stöds av Android SDK för Azure Maps.
4. Acceptera standardvärdet `Activity Name` och `Layout Name` och välj **Slutför**.

Se den [Android Studio-dokumentation](https://developer.android.com/studio/intro/) för mer hjälp med installation Android Studio och skapa ett nytt projekt.

![Skapa ett projekt](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Konfigurera en virtuell enhet

Android Studio kan du konfigurera en virtuell Android-enhet på datorn. Detta kan hjälpa dig att testa ditt program under utveckling. Om du vill konfigurera en virtuell enhet, väljer du Android Virtual Device (AVD) Manager-ikonen i det övre högra hörnet på skärmen projektet och välj sedan **Skapa virtuell enhet**. Du kan också få till AVD Manager genom att välja **verktyg** > **Android** > **AVD Manager** från verktygsfältet. I den **telefoner** kategori, väljer **Nexus 5 X**, och välj sedan **nästa**.

Du kan lära dig mer om hur du konfigurerar en AVD i den [Android Studio-dokumentation](https://developer.android.com/studio/run/managing-avds).

![Android-emulatorn](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Installera Azure Maps Android SDK

Nästa steg i att skapa ditt program är att installera Android SDK för Azure Maps. Utför stegen nedan för att installera SDK:

1. Lägg till följande kod till den **alla projekt**, **databaser** blockera i din **build.gradle** fil.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Uppdatera din **App/build.gradle** och Lägg till följande kod:

    1. Lägg till följande kod i blocket Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. Uppdatera din blocket med beroenden och Lägg till följande kod:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. Konfigurera behörigheter genom att lägga till följande XML-filen till din **AndroidManifest.xml** fil:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. Redigera **res** > **layout** > **activity_main.xml** så att det ser ut som den här XML:
    
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

5. Redigera **MainActivity.java** att skapa en karta visa aktivitet klass. När du har redigerat bör det se ut som den här klassen:

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

När du har slutfört föregående steg kan får du förmodligen varningar från Android Studio om några av koden. För att lösa dessa varningar, importera de klasser som refereras i `MainActivity.java`.

Du kan automatiskt importera dessa klasser genom att välja Alt + Retur (ALT + RETUR på en Mac).

Välj knappen Kör som visas i följande bild (eller tryck på CTRL + R på en Mac), att skapa ditt program.

![Klicka på Kör](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio tar några sekunder att skapa programmet. När versionen har slutförts, kan du testa ditt program i emulerade Android-enhet. Du bör se en karta som den här:

![Android karta](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>Lägga till en markör på kartan

Om du vill lägga till en markör till kartan, lägger du till den `mapView.getMapAsync()` funktionen för att `MainActivity.java`. Sista `MainActivity.java` kod bör se ut så här:

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

Kör programmet igen. Du bör se en markör på kartan som visas här:

![Android kartan PIN-kod](./media/how-to-use-android-map-control-library/android-map-pin.png)