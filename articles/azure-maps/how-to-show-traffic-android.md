---
title: Visa trafik data på Android-karta | Microsoft Azure Maps
description: I den här artikeln lär du dig hur du visar trafik data på en karta med hjälp av Microsoft Azure Maps Android SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/25/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5f7e67d159c2b7dea3ebac7fd4d0856f508cb298
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532762"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Visa trafik data på kartan med Azure Maps Android SDK

Flödes data och incident data är de två typer av trafik data som kan visas på kartan. Den här guiden visar hur du visar båda typerna av trafik data. Incident data består av punkt-och linjebaserade data för sådant som konstruktioner, väg stängningar och olyckor. Flow-data visar mått för trafik flödet på resan.

## <a name="prerequisites"></a>Krav

1. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel.
3. Hämta och installera [Azure Maps Android SDK](./how-to-use-android-map-control-library.md).

## <a name="incidents-traffic-data"></a>Incident trafik data

Du måste importera följande bibliotek för att kunna anropa `setTraffic` och `incidents` :

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 Följande kodfragment visar hur du visar trafik data på kartan. Vi skickar ett booleskt värde till `incidents` -metoden och skickar det till- `setTraffic` metoden. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>Flöda trafik data

Du måste först importera följande bibliotek för att kunna anropa `setTraffic` och `flow` :

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

Använd följande kodfragment för att ställa in trafik flödes data. Precis som koden i föregående avsnitt skickar vi returvärdet för `flow` metoden till- `setTraffic` metoden. Det finns fyra värden som kan skickas till `flow` och varje värde utlöser `flow` sig för att returnera respektive värde. Returvärdet från skickas `flow` sedan som argumentet till `setTraffic` . Se tabellen nedan för följande fyra värden:

|Flödes värde | Beskrivning|
| :-- | :-- |
| TrafficFlow. NONE | Visar inte trafik data på kartan |
| TrafficFlow. relativ | Visar trafik data som är relativa till vägens fria flödes hastighet |
| TrafficFlow.RELATIVE_DELAY | Visar områden som är långsammare än det genomsnittliga förväntad fördröjning |
| TrafficFlow. absolut | Visar den absoluta hastigheten för alla fordon på väg |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Visa incident trafik data genom att klicka på en funktion

Du kan använda koden nedan för att få incidenter för en speciell funktion. När du klickar på en funktion söker kod logiken efter incidenter och skapar ett meddelande om incidenten. Ett meddelande visas längst ned på skärmen med information.

1. Först måste du redigera `res > layout > activity_main.xml` så att det ser ut som på samma sätt som den nedan. Du kan ersätta `mapcontrol_centerLat` , `mapcontrol_centerLng` , och `mapcontrol_zoom` med önskade värden. Åter kallelse är zoomnings nivån ett värde mellan 0 och 22. På zoomnings nivå 0 passar hela världen på en enda panel.

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
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. Lägg till följande kod i **MainActivity. java** -filen. Paketet ingår som standard, så se till att du behåller paketet överst.

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
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

3. När du har införlivat ovanstående kod i programmet kan du klicka på en funktion och se information om trafik incidenter. Beroende på latitud-, longitud-och zoomnings nivå värden som du använde i **activity_main.xml** -filen visas resultat som liknar följande bild:


    ![Incident-trafik-på-kartan](./media/how-to-show-traffic-android/android-traffic.png)


## <a name="next-steps"></a>Nästa steg

Se följande guider för att lära dig hur du lägger till mer data i kartan:

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Lägga till ett panelskikt](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Lägga till former i Android-kartor](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Visa funktionsinformation](display-feature-information-android.md)