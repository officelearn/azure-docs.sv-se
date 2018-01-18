---
title: Azure IoT-enhetshantering med iothub explorer | Microsoft Docs
description: "Verktyget iothub explorer CLI för Azure IoT Hub-enhetshantering med direkta metoder och de två alternativ för egenskaper."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "Azure iot enhetshantering, azure iot-hubb enhetshantering, device management iot, enhetshantering för iot-hubb"
ms.assetid: b34f799a-fc14-41b9-bf45-54751163fffe
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: xshi
ms.openlocfilehash: 02d3241ae4ae515a8c2deaa14f50b5b7dbd4766f
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="use-iothub-explorer-for-azure-iot-hub-device-management"></a>Använda iothub explorer för hantering av Azure IoT Hub-enheter

![Diagram för slutpunkt till slutpunkt](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub explorer](https://github.com/azure/iothub-explorer) är ett CLI-verktyg som du kör på en värd datorn för att hantera enheten identiteter i din IoT-hubb registret. Den innehåller alternativ som du kan använda för att utföra olika uppgifter.

| Hanteringsalternativ          | Aktivitet                                                                                                                            |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------|
| Direkta metoder             | Gör en enhet som agerar som startar eller stoppar skickar meddelanden eller starta om enheten.                                        |
| Dubbla önskade egenskaper    | Lägga till en enhet i vissa tillstånd, till exempel ange en Indikator till grönt eller inställningen telemetri skicka intervall till 30 minuter.         |
| Dubbla rapporterade egenskaper   | Hämta rapporterat status för en enhet. Till exempel rapporterar enheten Indikatorn blinkar nu.                                    |
| Dubbla taggar                  | Lagra enhetsspecifika metadata i molnet. Till exempel distributionsplatsen för en Varuautomat.                         |
| Meddelanden moln till enhet   | Skicka meddelanden till en enhet. Till exempel ”är det mycket sannolikt att regn idag. Glöm inte att få en övergripande ”.              |
| Enheten dubbla frågor        | Fråga alla enheten twins för att hämta dem med valfri villkor, till exempel identifiera de enheter som är tillgängliga för användning. |

Mer detaljerad förklaring om skillnaderna och vägledning om hur du använder dessa alternativ finns [enhet till moln kommunikation vägledning](iot-hub-devguide-d2c-guidance.md) och [moln till enhet kommunikation vägledning](iot-hub-devguide-c2d-guidance.md).

> [!NOTE]
> Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd (metadata, konfigurationer och villkor). IoT-hubb kvarstår en enhet dubbla för varje enhet som ansluter till den. Läs mer om enheten twins [Kom igång med enheten twins](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig med olika alternativ på utvecklingsdatorn iothub explorer.

## <a name="what-you-do"></a>Vad du gör

Kör iothub explorer med olika hanteringsalternativ för.

## <a name="what-you-need"></a>Vad du behöver

- Kursen [konfigurera enheten](iot-hub-raspberry-pi-kit-node-get-started.md) slutförts som omfattar följande krav:
- En aktiv Azure-prenumeration.
- En Azure IoT-hubb i din prenumeration.
- Ett klientprogram som skickar meddelanden till din Azure IoT-hubb.
- Kontrollera att enheten kör med klientprogrammet under den här kursen.
- iothub-explorer [installera iothub explorer](https://github.com/azure/iothub-explorer) på utvecklingsdatorn.

## <a name="connect-to-your-iot-hub"></a>Ansluta till din IoT-hubb

Anslut till din IoT-hubb genom att köra följande kommando:

```bash
iothub-explorer login <your IoT hub connection string>
```

## <a name="use-iothub-explorer-with-direct-methods"></a>Använd iothub explorer med direkta metoder

Anropa den `start` metod i appen enhet för att skicka meddelanden till din IoT-hubb genom att köra följande kommando:

```bash
iothub-explorer device-method <your device Id> start
```

Anropa den `stop` metod i appen enheten slutar skicka meddelanden till din IoT-hubb genom att köra följande kommando:

```bash
iothub-explorer device-method <your device Id> stop
```

## <a name="use-iothub-explorer-with-twins-desired-properties"></a>Använd iothub explorer med dubblas egenskaper

Ange ett intervall önskade egenskapen = 3000 genom att köra följande kommando:

```bash
iothub-explorer update-twin <your device id> {\"properties\":{\"desired\":{\"interval\":3000}}}
```

Den här egenskapen kan läsas av enheten.

## <a name="use-iothub-explorer-with-twins-reported-properties"></a>Använd iothub explorer med dubblas rapporterade egenskaper

Hämta rapporterade egenskaperna för enheten genom att köra följande kommando:

```bash
iothub-explorer get-twin <your device id>
```

En av egenskaperna är $metadata. $lastUpdated som visar senast enheten skickar och tar emot ett meddelande.

## <a name="use-iothub-explorer-with-twins-tags"></a>Använd iothub explorer med dubblas taggar

Visa taggar och egenskaper för enheten genom att köra följande kommando:

```bash
iothub-explorer get-twin <your device id>
```

Lägga till en roll för fältet = temperatur & fuktighet till enheten genom att köra följande kommando:

```bash
iothub-explorer update-twin <your device id> "{\"tags\":{\"role\":\"temperature&humidity\"}}"
```

## <a name="use-iothub-explorer-with-cloud-to-device-messages"></a>Använda iothub explorer med meddelanden moln till enhet

Skicka ett meddelande om ”Hello World” till enheten genom att köra följande kommando:

```bash
iothub-explorer send <device-id> "Hello World"
```

Se [använda iothub explorer för att skicka och ta emot meddelanden mellan enheten och IoT-hubb](iot-hub-explorer-cloud-device-messaging.md) verkliga scenarier för att använda det här kommandot.

## <a name="use-iothub-explorer-with-device-twins-queries"></a>Använda iothub explorer med enheten twins frågor

Fråga enheter med en tagg av rollen = 'temperatur- och fuktighetskonsekvens' genom att köra följande kommando:

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Fråga alla enheter utom de som har en tagg av rollen = 'temperatur- och fuktighetskonsekvens' genom att köra följande kommando:

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du använder iothub explorer med olika hanteringsalternativ för.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
