---
title: Komma igång med Android Map-kontroll | Microsoft Azure Maps
description: Bekanta dig med Azure Maps Android SDK. Se hur du skapar ett projekt i Android Studio, installerar SDK och skapar en interaktiv karta.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 72bb821c0dfed6d3f9e7e2cc222242e65a35a011
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911060"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Komma igång med Azure Maps Android SDK

Azure Maps Android SDK är ett vektor kart bibliotek för Android. Den här artikeln vägleder dig genom processerna för att installera Azure Maps Android SDK och läsa in en karta.

## <a name="prerequisites"></a>Förutsättningar

### <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto

För att kunna slutföra procedurerna i den här artikeln måste du först [skapa ett Azure Maps-konto](quick-demo-map-app.md#create-an-azure-maps-account) på pris nivån S1 och [Hämta primär nyckeln](quick-demo-map-app.md#get-the-primary-key-for-your-account) för ditt konto.

Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](./how-to-manage-authentication.md).

### <a name="download-android-studio"></a>Ladda ned Android Studio

Hämta Android Studio och skapa ett projekt med en tom aktivitet innan du installerar Azure Maps Android SDK. Du kan [Ladda ned Android Studio](https://developer.android.com/studio/) kostnads fritt från Google. 

## <a name="create-a-project-in-android-studio"></a>Skapa ett projekt i Android Studio

Börja med att skapa ett nytt projekt med en tom aktivitet. Utför de här stegen för att skapa ett Android Studio-projekt:

1. Välj **telefon och surfplatta** under **Välj ditt projekt** . Programmet kommer att köras på den här formulär faktorn.
2. På fliken **telefon och surfplatta** väljer du **Tom aktivitet** och väljer sedan **Nästa** .
3. Under **Konfigurera ditt projekt** väljer `API 21: Android 5.0.0 (Lollipop)` du som lägsta SDK. Detta är den tidigaste versionen som stöds av Azure Maps Android SDK.
4. Godkänn standard `Activity Name` och `Layout Name` Välj **Slutför** .

Se [Android Studio-dokumentationen](https://developer.android.com/studio/intro/) för att få hjälp med att installera Android Studio och skapa ett nytt projekt.

![Skapa ett projekt i Android Studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Konfigurera en virtuell enhet

Med Android Studio kan du konfigurera en virtuell Android-enhet på datorn. På så sätt kan du testa ditt program under utveckling. Om du vill konfigurera en virtuell enhet väljer du ikonen för AVD-hanteraren (Android Virtual Device) i det övre högra hörnet på projekt skärmen och väljer sedan **Skapa virtuell enhet** . Du kan också gå till avd Manager genom att välja **verktyg**  >  **Android**  >  **avd Manager** i verktygsfältet. I kategorin **telefoner** väljer du **Nexus 5x** , och väljer sedan **Nästa** .

Du kan lära dig mer om hur du konfigurerar en AVD i [Android Studio-dokumentationen](https://developer.android.com/studio/run/managing-avds).

![Android-emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Installera Azure Maps Android SDK

Nästa steg i att skapa ditt program är att installera Azure Maps Android SDK. Slutför de här stegen för att installera SDK:

1. Öppna filen **build. gradle** på den översta nivån och Lägg till följande kod i avsnittet **alla projekt** , **databaser** :

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Uppdatera **appen/build. gradle** och Lägg till följande kod i den:
    
    1. Kontrol lera att projektets **minSdkVersion** är i API 21 eller högre.

    2. Lägg till följande kod i Android-avsnittet:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Uppdatera beroende blocket och Lägg till en ny implementerings beroende linje för den senaste Azure Maps Android SDK:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.6"
        ```
    
    4. Gå till **filen** i verktygsfältet och klicka sedan på **Synkronisera projekt med Gradle-filer** .
3. Lägg till ett kart fragment i huvud aktiviteten (aktivitet för res \> layout \> \_main.xml):
    
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

4. I filen **MainActivity. java** måste du:
    
    * lägga till importer för Azure Maps SDK
    * Ange information om Azure Maps-autentisering
    * Hämta kart kontroll instansen i **onCreate** -metoden

    Att ange autentiseringsinformation för `AzureMaps` klassen globalt med hjälp av `setSubscriptionKey` metoderna eller `setAadProperties` gör det så att du inte behöver lägga till autentiseringsinformation i alla vyer. 

    Kart kontrollen innehåller sina egna livs cykel metoder för att hantera Android: s OpenGL-livscykel. Dessa livs cykel metoder måste anropas direkt från den som innehåller aktiviteten. För att appen ska kunna anropa kart kontrollens livs cykel metoder måste du åsidosätta följande livs cykel metoder i den aktivitet som innehåller kart kontrollen. Och du måste anropa respektive kart kontroll metod. 

    * onCreate (paket) 
    * onStart () 
    * onResume() 
    * onPause () 
    * onStop () 
    * onDestroy() 
    * onSaveInstanceState (paket) 
    * onLowMemory() 

    Redigera filen **MainActivity. java** enligt följande:
    
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

## <a name="import-classes"></a>Importera klasser

När du har slutfört de föregående stegen får du förmodligen varningar från Android Studio om en del av koden. Om du vill lösa de här varningarna importerar du de klasser som refereras till i `MainActivity.java` .

Du kan importera dessa klasser automatiskt genom att välja Alt + Retur (alternativ + retur på en Mac).

Välj knappen Kör, som du ser i följande bild (eller tryck på CTRL + R på en Mac) för att skapa programmet.

![Klicka på Kör](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio tar några sekunder att skapa programmet. När versionen har slutförts kan du testa programmet i den emulerade Android-enheten. Du bör se en karta som den här:

<center>

![Azure Maps i Android-program](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Lokalisera kartan

Azure Maps Android SDK innehåller tre olika sätt att ange språk och regional vy för kartan. Följande kod visar hur du ställer in språket på franska ("fr-FR") och den regionala vyn till "Auto". 

Det första alternativet är att skicka språket och Visa regional information i `AzureMaps` klassen med hjälp av metoderna static `setLanguage` och `setView` Global. Detta anger standard språket och den regionala vyn för alla Azure Maps kontroller som läses in i din app.

```Java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

Det andra alternativet är att skicka språket och Visa information i kart kontrollens XML-fil.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Det tredje alternativet är att program mässigt ange språket och den regionala vyn för kartan med hjälp av Maps- `setStyle` metoden. Detta kan göras när som helst för att ändra språket och den regionala vyn för kartan.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Här är ett exempel på Azure Maps med språket "fr-FR" och regional View inställd på "Auto".

<center>

![Azure Maps, kart bild som visar etiketter på franska](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

En fullständig lista över språk som stöds och nationella vyer beskrivs [här](supported-languages.md).

## <a name="navigating-the-map"></a>Navigera i kartan

Det finns flera olika sätt på vilka kartan kan zoomas, panoreras, roteras och visas. Följande information innehåller alla olika sätt att navigera i kartan.

**Zooma kartan**

- Tryck på kartan med två fingrar och dra ihop för att zooma ut eller sprida fingrarna från varandra för att zooma in.
- Dubbel tryck på kartan för att zooma in en nivå.
- Dubbel tryck med två fingrar för att zooma ut kartan på en nivå.
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