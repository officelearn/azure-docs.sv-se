---
title: Grov omlokalisering
description: Lär dig mer om att använda grov omlokalisering för att hitta ankare nära dig.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a65b2ca4ba9f1912adeaf60df123bcd3c8833bd
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95496910"
---
# <a name="coarse-relocalization"></a>Grov omlokalisering

Grov omlokalisering är en funktion som ger ett första svar på frågan: *där är min enhet nu/vad innehållet ska jag observera?* Svaret är inte exakt, utan är i formatet: du är *nära dessa ankare. försök att hitta någon av dem*.

Grov omlokalisering fungerar genom att associera olika avläsningar av enhets sensorer med både skapande och frågor om ankare. Sensor data är vanligt vis enhetens GPS-position (Global Positioning System). Om GPS inte är tillgängligt eller otillförlitligt (t. ex. inomhus) består sensor data av WiFi-åtkomst punkter och Bluetooth-beacons inom räckhåll. Alla insamlade sensor data bidrar till att underhålla ett rums index som används av de spatiala fäst punkterna i Azure för att snabbt fastställa de ankare som ligger inom cirka 100 meter på enheten.

Snabb sökning av ankare som aktive ras vid grov omlokalisering fören klar utvecklingen av program som backas upp av världs omfattande samlingar av (t. ex. miljon tals geo-distribuerade) ankare. Komplexiteten när det gäller Ankares hantering är allt dolt, så att du kan fokusera mer på din häftiga program logik. All fäst punkts-och lyfter görs åt dig bakom kulisserna av molnets avstånds ankare.

## <a name="collected-sensor-data"></a>Insamlade sensor data

Sensor data som du kan skicka till docknings tjänsten är något av följande:

* GPS-position: latitud, longitud, höjd.
* Signal styrka för WiFi-åtkomst punkter i intervallet.
* Signal styrkan för Bluetooth-beacons i intervallet.

I allmänhet måste ditt program Hämta enhetsspecifika behörigheter för att få åtkomst till GPS-, WiFi-eller Bell-data. Dessutom är vissa av sensor data ovan inte tillgängliga i design på vissa plattformar. För att kunna beakta dessa situationer är insamlingen av sensor data valfri och är inaktive rad som standard.

## <a name="set-up-the-sensor-data-collection"></a>Konfigurera sensor data insamling

Vi börjar med att skapa en sensor för finger avtrycks kontroll och göra sessionen medveten om den:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

Därefter måste du bestämma vilka sensorer du vill använda för grov omlokalisering. Detta beslut är särskilt för det program som du utvecklar, men rekommendationerna i följande tabell bör ge dig en lämplig start punkt:

|                 | Inkorgar | Utomhus |
|-----------------|---------|----------|
| **GPS**         | Av | På |
| **WiFi**        | På | På (valfritt) |
| **Bell-beacons** | På (valfritt med varningar, se nedan) | Av |

### <a name="enabling-gps"></a>Aktivera GPS

Förutsatt att ditt program redan har behörighet att komma åt enhetens GPS-position kan du konfigurera Azure spatiala ankare för att använda den:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

När du använder GPS i ditt program bör du tänka på att de läsningar som tillhandahålls av maskin varan är vanligt vis:

* asynkron och låg frekvens (mindre än 1 Hz).
* otillförlitligt/brus (i genomsnittlig standard avvikelse på 7 m).

I allmänhet kommer både enhetens OS-och Azure-spatialdata att göra vissa filtreringar och extrapoleringar på den obehandlade GPS-signalen i ett försök att undvika problemen. Den här extra bearbetningen kräver ytterligare tid för konvergens, så för bästa resultat bör du försöka:

* skapa en sensor finger avtrycks leverantör så tidigt som möjligt i ditt program
* Behåll sensorn för finger avtryck mellan flera sessioner
* Dela sensorn finger avtrycks leverantör mellan flera sessioner

Om du planerar att använda sensorns finger avtrycks leverantör utanför en Anchor-session måste du starta den innan du begär sensor uppskattningar. Följande kod tar till exempel hand om uppdatering av enhetens position på kartan i real tid:

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

# <a name="objc"></a>[ObjC](#tab/objc)

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

Förutsatt att ditt program redan har behörighet att komma åt enhetens WiFi-tillstånd kan du konfigurera Azure spatiala ankare för att använda det:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

När du använder WiFi i ditt program bör du tänka på att de läsningar som tillhandahålls av maskin varan är vanligt vis:

* asynkron och låg frekvens (mindre än 0,1 Hz).
* eventuellt begränsad till operativ system nivå.
* otillförlitligt/brus (i genomsnitt 3 – dBm standard avvikelse).

Med hjälp av Azures spatialdata görs ett försök att bygga en filtrerad WiFi signal styrke karta under en session i ett försök att minimera problemen. För bästa resultat bör du försöka:

* skapa en bra session innan du placerar den första fäst punkten.
* Se till att sessionen är aktiv så länge som möjligt (det vill säga skapa alla ankare och fråga i en session).

### <a name="enabling-bluetooth-beacons"></a>Aktivera Bluetooth-beacons

Förutsatt att ditt program redan har behörighet att komma åt enhetens Bluetooth-tillstånd kan du konfigurera Azure spatiala ankare för att använda den:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

Beacons är vanligt vis mångsidiga enheter, där allt, inklusive UUID-och MAC-adresser, kan konfigureras. Den här flexibiliteten kan vara problematisk för Azure spatiala ankare eftersom det anses att signaler identifieras unikt av sina UUID: er. Det går inte att se till att den här unikheten förmodligen orsakar spatiala maskrum. För bästa resultat bör du:

* tilldela unika UUID: er till dina beacons.
* distribuera dem – vanligt vis i ett vanligt mönster, till exempel ett rutnät.
* skicka listan med unika Beacon-UUID: er till sensor finger avtrycks leverantören:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

Azures avstånds ankare spårar bara Bluetooth-beacons som finns i listan med kända Beacon-UUID. Skadliga beacons som är programmerade med tillåtna UUID: er i listan kan fortfarande påverka kvaliteten på tjänsten. Av den anledningen bör du bara använda signaler i hanterade utrymmen där du kan styra deras distribution.

## <a name="querying-with-sensor-data"></a>Fråga med sensor data

När du har skapat ankare med tillhör ande sensor data kan du börja hämta dem med hjälp av sensor avläsningar som rapporteras av enheten. Du behöver inte längre tillhandahålla tjänsten med en lista med kända ankare som du förväntar dig att hitta – i stället kan du bara låta tjänsten känna till platsen för enheten som rapporteras av dess inbyggda sensorer. De spatiala Ankarena i Azure visar sedan en uppsättning ankare nära enheten och försöker visuellt matcha dem.

Om du vill att frågor ska använda sensor data börjar du med att skapa "nära enhet"-kriterier:

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

# <a name="objc"></a>[ObjC](#tab/objc)

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

`DistanceInMeters`Parametern styr hur långt vi ska utforska fäst punkt diagrammet för att hämta innehåll. Anta till exempel att du har fyllt i utrymme med ankare med en konstant densitet på 2 varje meter. Dessutom är kameran på enheten en enda fäst punkt och tjänsten har hittat den. Du är mest intresse rad av att hämta alla ankare som du har placerat i närheten i stället för den enda fäst punkt som du för närvarande har iakttagit. Förutsatt att de ankare du har placerat är anslutna i ett diagram kan tjänsten hämta alla närliggande ankare åt dig genom att följa kanterna i diagrammet. Mängden diagram genom gång styrs av `DistanceInMeters` . du får alla fäst punkter som är anslutna till den som du har hittat, som är närmare än `DistanceInMeters` .

Tänk på att stora värden för `MaxResultCount` kan påverka prestanda negativt. Ange det som ett lämpliga-värde för ditt program.

Slutligen måste du berätta för sessionen att använda den sensorbaserade sökningen:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

GPS-enheter i konsument klass är vanligt vis ganska EXAKTA. En studie av [Zandenbergen och Barbeau (2011)][6] rapporterar median precisionen för mobil telefoner med stöd för GPS (A-GPS) till omkring 7 meter – ett stort värde ignoreras! För att kontona ska kunna beaktas, behandlar tjänsten ankare som sannolikhets distributioner i GPS-utrymme. En fäst punkt är nu den region där det är troligt (det vill säga med mer än 95% konfidensgrad) som innehåller den faktiska, okända GPS-positionen.

Samma orsak tillämpas när du frågar med GPS. Enheten representeras som en annan region för rums exakthet runt den logiska, okända GPS-positionen. Att identifiera närliggande ankare översätts till att helt enkelt hitta ankare med konfidens områden som är *tillräckligt nära* för enhetens konfidens område, enligt bilden nedan:

![Val av fäst kandidater med GPS](media/coarse-reloc-gps-separation-distance.png)

Noggrannheten i GPS-signalen, både vid skapande av ankare och under frågor, har ett stort inflytande över uppsättningen returnerade ankare. Frågor som baseras på WiFi/beacons ser däremot till att alla ankare som har minst en åtkomst punkt/beacon är gemensamma med frågan. I det här fallet bestäms resultatet av en fråga som baseras på WiFi/beacons huvudsakligen av den fysiska räckvidden för åtkomst punkter/signaler och miljö hinder.

Tabellen nedan uppskattar det förväntade Sök utrymmet för varje sensor typ:

| Mäta      | Sök efter utrymmes radie (ca.) | Information |
|-------------|:-------:|---------|
| GPS         | 20 m – 30 m | Bestäms av en GPS-osäkerhet bland andra faktorer. De rapporterade siffrorna beräknas för median precisionen för median för mobil telefoner med en-GPS, som är 7 meter. |
| WiFi        | 50 m-100 m | Bestäms av intervallet för de trådlösa åtkomst punkterna. Beror på frekvensen, sändnings styrkan, fysiska hinder, störning och så vidare. |
| Bell-beacons |  70 m | Bestäms av Beacon-intervallet. Beror på frekvensen, överförings styrkan, fysiska hinder, störning och så vidare. |

## <a name="per-platform-support"></a>Support per plattform

I följande tabell sammanfattas de sensor data som samlas in på var och en av de plattformar som stöds, tillsammans med alla plattformsspecifika varningar:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | Ej tillämpligt | Stöds via [LocationManager][3] -API: er (både GPS och nätverk) | Stöds via [CLLocationManager][4] -API: er |
| **WiFi**        | Stöds till ungefär en genomsökning var tredje sekund | Stöds. Från och med API-nivå 28 begränsas WiFi-genomsökningar till 4 anrop var 2: e minut. Från Android 10 kan begränsningen inaktive ras från menyn för inställningar för utvecklare. Mer information finns i Android- [dokumentationen][5]. | Ej tillämpligt – inget offentligt API |
| **Bell-beacons** | Begränsat till [Eddystone][1] och [iBeacon][2] | Begränsat till [Eddystone][1] och [iBeacon][2] | Begränsat till [Eddystone][1] och [iBeacon][2] |

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
> [C++-/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
