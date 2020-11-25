---
title: Grov omlokalisering i Swift
description: Djupgående förklaring av hur du skapar och hittar fäst punkter med grov omlokalisering i Swift.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7cc964ca8339e82aa7388aeab8718a6102a23d1b
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95999775"
---
# <a name="how-to-create-and-locate-anchors-using-coarse-relocalization-in-swift"></a>Skapa och hitta ankare med hjälp av grov omlokalisering i Swift

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android Java](set-up-coarse-reloc-java.md)
> * [C++-/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Molnbaserade ankare kan associeras på enheten, placera sensor data med de ankare som du skapar. Den här informationen kan också användas för att snabbt avgöra om det finns några fäst punkter i närheten av enheten. Mer information finns i [grov omlokalisering](../concepts/coarse-reloc.md).

## <a name="prerequisites"></a>Förutsättningar

Se till att du har följande för att slutföra den här guiden:

- Grundläggande kunskaper om Swift.
- Läs igenom [översikten över Azures spatiala ankare](../overview.md).
- Slutfört ett av [snabb starterna på fem minuter](../index.yml).
- Läs igenom [instruktionen skapa och hitta ankare](../create-locate-anchors-overview.md).

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Allow GPS
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true

// Allow WiFi scanning
sensors.wifiEnabled = true

// Populate the set of known BLE beacons' UUIDs
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

// Allow the set of known BLE beacons
sensors.bluetoothEnabled = true
sensors.knownBeaconProximityUuids = uuids
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```swift
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider

// Configure the near-device criteria
let nearDeviceCriteria = ASANearDeviceCriteria()!
nearDeviceCriteria.distanceInMeters = 5.0
nearDeviceCriteria.maxResultCount = 25

// Set the session's locate criteria
let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]