---
title: Hantering av Azure IoT-enheter med IoT-tillägg för Azure CLI | Microsoft-dokument
description: Använd IoT-tillägget för Azure CLI-verktyget för Azure IoT Hub-enhetshantering, med direct-metoderna och Twins önskade egenskaperhanteringsalternativ.
author: chrissie926
manager: ''
keywords: azure iot enhetshantering, azure iot hub device management, enhetshantering iot, iot hub device management
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 88c3d1f4213b161d5e322349a7f0e1bc1dd952e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239649"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Använda IoT-tillägget för Azure CLI för Azure IoT Hub-enhetshantering

![Diagram från på sluten tid](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) är ett IoT-tillägg med öppen källkod som lägger till funktionerna i [Azure CLI](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest). Azure CLI innehåller kommandon för att interagera med Azure Resource Manager och hanteringslutpunkter. Du kan till exempel använda Azure CLI för att skapa en Virtuell Azure-dator eller en IoT-hubb. Ett CLI-tillägg gör det möjligt för en Azure-tjänst att utöka Azure CLI som ger dig tillgång till ytterligare tjänstspecifika funktioner. IoT-tillägget ger IoT-utvecklare kommandoradsåtkomst till alla funktioner för IoT Hub, IoT Edge och IoT Hub Device Provisioning Service.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Förvaltningsalternativ          | Aktivitet  |
|----------------------------|-----------|
| Direkta metoder             | Få en enhet att fungera som att starta eller sluta skicka meddelanden eller starta om enheten.                                        |
| Dubbla önskade egenskaper    | Sätt en enhet i vissa lägen, till exempel ställa in en lysdiod till grönt eller ställa in telemetrissändintervallet till 30 minuter.         |
| Dubbla rapporterade egenskaper   | Hämta det rapporterade tillståndet för en enhet. Enheten rapporterar till exempel att lysdioden blinkar nu.                                    |
| Dubbla taggar                  | Lagra enhetsspecifika metadata i molnet. Distributionsplatsen för en varuautomat.                         |
| Dubbla frågor om enhet        | Fråga alla enhetstvillingar för att hämta dessa tvillingar med godtyckliga villkor, till exempel identifiera de enheter som är tillgängliga för användning. |

Mer detaljerad förklaring om skillnaderna och vägledningen om hur du använder dessa alternativ finns i [kommunikationsvägledning för enhet till moln](iot-hub-devguide-d2c-guidance.md) och [kommunikationvägledning från molnet till enhet](iot-hub-devguide-c2d-guidance.md).

Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd (metadata, konfigurationer och villkor). IoT Hub beständiga en enhetstvilling för varje enhet som ansluter till den. Mer information om enhetstvillingar finns i [Komma igång med enhetstvillingar](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig att använda IoT-tillägget för Azure CLI med olika hanteringsalternativ på din utvecklingsdator.

## <a name="what-you-do"></a>Vad du gör

Kör Azure CLI och IoT-tillägget för Azure CLI med olika hanteringsalternativ.

## <a name="what-you-need"></a>Vad du behöver

* Slutför [Raspberry Pi online simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) handledning eller en av enheten tutorials; till exempel [Raspberry Pi med node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Dessa poster omfattar följande krav:

  - En aktiv Azure-prenumeration.
  - En Azure IoT-hubb under din prenumeration.
  - Ett klientprogram som skickar meddelanden till din Azure IoT-hubb.

* Kontrollera att enheten körs med klientprogrammet under den här självstudien.

* [Python 2.7x eller Python 3.x](https://www.python.org/downloads/)

* The Azure CLI. Om du behöver installera det läser du [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Azure CLI-versionen måste vara minst 2.0.70 eller högre. Validera med `az –version`.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* Installera IoT-tillägget. Det enklaste sättet är att köra `az extension add --name azure-iot`. I [IoT-tilläggets Viktigt-fil](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) beskrivs olika sätt att installera tillägget.

## <a name="sign-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

Logga in på ditt Azure-konto genom att köra följande kommando:

```azurecli
az login
```

## <a name="direct-methods"></a>Direkta metoder

```azurecli
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Egenskaper för enhetstvilling

Ange ett önskat egenskapsintervall = 3000 genom att köra följande kommando:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Den här egenskapen kan läsas från enheten.

## <a name="device-twin-reported-properties"></a>Enhetstvilling rapporterade egenskaper

Hämta de rapporterade egenskaperna för enheten genom att köra följande kommando:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

En av de dubbla rapporterade egenskaperna är $metadata.$lastUpdated, som visar den senaste gången enhetsappen uppdaterade sin rapporterade egenskapsuppsättning.

## <a name="device-twin-tags"></a>Dubbla enhetstaggar

Visa enhetens taggar och egenskaper genom att köra följande kommando:

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Lägg till en fältroll = temperatur&fuktighet till enheten genom att köra följande kommando:

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Dubbla frågor om enhet

Frågeenheter med en rolltagg = "temperatur&luftfuktighet" genom att köra följande kommando:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Fråga alla enheter utom de med en rolltagg = "temperatur&luftfuktighet" genom att köra följande kommando:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du övervakar meddelanden från enhet till moln och skickar meddelanden från molnet till enheten mellan din IoT-enhet och Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
