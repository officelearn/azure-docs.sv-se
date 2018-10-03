---
title: Azure IoT-enhetshantering med IoT-tillägget för Azure CLI | Microsoft Docs
description: Använda IoT-tillägget för Azure CLI-verktyg för Azure IoT Hub-enhetshantering med direkta metoder och hanteringsalternativ för den läsningen önskade egenskaper.
author: chrissie926
manager: ''
keywords: Azure iot-enhetshantering, azure iot hub-enhetshantering, device management iot, iot hub-enhetshantering
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: c5d4299d2d391a25bfab579f5f78da3718afc24c
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043015"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Använd IoT-tillägget för Azure CLI för Azure IoT Hub-enhetshantering

![Slutpunkt till slutpunkt-diagram](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) är en ny öppen källkod IoT-tillägget som lägger till funktionerna i den [Azure CLI](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest). Azure CLI innehåller kommandon för att interagera med Azure resource manager och av hanteringsslutpunkter. Du kan till exempel använda Azure CLI för att skapa en Azure virtuell dator eller en IoT-hubb. CLI-tillägg kan en Azure-tjänst att utöka Azure CLI som ger dig åtkomst till ytterligare tjänstspecifika funktioner. IoT-tillägget ger IoT utvecklare kommandoraden åtkomst till alla IoT Hub, IoT Edge och IoT Hub Device Provisioning Service-funktioner.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Management-alternativ          | Aktivitet  |
|----------------------------|-----------|
| Direkta metoder             | Se en enhet som fungerar, till exempel starta eller stoppa skicka meddelanden eller att enheten startas om.                                        |
| Enhetstvillingens egenskaper    | Placera en enhet i vissa lägen, till exempel ställa in en LED grönt eller 30 minuter att ställa in skicka telemetriintervall.         |
| Enhetstvillingens egenskaper   | Hämta det rapporterade tillståndet för en enhet. Enheten rapporterar till exempel LAMPAN blinkar nu.                                    |
| Enhetstvilling-taggar                  | Store enhetsspecifika metadata i molnet. Till exempel distributionsplatsen för en Varuautomat.                         |
| Enhetstvillingfrågor        | Fråga alla enhetstvillingar för att hämta dem med valfri villkor, till exempel identifiera de enheter som är tillgängliga för användning. |

Mer detaljerad förklaring om skillnaderna och hjälp med att använda de här alternativen finns i [enhet till molnet kommunikation vägledning](iot-hub-devguide-d2c-guidance.md) och [moln till enhet kommunikation vägledning](iot-hub-devguide-c2d-guidance.md).

Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd (metadata, konfigurationer och villkor). IoT Hub lagrar en enhetstvilling för varje enhet som ansluter till den. Läs mer om enhetstvillingar [Kom igång med enhetstvillingar](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig använda IoT-tillägget för Azure CLI med olika hanteringsalternativ på utvecklingsdatorn.

## <a name="what-you-do"></a>Vad du gör

Kör Azure CLI och IoT-tillägget för Azure CLI med olika alternativ.

## <a name="what-you-need"></a>Vad du behöver

* I självstudiekursen [konfigurera enheten](iot-hub-raspberry-pi-kit-node-get-started.md) som omfattar följande krav:

  - En aktiv Azure-prenumeration.
  - Azure IoT hub i din prenumeration.
  - Ett klientprogram som skickar meddelanden till din Azure IoT hub.

* Kontrollera att enheten kör med klientprogrammet under den här självstudien.

* [Python 2.7x eller Python 3.x](https://www.python.org/downloads/)

* Azure CLI. Om du vill installera det kan se [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Azure CLI-version måste minst vara 2.0.24 eller senare. Validera med `az –version`. 

* Installera IoT-tillägget. Det enklaste sättet är att köra `az extension add --name azure-cli-iot-ext`. I [IoT-tilläggets Viktigt-fil](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) beskrivs olika sätt att installera tillägget.

## <a name="log-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

Logga in på ditt Azure-konto genom att köra följande kommando:

```bash
az login
```

## <a name="direct-methods"></a>Direkta metoder

```bash
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Enhetstvillingens egenskaper

Ange ett intervall för önskad egenskap = 3000 genom att köra följande kommando:

```bash
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Den här egenskapen kan läsas från din enhet.

## <a name="device-twin-reported-properties"></a>Enhetstvillingen rapporterade egenskaper

Hämta de rapporterade egenskaperna för enheten genom att köra följande kommando:

```bash
az iot hub device-twin show -n <your hub name> -d <your device id>
```

En av läsningen rapporterade egenskaper är $metadata. $lastUpdated som visar den senaste gången enhetsappen uppdateras dess rapporterade egenskapsuppsättning.

## <a name="device-twin-tags"></a>Enheten twin taggar

Visa taggar och egenskaper för enheten genom att köra följande kommando:

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Lägg till en roll för fältet = temperatur och fuktighet till enheten genom att köra följande kommando:

```bash
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Enhetstvillingfrågor

Fråga enheter med en tagg i rollen = 'temperatur och fuktighet' genom att köra följande kommando:

```bash
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Fråga efter alla enheter utom de som har en tagg i rollen = 'temperatur och fuktighet' genom att köra följande kommando:

```bash
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Nästa steg

Du har lärt dig att övervaka meddelanden från enheten till molnet och skicka meddelanden från molnet till enheten mellan dina IoT-enhet och Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
