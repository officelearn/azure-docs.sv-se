---
title: 'Självstudie: Migrera en Android-app | Microsoft Azure Maps'
description: Så här migrerar du en Android-app från Google Maps till Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6e54d8ea44b6c322f311cc1baeb6ca3ab6715aee
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989968"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Migrera en Android-app från Google Maps

Azure Maps Android SDK har ett API-gränssnitt som liknar webb-SDK: n. Om du har utvecklat en av dessa SDK: er gäller många av samma koncept, bästa praxis och arkitekturer. Du bör lätt kunna överföra dina kunskaper från en till en annan.

Azure Maps Android SDK stöder en lägsta Android-version av API 21: Android-5.0.0 (Lollipop).

Alla exempel finns i Java, men Kotlin kan också användas med Azure Maps Android SDK.

Mer information om hur du utvecklar med Android SDK genom att Azure Maps finns i [instruktions guiderna för Azure Maps Android SDK](how-to-use-android-map-control-library.md).

## <a name="load-a-map"></a>Läsa in en karta

Att läsa in en karta i en Android-app med Google eller Azure Maps består av många av de samma stegen. När du använder något av SDK: n måste du:

- Hämta en API-eller prenumerations nyckel för att få åtkomst till någon av plattformarna.
- Lägg till en del XML i en aktivitet för att ange var kartan ska återges och hur den ska anges.
- Vidarebefordra alla livs cykel metoder från den aktivitet som innehåller kart visningen till motsvarande i Map-klassen. I synnerhet måste du åsidosätta följande metoder:
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Vänta tills kartan är klar innan du försöker få åtkomst till den program mässigt.

**Före: Google Maps**

Om du vill visa en karta med Google Maps SDK för Android gör du så här:

1.  Se till att Google Play-tjänsterna är installerade.
2.  Lägg till ett beroende för Google Maps-tjänsten till modulens **gradle. Build** -fil: 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Lägg till en Google Maps API-nyckel i program avsnittet i filen **Google\_Maps\_API. XML** :
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  Lägg till ett kart fragment i huvud aktiviteten:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  I filen MainActivity. java måste du lägga till importer för Google Maps SDK. Vidarebefordra alla livs cykel metoder från den aktivitet som innehåller kart visningen till motsvarande i Map-klassen. En `MapView` instans kan hämtas från MAP-fragment med hjälp av metoden `getMapAsync(OnMapReadyCallback)`. `MapView` initierar automatiskt Maps-systemet och vyn. Redigera filen **MainActivity. java** enligt följande:

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

När du körde ett program läses kart kontrollen in enligt följande.

<center>

![enkla Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**Efter: Azure Maps**

Om du vill visa en karta som använder Azure Maps SDK för Android måste du utföra följande steg:

1. Öppna filen **build. gradle** på den översta nivån och Lägg till följande kod i avsnittet **alla projekt** block:

    ```JAVA
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Uppdatera **appen/build. gradle** och Lägg till följande kod i den:
    
    1. Kontrol lera att projektets **minSdkVersion** är i API 21 eller högre.

    2. Lägg till följande kod i Android-avsnittet:

        ```JAVA
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Uppdatera beroende blocket och Lägg till en ny implementerings beroende linje för den senaste Azure Maps Android SDK:

        ```JAVA
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Azure Maps Android SDK regelbundet uppgraderas och förbättras. Du kan se dokumentationen för att [komma igång med Android Map-kontroll](how-to-use-android-map-control-library.md) för att hämta det senaste Azure Maps versions numret. Du kan också ställa in versions numret från "0,2" till "0 +" för att koden alltid ska peka på den senaste versionen.
    
    4. Gå till **filen** i verktygsfältet och klicka sedan på **Synkronisera projekt med Gradle-filer**.
3. Lägg till ett kart fragment i huvud aktiviteten (resurser \> layout \> aktivitet\_main. xml):
    
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

    Att ställa in autentiseringsinformationen i `AzureMaps`-klassen globalt med `setSubscriptionKey` eller `setAadProperties` metoder gör det så att du inte behöver lägga till autentiseringsinformation i alla vyer. 

    Kart kontrollen innehåller sina egna livs cykel metoder för hantering av Android: s OpenGL-livscykel, som måste anropas direkt från den som innehåller aktiviteten. För att kunna anropa kart kontrollens livs cykel metoder måste du åsidosätta följande livs cykel metoder i aktiviteten som innehåller kart kontrollen och anropa respektive kart kontroll metod. 

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Redigera filen **MainActivity. java** enligt följande:
    
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

Om du kör programmet kommer kart kontrollen att läsas in enligt följande.

<center>

![enkla Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Observera att Azure Maps kontrollen har stöd för att zooma ut mer och innehåller mer av en världs visning.

> [!TIP]
> Om du använder en Android-emulator i Windows kan kartan inte återges på grund av konflikter med OpenGL och accelererad bild åter givning i program vara. Följande har bearbetats för att lösa det här problemet. Öppna AVD Manager och välj den virtuella enhet som ska redige ras. Rulla ned panelen **Verifiera konfiguration** . I avsnittet **emulerade prestanda** ställer du in alternativet **Graphics** på **maskin vara**.

## <a name="localizing-the-map"></a>Lokalisera kartan

Om din mål grupp är uppdelad i flera länder eller talar olika språk är det viktigt att lokaliseringen är viktig.

**Före: Google Maps**

Språket för kartan kan anges i `onCreate`-metoden för huvud aktiviteten genom att lägga till följande kod. Du måste lägga till koden innan du anger kartans sammanhang. Följande begränsar språket till franska med hjälp av språk koden "fr".

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

![Google Maps lokalisering](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**Efter: Azure Maps**

Azure Maps tillhandahåller tre olika sätt att ange språk och regional vy för kartan. Det första alternativet är att skicka information om språket och den regionala vyn till klassen `AzureMaps` med hjälp av statiska `setLanguage` och `setView` metoder globalt. Detta ställer in standard språk och regional vy över alla Azure Maps kontroller som läses in i din app. Följande begränsar språket till franska med hjälp av språk koden "fr-FR".

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

Det andra alternativet är att skicka språket och Visa information i kart kontrollens XML-fil.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Det tredje alternativet är att program mässigt ange språket och den regionala vyn för kartan med hjälp av metoden Maps `setStyle`. Det här alternativet kan ställas in när som helst för att ändra språket och den regionala vyn för kartan.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Här är ett exempel på Azure Maps med språket inställt på "fr-FR".

<center>

![Azure Maps lokalisering](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

En fullständig lista över språk som stöds och nationella vyer beskrivs [här](supported-languages.md).

## <a name="setting-the-map-view"></a>Ställa in Map-vyn

Dynamiska kartor i både Azure Maps och Google Maps kan flyttas program mässigt till nya geografiska platser genom att anropa lämpliga metoder. I exemplen nedan visas hur du gör kartan för att Visa satellit antenn bilder, centrera kartan över en plats med koordinater (latitud: 35,0272, longitud:-111,0225) och ändra zoomnings nivån till 15 i Google Maps.

> [!NOTE]
> Google Maps använder paneler som är 256 pixlar i dimensioner medan Azure Maps använder en större 512 pixel panel. Detta minskar antalet nätverks begär Anden som krävs av Azure Maps för att läsa in samma mappnings område som Google Maps. Men på grund av hur panelbaserade pyramider fungerar i kart kontroller, innebär de större panelerna i Azure Maps att du kan uppnå samma visnings bara område som en karta i Google Maps, och du måste subtrahera zoomnings nivån som används i Google Maps med 1 när du använder Azure Maps. 

**Före: Google Maps**

Kameran med Google Maps kart kontroll kan flyttas program mässigt med hjälp av metoden `moveCamera`, där du kan ange kartans centrum och zoomnings nivå. `setMapType`-metoden kan användas för att ändra vilken typ av karta som visas.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Google Maps set View](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

**Efter: Azure Maps**

Som tidigare nämnts kan du, om du vill uppnå samma visnings område i Azure Maps, subtrahera den zoomnivå som används i Google Maps med en, i det här fallet använder du en zoomnings nivå på 14.

Den inledande kart visningen kan anges i XML-attribut på kart kontrollen.

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

Map-vyn kan uppdateras program mässigt med hjälp av Maps-`setCamera` och `setStyle` metoder.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Azure Maps ange vy](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**Ytterligare resurser:**

- [Kart format som stöds](supported-map-styles.md)

## <a name="adding-a-marker"></a>Lägga till en markör

Punkt data återges ofta på kartan med en bild på kartan. Dessa bilder kallas ofta för markörer, kartnålar, stift eller symboler. I följande exempel återges punkt data som markörer på kartan vid (latitud: 51,5, longitud:-0,2).

**Före: Google Maps**

Med Google Maps läggs markörerna till med hjälp av metoden Maps `addMarker`.

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

I Azure Maps kan punkt data återges på kartan genom att först lägga till data i en data källa. Sedan ansluter den data källan till ett symbol lager. Data källan optimerar hanteringen av spatialdata i kart kontrollen. Symbol lagret anger hur punkt data ska återges med hjälp av som en bild och/eller text.

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

![Azure Maps markör](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Lägga till en anpassad markör

Anpassade bilder kan användas för att representera punkter på en karta. I kartan i exemplen nedan används en anpassad bild för att visa en punkt på kartan. Punkten är på latitud: 51,5 och longitud:-0,2. Kartan förskjuter markörens position så att punkten på kartnålen justeras mot rätt placering på kartan.

<center>

![gul kartnåls bild](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
YLW\_kartnål. png</center>

I båda exemplen läggs bilden ovan till i den skrivbara mappen i Apps-resurserna.

**Före: Google Maps**

Med Google Maps kan anpassade avbildningar användas för markörer. Läs in anpassade bilder med markörens `icon` alternativ. Använd alternativet `anchor` för att justera bildens punkt till koordinaten. Fäst punkten i förhållande till bildens dimensioner, i det här fallet 0,2 enheter bred och 1 enhet hög.

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

![Google Maps-anpassad markör](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**Efter: Azure Maps**

Symbol lager i Azure Maps stöder anpassade bilder, men först måste bilden läsas in i kart resurserna och tilldelas ett unikt ID. Symbol lagret kan sedan referera till detta ID. Förskjuter symbolen så att den överensstämmer med rätt punkt på bilden med alternativet `iconOffset`. Ikonens förskjutning är i bild punkter. Som standard är förskjutningen relativ till längst ned i mitten av bilden, men detta förskjutnings värde kan justeras med alternativet `iconAnchor`. I det här exemplet anges `iconAnchor` alternativet för att `"center"` och använder en ikon förskjutning för att flytta bilden fem pixlar till höger och 15 bild punkter upp för att passa med punkten för den kartnålade bilden.

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

![Azure Maps anpassad markör](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>Lägga till en polyline

Polylinjer används för att representera en linje eller bana på kartan. I följande exempel visas hur du skapar en streckad polyline på kartan.

**Före: Google Maps**

Med Google Maps kan en polyline skapas med hjälp av klassen `PolylineOptions` och läggas till i kartan med hjälp av metoden `addPolyline`. Linje färgen kan anges med alternativet `color`, linje bredden anges med alternativet width och en streck mat ris kan anges med alternativet `pattern`.

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

I Azure Maps kallas polystreck `LineString` eller `MultiLineString` objekt. Dessa objekt kan läggas till i en data källa och återges med hjälp av ett linje lager. Unit width-och streck mat ris-matrisen "pixel" överensstämmer med Azure Maps Web SDK i som använder samma värden i båda SDK: erna ger samma resultat.

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

![Azure Maps polylinje](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>Lägga till en polygon

Polygoner används för att representera ett område på kartan. I följande exempel visas hur du skapar en polygon som utgör en triangel baserat på kartans centrum-koordinat.

**Före: Google Maps**

Med Google Maps kan en polygon skapas med `PolygonOptions`-klassen och läggas till i kartan med hjälp av metoden `addPolygon`. Du kan ställa in fyllnings-och linje färgerna med alternativet `fillColor` och `strokeColor`, linje bredden anges med alternativet `strokeWidth`.

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

I Azure Maps kan `Polygon` och `MultiPolygon` objekt läggas till i en data källa och återges på kartan med hjälp av lager. Ytan i en polygon kan återges i ett polygon-lager. Dispositionen för en polygon kan återges med hjälp av ett linje lager. Unit width-och streck mat ris-matrisen "pixel" överensstämmer med Azure Maps Web SDK i som använder samma värden i båda SDK: erna ger samma resultat.

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

## <a name="overlay-a-tile-layer"></a>Täcka över ett panel lager

 Med panel lager kan du täcka över lager bilder som har delats upp i mindre klickbara bilder, som överensstämmer med Kartornas placerings system. Den här metoden är ett vanligt sätt att täcka lager bilder eller stora data mängder. Panel lager kallas bild överlägg i Google Maps.

I följande exempel överlappar ett väder radar panels lager från Iowa Environment-Mesonet för Iowa State University. Panelerna har storleken 256 bild punkter.

**Före: Google Maps**

Med Google Maps kan ett panel lager överlappa ovanpå kartan använda klassen `TileOverlayOptions` och läggas till i kartan med hjälp av metoden `addTileLauer`. För att göra brickorna halv genomskinliga är `transparency` alternativet inställt på 0,2 eller 20% transparent.

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

![Google Maps panels lager](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**Efter: Azure Maps**

Ett panel lager kan läggas till i kartan på samma sätt som andra lager. En formaterad URL med plats hållare för x, y och zoomning; `{x}`är `{y}``{z}` används för att berätta för lagret var du ska få åtkomst till panelerna. Panel lager i Azure Maps också stöd för `{quadkey}`, `{bbox-epsg-3857}`och `{subdomain}` plats hållare. Om du vill göra panel lagret halv genomskinligt används värdet 0,8 för opacitet. Ogenomskinlighet och genomskinlighet, men Använd inverterade värden. Om du vill konvertera mellan dem drar du värdet från talet ett.

> [!TIP]
> I Azure Maps lager kan enkelt återges under andra lager, inklusive bas kart skikt. Det är ofta önskvärt att återge panel lager under kart etiketterna så att de är lätta att läsa. Metoden `map.layers.add` tar en andra parameter som är ID: t för det lager där det nya lagret ska infogas. Följande kod kan användas för att infoga ett panel lager under kart etiketterna: `map.layers.add(myTileLayer, "labels");`

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

![Azure Maps panels lager](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Visa trafik

Trafik data kan anges i både Azure Maps och Google Maps.

**Före: Google Maps**

Med Google Maps kan trafik flödes data översättas ovanpå kartan genom att de skickas till kartans `setTrafficEnabled` metod.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Google Maps-trafik](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**Efter: Azure Maps**

Azure Maps tillhandahåller flera olika alternativ för att Visa trafik. Trafik incidenter, till exempel väg stängningar och haverier kan visas som ikoner på kartan. Trafikflöde, färgkodade vägar, kan översättas på kartan. Färgerna kan ändras så att de visas i förhållande till den angivna hastighets gränsen, i förhållande till den normala förväntade fördröjningen eller den absoluta fördröjningen. Incident data i Azure Maps uppdateras varje minut och Flow-data uppdateras varannan minut.

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
> [Använda Android Map-kontrollen](how-to-use-android-map-control-library.md)
