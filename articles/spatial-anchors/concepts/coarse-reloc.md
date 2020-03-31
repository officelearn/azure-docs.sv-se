---
title: Grov omlokalisering
description: Lär dig mer om hur du använder Grov relocalization för att hitta ankare nära dig.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4c1604eaad1ebdedf6a360a647fe5b9f95c829c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844402"
---
# <a name="coarse-relocalization"></a>Grov omlokalisering

Grov omlokalisering är en funktion som ger ett första svar på frågan: *Var är min enhet nu / Vilket innehåll ska jag observera?* Svaret är inte exakt, men i stället är i form: *Du är nära dessa ankare, försök att hitta en av dem*.

Grov relocalization fungerar genom att associera olika sensoravläsningar på enheten med både skapande och frågesättning av ankare. För utomhusscenarier är sensordata vanligtvis enhetens GPS-position (Global Positioning System). När GPS inte är tillgängligt eller opålitligt (t.ex. inomhus) består sensordata av wi-fi-åtkomstpunkterna och Bluetooth-fyrarna inom räckhåll. Alla insamlade sensordata bidrar till att upprätthålla ett rumsligt index som detta används av Azure Spatial Anchors för att snabbt bestämma de fästpunkter som finns inom cirka 100 meter från din enhet.

Den snabba uppslagningen av ankare som möjliggörs av grov omlokalisering förenklar utvecklingen av program som backas upp av världsomstliga samlingar av (säg miljontals geodistribuerade) ankare. Komplexiteten i ankarhantering är alla dolda, så att du kan fokusera mer på din awesome ansökan logik. Alla ankare tunga lyft görs för dig bakom kulisserna av Azure Spatial Anchors.

## <a name="collected-sensor-data"></a>Insamlade sensordata

Sensordata som du kan skicka till ankartjänsten är något av följande:

* GPS-position: latitud, longitud, höjd.
* Signalstyrkan hos WiFi-åtkomstpunkter inom räckhåll.
* Signalstyrkan hos Bluetooth-fyrar inom räckhåll.

I allmänhet måste ditt program skaffa enhetsspecifika behörigheter för att komma åt GPS-, WiFi- eller BLE-data. Dessutom är vissa av sensordata ovan inte tillgängliga avsiktligt på vissa plattformar. För att ta hänsyn till dessa situationer är insamlingen av sensordata valfri och inaktiveras som standard.

## <a name="set-up-the-sensor-data-collection"></a>Ställa in datainsamlingen för sensorn

Låt oss börja med att skapa en sensor fingeravtrycksleverantör och göra sessionen medveten om det:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[ObjC (olika)](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="java"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

Därefter måste du bestämma vilka sensorer du vill använda för grov omlokalisering. Det här beslutet är specifikt för det program du utvecklar, men rekommendationerna i följande tabell bör ge dig en bra utgångspunkt:


|             | Inomhus | Utomhus |
|-------------|---------|----------|
| Gps         | Av | På |
| WiFi        | På | På (valfritt) |
| BLE-fyrar | På (valfritt med varningar, se nedan) | Av |


### <a name="enabling-gps"></a>Aktivera GPS

Förutsatt att ditt program redan har behörighet att komma åt enhetens GPS-position kan du konfigurera Azure Spatial Anchors så att det används:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[ObjC (olika)](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

När du använder GPS i ditt program, tänk på att de avläsningar som tillhandahålls av hårdvaran är vanligtvis:

* asynkrona och lågfrekventa (mindre än 1 Hz).
* opålitliga / bullriga (i genomsnitt 7 m standardavvikelse).

I allmänhet kommer både enhetsoperativsystemet och Azure Spatial Anchors att göra en del filtrering och extrapolering på den råa GPS-signalen i ett försök att minska dessa problem. Denna extra-bearbetning kräver ytterligare tid för konvergens, så för bästa resultat bör du försöka:

* skapa en sensorfingeravtrycksleverantör så tidigt som möjligt i din applikation
* hålla sensorns fingeravtrycksleverantör vid liv mellan flera sessioner
* dela sensorns fingeravtrycksleverantör mellan flera sessioner

Om du planerar att använda sensorns fingeravtrycksleverantör utanför en ankarsession kontrollerar du att du startar den innan du begär sensoruppskattningar. Följande kod tar till exempel hand om att uppdatera enhetens position på kartan i realtid:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objc"></a>[ObjC (olika)](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="java"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>Aktivera WiFi

Om du antar att ditt program redan har behörighet att komma åt enhetens WiFi-tillstånd kan du konfigurera Azure Spatial Anchors så att det används:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[ObjC (olika)](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

När du använder Trådlöst nätverk i ditt program bör du tänka på att de avläsningar som tillhandahålls av maskinvaran vanligtvis är:

* asynkrona och lågfrekventa (mindre än 0,1 Hz).
* potentiellt strypt på OS-nivå.
* opålitliga / bullriga (i genomsnitt 3-dBm standardavvikelse).

Azure Spatial Anchors försöker skapa en filtrerad wi-fi-signalstyrkasmappning under en session i ett försök att minska dessa problem. För bästa resultat bör du försöka:

* skapa sessionen i god tid innan du placerar det första ankaret.
* hålla sessionen vid liv så länge som möjligt (det vill än skapa alla ankare och fråga i en session).

### <a name="enabling-bluetooth-beacons"></a>Aktivera Bluetooth-beacons

Om du antar att ditt program redan har behörighet att komma åt enhetens Bluetooth-tillstånd kan du konfigurera Azure Spatial Anchors så att det används:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[ObjC (olika)](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

Beacons är vanligtvis mångsidiga enheter, där allt - inklusive UUIDs och MAC-adresser - kan konfigureras. Den här flexibiliteten kan vara problematisk för Azure Spatial Anchors eftersom den anser att beacons identifieras unikt av deras UUIDs. Om du inte säkerställer denna unika kommer sannolikt att orsaka rumsliga maskhål. För bästa resultat bör du:

* tilldela unika UUID:er till dina beacons.
* distribuera dem - vanligtvis i ett vanligt mönster, till exempel ett rutnät.
* skicka listan över unika beacon UUIDs till sensorns fingeravtrycksleverantör:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[ObjC (olika)](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="java"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Azure Spatial Anchors spårar endast Bluetooth-beacons som finns i listan med kända beacon proximity UUIDs. Skadliga beacons programmerade att ha tillåta-listade UUIDs kan fortfarande negativt påverka kvaliteten på tjänsten ändå. Därför bör du bara använda beacons i kurerade utrymmen där du kan styra deras distribution.

## <a name="querying-with-sensor-data"></a>Fråga med sensordata

När du har skapat ankare med tillhörande sensordata kan du börja hämta dem med hjälp av sensoravläsningarna som rapporteras av enheten. Du behöver inte längre förse tjänsten med en lista över kända ankare som du förväntar dig att hitta - istället meddelar du bara tjänsten var enheten finns som rapporterats av dess inbyggda sensorer. Azure Spatial Anchors kommer sedan att räkna ut uppsättningen ankare nära din enhet och försöka visuellt matcha dem.

Om du vill att frågor ska använda sensordata börjar du med att skapa kriterier för "nära enhet":

# <a name="c"></a>[C#](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objc"></a>[ObjC (olika)](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="java"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

Parametern `DistanceInMeters` styr hur långt vi ska utforska ankardiagrammet för att hämta innehåll. Antag till exempel att du har befolkat lite utrymme med ankare vid en konstant densitet på 2 varje meter. Dessutom observerar kameran på enheten ett enda ankare och tjänsten har lokaliserat den. Du är troligen intresserad av att hämta alla ankare du har placerat i närheten i stället för det enda ankare som du för närvarande observerar. Om du antar att de fästpunkter du har placerat är anslutna i ett diagram kan tjänsten hämta alla fästpunkter i närheten genom att följa kanterna i diagrammet. Mängden graf traversal gjort styrs av `DistanceInMeters`; du kommer att få alla ankare som är anslutna till den `DistanceInMeters`du har hittat, som är närmare än .

Tänk på att stora `MaxResultCount` värden för kan påverka prestanda negativt. Ställ in det på ett vettigt värde för din ansökan.

Slutligen måste du be sessionen att använda sensorbaserad look up:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[ObjC (olika)](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="java"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>Förväntat resultat

Konsument-grade GPS-enheter är vanligtvis ganska oprecisa. En studie av [Zandenbergen och Barbeau (2011)][6] rapporterar mediannoggrannhet mobiltelefoner med assisterad GPS (A-GPS) att vara runt 7 meter - ganska stort värde att ignoreras! För att ta hänsyn till dessa mätfel behandlar tjänsten ankare som sannolikhetsfördelningar i GPS-rymden. Som sådan är ett ankare nu den region i rymden som mest sannolikt (det vill ha mer än 95% förtroende) innehåller sin sanna, okända GPS-position.

Samma resonemang tillämpas vid frågor med GPS. Enheten representeras som en annan rumslig förtroende region runt sin sanna, okända GPS-position. Upptäcka närliggande ankare översätter till att helt enkelt hitta ankare med förtroende regioner *tillräckligt nära* enhetens förtroende region, vilket illustreras i bilden nedan:

![Urval av ankare kandidater med GPS](media/coarse-reloc-gps-separation-distance.png)

Gps-signalens noggrannhet, både när det gäller att skapa ankare och vid frågor, har ett stort inflytande över uppsättningen returnerade ankare. Däremot frågor baserade på WiFi / beacons kommer att överväga alla ankare som har minst en åtkomstpunkt / beacon gemensamt med frågan. I den meningen bestäms resultatet av en fråga baserad på WiFi / beacons mestadels av det fysiska intervallet av åtkomstpunkter / fyrar och miljöhinder.

Tabellen nedan uppskattar det förväntade sökutrymmet för varje sensortyp:

| Sensor      | Sökutrymmesradie (ca)Search space radius (ca.) | Information |
|-------------|:-------:|---------|
| Gps         | 20 m - 30 m | Bestäms av GPS-osäkerhet bland andra faktorer. De rapporterade siffrorna uppskattas för medianen GPS noggrannhet mobiltelefoner med A-GPS, det vill vara 7 meter. |
| WiFi        | 50 m - 100 m | Bestäms av räckvidden för de trådlösa åtkomstpunkterna. Beror på frekvens, sändare styrka, fysiska hinder, störningar, och så vidare. |
| BLE-fyrar |  70 m | Bestäms av fyrens räckvidd. Beror på frekvens, transmissionsstyrka, fysiska hinder, störningar och så vidare. |

## <a name="per-platform-support"></a>Support per plattform

I följande tabell sammanfattas sensordata som samlas in på var och en av de plattformar som stöds, tillsammans med eventuella plattformsspecifika varningar:


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| Gps         | Ej tillämpligt | Stöds via [LocationManager][3] API:er (både GPS och NETWORK) | Stöds via [CLLocationManager][4] API:er |
| WiFi        | Stöds med en hastighet av ungefär en genomsökning var tredje sekund | Stöds. Från och med API nivå 28, WiFi-skanningar begränsas till 4 samtal var 2 minuter. Från Android 10 kan begränsningen inaktiveras från menyn Utvecklarinställningar. Mer information finns i [Android-dokumentationen][5]. | Ej offentligt API - inget offentligt API |
| BLE-fyrar | Begränsad till [Eddystone][1] och [iBeacon][2] | Begränsad till [Eddystone][1] och [iBeacon][2] | Begränsad till [Eddystone][1] och [iBeacon][2] |

## <a name="next-steps"></a>Nästa steg

Använd grov omlokalisering i en app.

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Objective-C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
