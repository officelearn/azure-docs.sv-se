---
title: 'Självstudiekurs: Migrera en Android-app | Microsoft Azure Maps'
description: Så här migrerar du en Android-app från Google Maps till Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 9514398ec6a84becd1283e4b0975804101b64086
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77209740"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Migrera en Android-app från Google Maps

Azure Maps Android SDK har ett API-gränssnitt som liknar Web SDK. Om du har utvecklat med en av dessa SDK: er gäller många av samma koncept, metodtips och arkitekturer.

Azure Maps Android SDK stöder en minsta Android-version av API 21: Android 5.0.0 (Lollipop).

Alla exempel finns i Java; Du kan dock använda Kotlin med Azure Maps Android SDK.

Mer information om hur du utvecklar med Android SDK by Azure Maps finns i [hjälpguiderna för Azure Maps Android SDK](how-to-use-android-map-control-library.md).

## <a name="load-a-map"></a>Ladda en karta

Att läsa in en karta i en Android-app med Google eller Azure Maps består av liknande steg. När du använder antingen SDK måste du:

- Skaffa en API- eller prenumerationsnyckel för att komma åt någon av plattformarna.
- Lägg till lite XML i en aktivitet för att ange var kartan ska återges och hur den ska läggas ut.
- Åsidosätt alla livscykelmetoder från aktiviteten som innehåller kartvyn till motsvarande metoder i kartklassen. I synnerhet måste du åsidosätta följande metoder:
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Vänta tills kartan är klar innan du försöker komma åt och programmera den.

**Före: Google Maps**

Så här visar du en karta med Google Maps SDK för Android:

1.  Se till att Google Play-tjänsterna är installerade.
2.  Lägg till ett beroende för Google Maps-tjänsten i modulens **gradle.build-fil:** 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Lägg till en API-nyckel för Google Maps i programavsnittet i filen **google\_maps\_api.xml:**
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  Lägg till ett kartfragment i huvudaktiviteten:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  I filen **MainActivity.java** måste du importera Google Maps SDK. Vidarebefordra alla livscykelmetoder från aktiviteten som innehåller kartvyn till motsvarande metoder i kartklassen. Hämta `MapView` en förekomst från `getMapAsync(OnMapReadyCallback)` kartfragmentet med hjälp av metoden. Automatiskt `MapView` initieras kartor systemet och vyn. Redigera filen **MainActivity.java** enligt följande:

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
    
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

När du kör ett program läses kartkontrollen in som i följande bild.

<center>

![Enkla Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**Efter: Azure Maps**

Om du vill visa en karta med Azure Maps SDK för Android måste följande steg göras:

1. Öppna filen **build.gradle** på den översta nivån och lägg till följande kod i avsnittet **alla projektblock:**

    ```JAVA
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Uppdatera **appen/build.gradle** och lägg till följande kod i den:
    
    1. Kontrollera att projektets **minSdkVersion** finns på API 21 eller senare.

    2. Lägg till följande kod i Avsnittet Android:

        ```java
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Uppdatera beroendeblocket. Lägg till en ny implementeringsberoenderad för den senaste Azure Maps Android SDK:

        ```java
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Azure Maps Android SDK uppgraderas och förbättras regelbundet. Du kan se [kartkontrollen Komma igång med Android](how-to-use-android-map-control-library.md) för att få det senaste Versionsnumret för Azure Maps. Du kan också ställa in versionsnumret från "0.2" till "0+" så att koden alltid pekar på den senaste versionen.
    
    4. Gå till **Arkiv** i verktygsfältet och klicka sedan på **Synkronisera projekt med Gradle-filer**.
3. Lägg till ett kartfragment \> \> i\_huvudaktiviteten (resurslayoutaktivitet main.xml):
    
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
    
    * Importerar SDK för Azure Maps
    * Ange autentiseringsinformation för Azure Maps
    * Hämta kartkontrollinstansen i **metoden onCreate**

     Ange autentiseringsinformation `AzureMaps` i klassen `setSubscriptionKey` `setAadProperties` med hjälp av metoderna eller. Den här globala uppdateringen, se till att du lägger till din autentiseringsinformation i varje vy.

    Kartkontrollen innehåller sina egna livscykelmetoder för att hantera Androids OpenGL-livscykel. Dessa metoder måste anropas direkt från den inneslutna aktiviteten. Om du vill anropa kartkontrollens livscykelmetoder korrekt måste du åsidosätta följande livscykelmetoder i aktiviteten som innehåller kartkontrollen. Anropa respektive kartkontrollmetod.

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Redigera filen **MainActivity.java** enligt följande:
    
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

Om du kör programmet läses kartkontrollen in som i följande bild.

<center>

![Enkla Azure-kartor](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Observera att Azure Maps-kontrollen stöder att zooma ut mer och ger mer av en världsbild.

> [!TIP]
> Om du använder en Android-emulator på en Windows-dator kanske kartan inte återges på grund av konflikter med OpenGL och programvaruaccelererad grafikåtergivning. Följande har för vissa personer arbetat för att lösa problemet. Öppna AVD-hanteraren och välj den virtuella enheten som ska redigeras. Bläddra nedåt på panelen **Verifiera konfiguration.** I avsnittet **Emulerad prestanda** anger du alternativet **Grafik** till **Maskinvara**.

## <a name="localizing-the-map"></a>Lokalisera kartan

Lokalisering är viktigt om målgruppen är spridd över flera länder eller talar olika språk.

**Före: Google Maps**

Lägg till följande `onCreate` kod i metoden för att ange språket på kartan. Koden måste läggas till innan du anger kontextvyn för kartan. Språkkoden "fr" begränsar språket till franska.

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Här är ett exempel på Google Maps med språket inställt på "fr".

<center>

![Anpassning av Google Maps](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**Efter: Azure Maps**

Azure Maps innehåller tre olika sätt att ange språket och den regionala vyn på kartan. Det första alternativet är att skicka språk- `AzureMaps` och regionalvyinformation till klassen. Det här alternativet `setLanguage` `setView` använder statiska metoder och metoder globalt. Det innebär att standardspråket och den regionala vyn anges i alla Azure Maps-kontroller som läses in i appen. I det här exemplet anges franska med språkkoden "fr-FR".

```java
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

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Det tredje alternativet är att programmera språk- `setStyle` och regionalkartvyn med hjälp av kartmetoden. Det här alternativet uppdaterar språk- och regionalvyn när koden körs.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Här är ett exempel på Azure Maps med språket inställt på "fr-FR".

<center>

![Lokalisering av Azure Maps](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

Gå igenom hela listan över [språk som stöds](supported-languages.md).

## <a name="setting-the-map-view"></a>Ställa in kartvyn

Dynamiska kartor i både Azure Maps och Google Maps kan flyttas programmässigt till nya geografiska platser genom att anropa lämpliga metoder. Låt oss göra satellitens flygbilder, centrera kartan över en plats med koordinater och ändra zoomnivån. I det här exemplet använder vi latitud: 35.0272, longitud: -111.0225 och zoomnivå 15.

**Före: Google Maps**

Kameran i Google Maps kartkontroll kan programmässigt `moveCamera` flyttas med hjälp av metoden. Med `moveCamera` metoden kan du ange mitten av kartan och en zoomnivå. Metoden `setMapType` ändrar vilken typ av karta som ska visas.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Ställ vy över Google Maps](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

> [!NOTE]
> Google Maps använder paneler som är 256 pixlar i dimensioner medan Azure Maps använder en större 512-pixelpanel. Detta minskar antalet nätverksbegäranden som krävs av Azure Maps för att läsa in samma kartområde som Google Maps. Om du vill uppnå samma synliga område som en karta i Google Maps måste du subtrahera zoomnivån som används i Google Maps med en när du använder Azure Maps. 

**Efter: Azure Maps**

Som nämnts tidigare, för att uppnå samma synliga område i Azure Maps subtrahera zoomnivån som används i Google Maps med en. Använd i så fall en zoomnivå på 14.

Den första kartvyn kan anges i XML-attribut på kartkontrollen.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

Kartvyn kan programmeras med `setCamera` hjälp `setStyle` av kartor och metoder.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Uppsättning vy för Azure Maps](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**Ytterligare resurser:**

- [Kartformat som stöds](supported-map-styles.md)

## <a name="adding-a-marker"></a>Lägga till en markör

Punktdata återges ofta med hjälp av en bild på kartan. Dessa bilder kallas markörer, kartnålar, stift eller symboler. I följande exempel återges punktdata som markörer på kartan vid latitud: 51,5, longitud: -0,2.

**Före: Google Maps**

Med Google Maps läggs markörer `addMarker` till med hjälp av kartmetoden.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![Google Maps markör](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**Efter: Azure Maps**

I Azure Maps återges punktdata på kartan genom att först lägga till data i en datakälla. Sedan ansluter datakällan till ett symbollager. Datakällan optimerar hanteringen av rumsliga data i kartkontrollen. Symbollagret anger hur punktdata ska återges som en bild eller text.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

<center>

![Markören Azure Maps](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Lägga till en anpassad markör

Anpassade bilder kan användas för att representera punkter på en karta. Kartan i exemplen nedan använder en anpassad bild för att visa en punkt på kartan. Punkten är vid latitud: 51,5 och longitud: -0,2. Ankaret förskjuter markörens position så att pushpinikonens punkt ligger i linje med rätt position på kartan.

<center>

![gul pushpin-bild](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

I båda exemplen läggs ovanstående bild till i den drawable mappen för appresurserna.

**Före: Google Maps**

Med Google Maps kan anpassade bilder användas för markörer. Läs in anpassade bilder `icon` med hjälp av markeringsalternativet. Om du vill justera bildens punkt `anchor` mot koordinaten använder du alternativet. Ankaret är relativt till bildens dimensioner. I det här fallet är ankaret 0,2 enheter breda och 1 enhet hög.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.ylw_pushpin))
    .anchor(0.2f, 1f));
}
```

<center>

![Anpassad markör för Google Maps](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**Efter: Azure Maps**

Symbollager i Azure Maps stöder anpassade avbildningar, men först måste avbildningen läsas in till kartresurserna och tilldelas ett unikt ID. Sedan måste symbollagret referera till detta ID. Förskjut symbolen för att justera mot rätt `iconOffset` punkt på bilden med hjälp av alternativet. Ikonförseningen är i bildpunkter. Som standard är förskjutningen relativ till bildens nedersta mittpunkt, men `iconAnchor` det här förskjutningsvärdet kan justeras med alternativet. I det `iconAnchor` här `"center"`exemplet anges alternativet till . Den använder en ikon förskjutning för att flytta bilden fem pixlar åt höger och 15 pixlar upp för att justera med punkten i pushpin bilden.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.ylw_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

<center>

![Anpassad markör för Azure Maps](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>Lägga till en polylinje

Polylinjer används för att representera en linje eller bana på kartan. Följande exempel visar hur du skapar en streckad polylinje på kartan.

**Före: Google Maps**

Med Google Maps kan du `PolylineOptions` återge en polylinje med hjälp av klassen. Lägg till polyline till `addPolyline` kartan med hjälp av metoden. Ange linjefärg med `color` alternativet. Ange linjebredden `width` med alternativet. Lägg till en linjestrecksmatris med `pattern` alternativet.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

<center>

![Google Maps polyline](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**Efter: Azure Maps**

I Azure Maps anropas `LineString` polylinjer eller `MultiLineString` objekt. Lägg till dessa objekt i en datakälla och återge dem med hjälp av ett linjelager. Ange linjebredden `strokeWidth` med alternativet. Lägg till en linjestrecksmatris med `strokeDashArray` alternativet.

Linjebredden och instrumentpanelen "pixel"-enheter i Azure Maps Web SDK är desamma som i Google Maps-tjänsten. Båda accepterar samma värden för att ge samma resultat.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

<center>

![Polylinje för Azure Maps](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>Lägga till en polygon

Polygoner används för att representera ett område på kartan. I nästa exempel visas hur du skapar en polygon. Denna polygon bildar en triangel baserad på kartans mittkoordinat.

**Före: Google Maps**

Med Google Maps kan du `PolygonOptions` rendera en polygon med hjälp av klassen. Lägg till polygonen på `addPolygon` kartan med hjälp av metoden. Ange fyllnings- och `fillColor` linjefärgerna med alternativen respektive. `strokeColor` Ange linjebredden `strokeWidth` med alternativet.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

<center>

![Google Maps polygon](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**Efter: Azure Maps**

I Azure Maps `Polygon` `MultiPolygon` lägger du till och objekt i en datakälla och återger dem på kartan med hjälp av lager. Återge området för en polygon i ett polygonskikt. Återge dispositionen för en polygon med hjälp av ett linjelager. Ange linjefärg och bredd `strokeColor` `strokeWidth` med hjälp av alternativen och alternativen.

Linjebredden och streckmatrisenheterna "pixel" i Azure Maps Web SDK justeras med respektive enheter i Google Maps. Båda accepterar samma värden och ger samma resultat.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

<center>

![Azure Maps polygon](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>Överlagra ett panellager

 Använd Panellager för att överlagra lagerbilder som har delats upp i mindre kaklade bilder, som ligger i linje med kartkrätande systemet. Den här metoden är ett vanligt sätt att lägga över lagerbilder eller stora datauppsättningar. Panellager kallas bildöverlägg i Google Maps.

Följande exempel överlägg ett väderradarkakellager från Iowa Environmental Mesonet från Iowa State University. Brickorna är 256 pixlar stora.

**Före: Google Maps**

Med Google Maps kan ett panellager läggas ovanpå kartan. Använd `TileOverlayOptions` klassen. Lägg till panellagret på `addTileLauer` kartan med hjälp av metoden. För att göra brickorna `transparency` halvtransparenta är alternativet inställt på 0,2 eller 20 % genomskinligt.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

<center>

![Panellager för Google Maps](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**Efter: Azure Maps**

Ett panellager kan läggas till på kartan på ett liknande sätt som alla andra lager. En formaterad URL som har platshållare för x, y och zoom. `{x}`, `{y}` `{z}` används för att tala om för lagret var du ska komma åt panelerna. Panellager i Azure Maps `{quadkey}` `{bbox-epsg-3857}`stöder `{subdomain}` , och platshållare. För att göra panellagret halvtransparent används ett opacitetsvärde på 0,8. Opacitet och genomskinlighet, även om liknande, använder inverterade värden. Om du vill konvertera mellan båda alternativen subtraherar du deras värde från nummer ett.

> [!TIP]
> I Azure Maps är det praktiskt att återge lager under andra lager, inklusive baskartlager. Det är också ofta önskvärt att återge panellager under kartetiketterna så att de är lätta att läsa. Metoden `map.layers.add` tar en andra parameter som är id för det lager där du kan infoga det nya lagret nedan. Om du vill infoga ett panellager under kartetiketterna kan följande kod användas:`map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

<center>

![Azure Maps-panellager](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Visa trafik

Både Azure Maps och Google Maps har alternativ för att lägga över trafikdata.

**Före: Google Maps**

Med Google Maps kan trafikflödesdata läggas ovanpå kartan genom att gå emot `setTrafficEnabled` kartans metod.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Google Maps trafik](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**Efter: Azure Maps**

Azure Maps innehåller flera olika alternativ för att visa trafik. Trafikincidenter, till exempel avstängda vägar och olyckor, kan visas som ikoner på kartan. Trafikflöde och färgkodade vägar kan överlagras på kartan. Färgerna kan ändras så att de visas i förhållande till den bokförda hastighetsgränsen, i förhållande till den normala förväntade fördröjningen eller den absoluta fördröjningen. Incidentdata i Azure Maps uppdateras varje minut och flödesdata uppdateras varannan minut.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Azure Maps trafik](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Maps Android SDK.

> [!div class="nextstepaction"]
> [Så här använder du Kartkontrollen för Android](how-to-use-android-map-control-library.md)
