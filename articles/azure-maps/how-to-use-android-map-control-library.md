---
title: Komma igång med Kartkontrollen för Android | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du kommer igång med Android-kartkontrollen med hjälp av Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 6e0f0f311b7ec8adae6ddb25e01046141adadfa4
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548544"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Komma igång med Azure Maps Android SDK

Azure Maps Android SDK är ett vektorkartbibliotek för Android. Den här artikeln guidar dig genom processerna för att installera Azure Maps Android SDK och läsa in en karta.

## <a name="prerequisites"></a>Krav

### <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto

För att slutföra procedurerna i den här artikeln måste du först [skapa ett Azure Maps-konto](quick-demo-map-app.md#create-an-account-with-azure-maps) på prisnivån S1 och [få primärnyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account) för ditt konto.

Mer information om autentisering i Azure Maps finns [i hantera autentisering i Azure Maps](./how-to-manage-authentication.md).

### <a name="download-android-studio"></a>Ladda ner Android Studio

Ladda ned Android Studio och skapa ett projekt med en tom aktivitet innan du installerar Azure Maps Android SDK. Du kan [ladda ner Android Studio](https://developer.android.com/studio/) gratis från Google. 

## <a name="create-a-project-in-android-studio"></a>Skapa ett projekt i Android Studio

Skapa först ett nytt projekt med en tom aktivitet. Gör så här för att skapa ett Android Studio-projekt:

1. Under **Välj projekt**väljer du Telefon och **Surfplatta**. Ditt program körs på den här formfaktorn.
2. På fliken **Telefon och Surfplatta** väljer du Tom **aktivitet**och väljer sedan **Nästa**.
3. Under **Konfigurera projektet** `API 21: Android 5.0.0 (Lollipop)` väljer du som minsta SDK. Det här är den tidigaste versionen som stöds av Azure Maps Android SDK.
4. Acceptera standardinställningen `Activity Name` och `Layout Name` välj **Slutför**.

Mer hjälp med att installera Android Studio och skapa ett nytt projekt finns i [Android Studio-dokumentationen.](https://developer.android.com/studio/intro/)

![Skapa ett projekt i Android-studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Konfigurera en virtuell enhet

Med Android Studio kan du konfigurera en virtuell Android-enhet på din dator. Om du gör det kan du testa ditt program under utvecklingen. Om du vill konfigurera en virtuell enhet väljer du ikonen Android Virtual Device (AVD) Manager i det övre högra hörnet på projektskärmen och väljer sedan **Skapa virtuell enhet**. Du kan också komma till AVD Manager genom att välja **Verktyg** > **Android** > **AVD Manager** från verktygsfältet. I kategorin **Telefoner** väljer du **Nexus 5X**och väljer sedan **Nästa**.

Du kan läsa mer om hur du konfigurerar en AVD i [Android Studio-dokumentationen](https://developer.android.com/studio/run/managing-avds).

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Installera Azure Maps Android SDK

Nästa steg i att skapa ditt program är att installera Azure Maps Android SDK. Gör så här för att installera SDK:

1. Öppna filen **build.gradle** på den översta nivån och lägg till följande kod i avsnittet **alla projekt**, spärrar du av **databaser:**

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Uppdatera **appen/build.gradle** och lägg till följande kod i den:
    
    1. Kontrollera att projektets **minSdkVersion** finns på API 21 eller senare.

    2. Lägg till följande kod i Avsnittet Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Uppdatera beroendenas blockering och lägg till en ny implementeringsberoenderad för den senaste Azure Maps Android SDK:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```
    
    4. Gå till **Arkiv** i verktygsfältet och klicka sedan på **Synkronisera projekt med Gradle-filer**.
3. Lägg till ett kartfragment \> i \> \_huvudaktiviteten (res layout activity main.xml):
    
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

4. I filen **MainActivity.java** måste du:
    
    * lägga till import för Azure Maps SDK
    * ange din autentiseringsinformation för Azure Maps
    * hämta kartkontrollinstansen i **metoden onCreate**

    Om du ställer `AzureMaps` in autentiseringsinformationen för klassen globalt med hjälp av metoderna `setSubscriptionKey` eller `setAadProperties` gör det så att du inte behöver lägga till din autentiseringsinformation i varje vy. 

    Kartkontrollen innehåller sina egna livscykelmetoder för att hantera Androids OpenGL-livscykel. Dessa livscykelmetoder måste anropas direkt från den innehållande aktiviteten. Om appen ska kunna anropa kartkontrollens livscykelmetoder på rätt sätt måste du åsidosätta följande livscykelmetoder i aktiviteten som innehåller kartkontrollen. Och du måste anropa respektive kartkontrollmetod. 

    * onCreate(Paket) 
    * onStart() 
    * onResume() 
    * onPause() 
    * onStop() 
    * onDestroy() 
    * onSaveInstanceState(Paket) 
    * onLowMemory() 

    Redigera filen **MainActivity.java** enligt följande:
    
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

När du har slutfört föregående steg får du förmodligen varningar från Android Studio om en del av koden. Lös dessa varningar genom att importera `MainActivity.java`de klasser som refereras i .

Du kan importera dessa klasser automatiskt genom att välja Alt+Retur (Alternativ+Retur på en Mac).

Välj körknappen, som visas i följande bild (eller tryck på Control+R på en Mac) för att skapa programmet.

![Klicka på Kör](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio tar några sekunder att bygga programmet. När versionen är klar kan du testa ditt program i den emulerade Android-enheten. Du bör se en karta som denna:

<center>

![Azure Maps i Android-program](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Lokalisera kartan

Azure Maps Android SDK innehåller tre olika sätt att ange språket och den regionala vyn på kartan. Följande kod visar hur du ställer in språket på franska ("fr-FR") och den regionala vyn till "auto". 

Det första alternativet är att skicka språket `AzureMaps` och visa `setLanguage` regional `setView` information till klassen med hjälp av statiska metoder och metoder globalt. Detta anger standardspråk och regional vy för alla Azure Maps-kontroller som läses in i din app.

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

Det andra alternativet är att skicka språket och visa information till XML-koden för kartkontrollen.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Det tredje alternativet är att programmässigt ställa in språket och `setStyle` den regionala vyn av kartan med hjälp av kartmetoden. Detta kan göras när som helst för att ändra språket och den regionala vyn på kartan.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Här är ett exempel på Azure Maps med språket inställt på "fr-FR" och regional vy inställd på "auto".

<center>

![Azure Maps, kartbild som visar etiketter på franska](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

En fullständig lista över språk och regionala vyer som stöds dokumenteras [här](supported-languages.md).

## <a name="navigating-the-map"></a>Navigera på kartan

Det finns flera olika sätt på vilka kartan kan zoomas, panoreras, roteras och kastas. Följande beskriver alla olika sätt att navigera på kartan.

**Zooma kartan**

- Tryck på kartan med två fingrar och nyp ihop för att zooma ut eller sprida fingrarna isär för att zooma in.
- Dubbeltryck på kartan för att zooma in en nivå.
- Dubbeltryck med två fingrar för att zooma ut kartan en nivå.
- Tryck två gånger; Vid andra tryck håller du fingret på kartan och drar uppåt för att zooma in eller nedåt för att zooma ut.

**Panorera kartan**

- Tryck på kartan och dra i valfri riktning.

**Rotera kartan**

- Tryck på kartan med två fingrar och rotera.

**Pitcha kartan**

- Tryck på kartan med två fingrar och dra dem uppåt eller nedåt tillsammans.

## <a name="next-steps"></a>Nästa steg

Läs om hur du lägger till överlagringsdata på kartan:

> [!div class="nextstepaction"]
> [Lägga till ett symbollager på en Android-karta](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Lägga till former på en Android-karta](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Ändra kartstilar i Android-kartor](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
