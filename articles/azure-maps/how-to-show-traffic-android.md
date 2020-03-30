---
title: Visa trafikdata på Android Map | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du visar trafikdata på en karta med Hjälp av Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e5611eeb08ac370e12cf452d57a87e449fbd80da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335381"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Visa trafikdata på kartan med Azure Maps Android SDK

Flödesdata och incidentersdata är de två typer av trafikdata som kan visas på kartan. Den här guiden visar hur du visar båda typerna av trafikdata. Uppgifter om tillbud består av punkt- och linjebaserade data för bland annat konstruktioner, avstängda vägar och olyckor. Flödesdata visar mått om trafikflödet på vägen.

## <a name="prerequisites"></a>Krav

Innan du kan visa trafik på kartan måste du [skapa ett Azure-konto](quick-demo-map-app.md#create-an-account-with-azure-maps)och [skaffa en prenumerationsnyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account). Sedan måste du installera [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) och läsa in en karta.

## <a name="incidents-traffic-data"></a>Trafikdata för incidenter 

Du måste importera följande bibliotek för `setTraffic` att `incidents`ringa och:

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 Följande kodavsnitt visar hur du visar trafikdata på kartan. Vi skickar ett booleskt värde till `incidents` metoden `setTraffic` och skickar det till metoden. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>Flödestrafikdata

Du måste först importera följande bibliotek för `setTraffic` `flow`att ringa och:

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

Använd följande kodavsnitt för att ange trafikflödesdata. I likhet med koden i föregående avsnitt skickar `flow` vi metodens returvärde till `setTraffic` metoden. Det finns fyra värden som `flow`kan skickas till `flow` och varje värde utlöses för att returnera respektive värde. Returvärdet för `flow` skickas sedan som argument `setTraffic`till . Se tabellen nedan för dessa fyra värden:

| | |
| :-- | :-- |
| TrafficFlow.NONE | Visar inte trafikdata på kartan |
| TrafficFlow.RELATIVE | Visar trafikdata som är i förhållande till vägens hastighet fritt flöde |
| TrafficFlow.RELATIVE_DELAY | Visar områden som är långsammare än den genomsnittliga förväntade fördröjningen |
| TrafficFlow.ABSOLUTE | Visar den absoluta hastigheten för alla fordon på vägen |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Visa incidenttrafikdata genom att klicka på en funktion

Om du vill hämta incidenter för en viss funktion kan du använda koden nedan. När du klickar på en funktion söker kodlogiken efter incidenter och skapar ett meddelande om incidenten. Ett meddelande visas längst ned på skärmen med information.

1. Först måste du redigera **res > layout > activity_main.xml**, så att det ser ut som den nedan. Du kan `mapcontrol_centerLat`ersätta `mapcontrol_centerLng`, `mapcontrol_zoom` och med önskade värden. Kom ihåg att zoomnivån är ett värde mellan 0 och 22. På zoomnivå 0 passar hela världen på en enda bricka.

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

2. Lägg till följande kod i filen **MainActivity.java.** Paketet ingår som standard, så se till att du håller ditt paket högst upp.

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

3. När du har införlivat ovanstående kod i ditt program kan du klicka på en funktion och se information om trafikincidenter. Beroende på latitud, longitud och de zoomnivåvärden som du använde i **filen activity_main.xml** visas resultat som liknar följande bild:

   <center>

   ![Incident-trafik-på-kartan](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>Nästa steg

Visa följande guider om du vill veta hur du lägger till mer data på kartan:

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Lägga till ett panelskikt](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Lägga till former i Android-kartan](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Visa funktionsinformation](display-feature-information-android.md)
