---
title: Komma igång med Azure Maps Android SDK
description: Bekanta dig med Microsoft Azure Maps Android SDK. Se hur du skapar ett projekt i Android Studio, installerar SDK och skapar en interaktiv karta.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 1da003bb8d285dbedde87cbc6cd4708fda2dc38b
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531269"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Komma igång med Azure Maps Android SDK

Azure Maps Android SDK är ett vektor kart bibliotek för Android. Den här artikeln vägleder dig genom processerna för att installera Azure Maps Android SDK och läsa in en karta.

## <a name="prerequisites"></a>Krav

### <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto

1. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel.
Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](./how-to-manage-authentication.md).
3. [Ladda ned och installera googles Android Studio](https://developer.android.com/studio/).

## <a name="create-a-project-in-android-studio"></a>Skapa ett projekt i Android Studio

Utför de här stegen för att skapa ett Android Studio-projekt:

1. Starta Android Studio.
2. Klicka på **+ Skapa nytt projekt**.
3. På fliken **telefon och surfplatta** klickar du på **Tom aktivitet**. Klicka på **Nästa**.
4. Under **Konfigurera ditt projekt** väljer `API 21: Android 5.0.0 (Lollipop)` du som lägsta SDK.
5. Välj `Java` som språk.
6. Acceptera standardvärdet `Name` för projektet. Klicka på **Finish**.

Se [Android Studio-dokumentationen](https://developer.android.com/studio/intro/) för att få hjälp med att installera Android Studio och skapa ett nytt projekt.

![Skapa ett projekt i Android Studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-device"></a>Konfigurera en enhet

Om du vill testa programmet under utvecklingen kan du använda antingen en Android-telefon eller en Android-emulator.

Mer information om hur du konfigurerar en AVD (Android Virtual Device) finns i [Android Studio-dokumentationen](https://developer.android.com/studio/run/managing-avds).

## <a name="install-the-azure-maps-android-sdk"></a>Installera Azure Maps Android SDK

Nästa steg i att skapa ditt program är att installera Azure Maps Android SDK.

Slutför de här stegen för att installera SDK:

1. På fliken projekt expanderar du **Gradle-skript**. Öppna **build. gradle (projekt: My_Application)** och Lägg till följande kod i avsnittet **alla projekt** `repositories`  :

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Öppna **build. gradle (modul: My_Application)**.

3. Se till att **minSdkVersion** i `defaultConfig` avsnittet är i API 21 eller högre.

4. Lägg till följande kod i Android-avsnittet:

    ```
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    ```

5. Lägg till följande kod i `dependencies` avsnittet:

    ```
    implementation "com.microsoft.azure.maps:mapcontrol:0.6"
    ```

6. Klicka på **fil** i huvud verktygsfältet och välj sedan **Synkronisera projekt med Gradle-filer**.

7. Öppna `res > layout > activity_main.xml`. Klicka på `Code` Visa i det övre högra hörnet. Lägg till följande XML inuti `<androidx.constraintlayout.widget.ConstraintLayout>` elementet.
    
    ```XML
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

8. I `java > com.example.myapplication > MainActivity.java` filen måste du:

    * Lägg till importer för Azure Maps SDK.
    * Ange information om Azure Maps-autentisering.
    * Hämta kart kontroll instansen i **onCreate** -metoden.

    För att undvika att behöva lägga till autentiseringsinformation för varje programvy ställer vi in autentiseringsinformation globalt genom att anropa `AzureMaps.setSubscriptionKey` . Du kan också anropa `AzureMaps.setAadProperties` , om du vill autentisera med hjälp av Azure Active Directory.

    Kart kontrollen åsidosätter följande livs cykel metoder för klassen MainActivity. Dessa metoder ansvarar för att hantera Android: s OpenGL-livs cykel.

    * onCreate (paket)
    * onStart ()
    * onResume()
    * onPause ()
    * onStop ()
    * onDestroy()
    * onSaveInstanceState (paket)
    * onLowMemory()

    Redigera `MainActivity.java` filen på följande sätt:

    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
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

            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.

            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
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

>[!WARNING]
>Android Studio kanske inte har importerat de obligatoriska klasserna.  Därför har koden vissa referenser som inte kan matchas. Om du vill importera de obligatoriska klasserna hovrar du bara över varje olöst referens och trycker på `Alt + Enter` (alternativ + retur på en Mac).

Android Studio tar några sekunder att skapa programmet. När versionen har slutförts kan du testa programmet i den emulerade Android-enheten. Du bör se en karta som den här:

:::image type="content" source="./media/how-to-use-android-map-control-library/android-map.png" border="true" alt-text="Azure Maps i Android-program":::

## <a name="localizing-the-map"></a>Lokalisera kartan

Azure Maps Android SDK innehåller tre olika sätt att ange språk och nationella inställningar för kartan.

1. Ange språk inställningar och nationella inställningar genom att anropa statiska metoder i klassen AzureMaps.

    ```Java
    static {
        //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");

        //Set the regional view.
        AzureMaps.setLanguage("Auto");
    
    }
    ```

2. Definiera språk och nationella inställningar i kart kontrollens XML-fil.

    ```XML
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        />
    ```

3. Ange språk och nationella inställningar genom att anropa metoder på kart kontrollen. Med det här alternativet kan du ändra inställningarna under körning.

    ```Java
    mapControl.onReady(map -> {
        map.setStyle(StyleOptions.language("fr-FR"));
        map.setStyle(StyleOptions.view("Auto"));
    
    });
    ```

Här är ett exempel på Azure Maps med språket inställt på `fr-FR` .

:::image type="content" source="./media/how-to-use-android-map-control-library/android-localization.png" border="true" alt-text="Azure Maps, kart bild som visar etiketter på franska":::

En fullständig lista över språk som stöds och nationella vyer beskrivs [här](supported-languages.md).

## <a name="navigating-the-map"></a>Navigera i kartan

Det finns flera olika sätt på vilka kartan kan zoomas, panoreras, roteras och visas. Följande information innehåller alla olika sätt att navigera i kartan.

**Zooma kartan**

- Tryck på kartan med två fingrar och dra ihop för att zooma ut eller sprida fingrarna från varandra för att zooma in.
- Dubbel tryck på kartan för att zooma in en nivå.
- Dubbel tryck med två fingrar för att zooma ut kartan med en nivå.
- Tryck två gånger; vid andra knackningar håller du fingret på kartan och drar upp för att zooma in eller ned för att zooma ut.

**Panorera kartan**

- Vidrör kartan och dra i vilken riktning som helst.

**Rotera kartan**

- Vidrör kartan med två fingrar och rotera.

**Bredd på kartan**

- Vidrör kartan med två fingrar och dra dem uppåt eller nedåt.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du lägger till överläggs data på kartan:

> [!div class="nextstepaction"]
> [Lägga till ett symbol lager i en Android-karta](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Lägga till former i en Android-karta](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Ändra kart format i Android Maps](./set-android-map-styles.md)