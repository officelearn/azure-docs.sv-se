---
title: Komma igång med Android kartkontroll i Azure Maps | Microsoft Docs
description: Android kartkontrollen i Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e655b442ba9290d4b4525108521f2d1a0c766b48
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869797"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Komma igång med Android SDK för Azure Maps

Android SDK för Azure Maps är ett vector map-bibliotek för Android. Den här artikeln vägleder dig genom processerna som installerar Azure Maps Android SDK och läser in en karta.

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto

För att slutföra procedurerna i den här artikeln, måste du först [skapa ett Azure Maps-konto](how-to-manage-account-keys.md) i du prisnivån S1.

### <a name="download-android-studio"></a>Ladda ned Android Studio

Du måste ladda ned Android Studio och skapa ett projekt med en tom aktivitet innan du installerar Azure Maps Android SDK. Du kan [ladda ned Android Studio](https://developer.android.com/studio/) kostnadsfritt från Google. 

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

1. Öppna den översta nivån **build.gradle** filen och Lägg till följande kod till den **alla projekt**, **databaser** blockera avsnittet:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Uppdatera din **App/build.gradle** och Lägg till följande kod:
    
    1. Se till att ditt projekt **minSdkVersion** på API-21 eller högre.

    2. Lägg till följande kod i avsnittet Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Uppdatera din blocket med beroenden och lägga till en ny rad i implementeringen beroende för den senaste Azure Maps Android SDK:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

    > [!Note]
    > Android SDK för Azure Maps regelbundet håller på att uppgraderas och förbättrad. Du kan se den [komma igång med Android kartkontroll](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) dokumentation för att hämta senaste versionsnumret för Azure Maps-implementering. Du kan också ange versionsnumret från ”0.2” till ”0 +” att den alltid pekar till den senaste versionen.

3. Redigera **res** > **layout** > **activity_main.xml** och Ersätt den med följande:
    
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
            />
    </FrameLayout>
    ```

4. I den **MainActivity.java** fil måste du:
    
    * Lägg till importer för Azure Maps SDK
    * Ange autentiseringsinformation för ditt Azure Maps
    * Hämta map control-instans i den **onCreate** metod

    Ange autentiseringsinformationen för klassen AzureMaps globalt med metoderna setSubscriptionKey eller setAadProperties gör det så att du inte behöver lägga till din autentiseringsinformation om varje vy. Kartkontrollen innehåller sin egen livscykel-metoder för att hantera Androids OpenGL livscykel, som måste anropas direkt från den överordnade aktiviteten. För din app för att anropa korrekt i kartkontroll livscykel metoder, måste du åsidosätter livscykel följande metoder i den aktivitet som innehåller kartkontrollen och anropa metoden respektive karta kontroll. 

    Redigera den **MainActivity.java** så här:
    
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
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
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

<center>

![Android karta](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="next-steps"></a>Nästa steg

Om du vill lägga till innehåll i kartan, se:

> [!div class="nextstepaction"]
> [Lägga till ett lager för symbolen i en Android-karta](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [Lägga till former i en Android-karta](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Ändra Koppla format i Android maps](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)


