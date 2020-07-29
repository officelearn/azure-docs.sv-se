---
title: Implementera identifiering av IoT Plug and Play Preview-modell | Microsoft Docs
description: Som Solution Builder kan du läsa om hur du kan implementera IoT Plug and Play modell identifiering i din lösning.
author: prashmo
ms.author: prashmo
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 364b85a8ead09858b97d5d7e6ca8c130b9960b2c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337389"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Implementera IoT Plug and Play för hands versions modell identifiering i en IoT-lösning

I den här artikeln beskrivs hur du kan implementera IoT Plug and Play Preview modell identifiering i en IoT-lösning, som Solution Builder. Modell identifieringen beskriver hur:

- IoT Plug and Play-enheter registrerar sitt modell-ID.
- En IoT-lösning hämtar de gränssnitt som implementeras av enheten.

Det finns två breda kategorier av IoT-lösningar:

- En *syfte – konstruerad IoT-lösning* fungerar med en känd uppsättning IoT plug and Play enhets modeller.

- En *modell driven IoT-lösning* kan fungera med alla IoT plug and Play-enheter. Att skapa en modell driven lösning är mer komplex, men fördelen är att din lösning fungerar med alla enheter som lagts till i framtiden.

    Om du vill bygga en modell driven IoT-lösning måste du bygga logik mot IoT Plug and Play-gränssnittets primitiver: telemetri, egenskaper och kommandon. Din lösnings logik representerar en enhet genom att kombinera flera telemetri-, egenskaps-och kommando funktioner.

Den här artikeln beskriver hur du implementerar modell identifiering i båda typerna av lösning.

## <a name="model-discovery"></a>Modellidentifiering

För att identifiera vilken modell en enhet implementerar, kan en lösning Hämta modell-ID med hjälp av händelse-eller nätverksbaserad identifiering:

### <a name="event-based-discovery"></a>Händelse-baserad identifiering

När en IoT Plug and Play-enhet ansluter till IoT Hub registreras den modell som implementeras. Den här registreringen resulterar i ett digitalt meddelande om [ändrings händelser](concepts-digital-twin.md#digital-twin-change-events) . Information om hur du aktiverar routning för digitala dubbla händelser finns i [använda IoT Hub meddelanderoutning för att skicka meddelanden från enhet till moln till olika slut punkter](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Lösningen kan använda den händelse som visas i följande kodfragment för att lära dig mer om IoT Plug and Play enhet som ansluter och får sitt modell-ID:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

Den här händelsen utlöses när enhets modellens ID läggs till eller uppdateras.

### <a name="twin-based-discovery"></a>Dubbel-baserad identifiering

Om lösningen vill veta om funktionerna för en specifik enhet kan du använda appen [Hämta digitala dubbla](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin) API för att hämta informationen.

I följande digitala dubbla kodfragment `$metadata.$model` innehåller modell-ID: t för en IoT plug and Play-enhet:

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

Lösningen kan också använda **Get fläta** för att hämta modell-ID från enheten på samma sätt som visas i följande kodfragment:

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

## <a name="model-resolution"></a>Modell upplösning

En lösning använder modell upplösning för att få åtkomst till gränssnitten som skapar en modell från modell-ID: t. 

- Lösningar kan välja att lagra dessa gränssnitt som filer i en lokal mapp. 
- Lösningar kan använda [modell databasen](concepts-model-repository.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om modell identifiering av en IoT-lösning kan du läsa mer om [Azure IoT-plattformen](overview-iot-plug-and-play.md) för att använda andra funktioner för din lösning.

- [Interagera med en enhet från din lösning](quickstart-service-node.md)
- [IoT Digital, dubbla REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)
