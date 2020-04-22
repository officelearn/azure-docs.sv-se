---
title: Gemensamma gränssnitt - IoT Plug and Play Preview | Microsoft-dokument
description: Beskrivning av vanliga gränssnitt för IoT Plug and Play-utvecklare
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5773ec2b3ea88fa9a507b7c1b0b84bb7ea305a94
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770489"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>Vanliga gränssnitt för IoT Plug and Play Preview

Alla IoT Plug and Play-enheter förväntas implementera några vanliga gränssnitt. Vanliga gränssnitt gynnar IoT-lösningar eftersom de ger konsekvent funktionalitet. [Certifiering](tutorial-build-device-certification.md) kräver att enheten implementerar flera vanliga gränssnitt. Du kan hämta vanliga gränssnittsdefinitioner från den offentliga modelldatabasen.

## <a name="summary-of-common-interfaces"></a>Sammanfattning av gemensamma gränssnitt

| Namn | ID | Beskrivning | Implementerad av Azure IoT SDK | Måste deklareras i kapacitetsmodell |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Modellinformation | urn:azureiot:ModelDiscovery:ModelInformation:1 | För enheter att deklarera kapacitetsmodell-ID och gränssnitt. Krävs för alla IoT Plug and Play-enheter. | Ja | Inga |
| Digital Twin Client SDK-information | urn:azureiot:Client:SDKInformation:1 | Klient SDK för anslutning av enheten med Azure. Krävs för [certifiering](tutorial-build-device-certification.md) | Ja | Inga |
| Enhetsinformation | urn:azureiot:DeviceManagement:DeviceInformation:1 | Information om maskinvara och operativsystem om enheten. Krävs för [certifiering](tutorial-build-device-certification.md) | Inga | Ja |
| Modelldefinition | urn:azureiot:ModelDiscovery:ModelDefinition:1 | För enheter att deklarera den fullständiga definitionen för sin kapacitet modell och gränssnitt. Måste implementeras när modelldefinitioner inte finns i en modelldatabas. | Inga | Ja |
| Digital tvilling | urn:azureiot:ModelDiscovery:DigitalTwin:1 | För lösningsutvecklare att hämta kapacitetsmodell-ID och gränssnitts-ID för en digital tvilling. Det här gränssnittet deklareras eller implementeras inte av en IoT Plug and Play-enhet. | Inga | Inga |

- Implementerad av Azure IoT SDK - Om Azure IoT SDK implementerar de funktioner som deklarerats i gränssnitten. IoT Plug and Play-enheter som använder Azure IoT SDK behöver inte implementera det här gränssnittet.
- Måste deklareras i kapacitetsmodell - Om "ja" `"implements":` måste det här gränssnittet deklareras inom den del av enhetskapacitetsmodellen för den här IoT Plug and Play-enheten.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Hämta gränssnittsdefinitioner från den offentliga databasen

### <a name="cli"></a>CLI

Du kan använda Azure IoT-tillägget för Azure CLI för att hämta de vanliga gränssnitten från den offentliga modelldatabasen.

```azurecli
az iot pnp interface show --interface {InterfaceID}
```

```azurecli
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>VS Code

1. Öppna kommandopaletten genom att använda **Ctrl+Skift+P.**

1. Gå **in i Plug and Play** och välj sedan kommandot **IoT Plug and Play: Open Model Repository.** Välj **Offentlig databas**. Den offentliga modelldatabasen öppnas i VS-kod.

1. Ange gränssnittsnamnet i sökfältet i den offentliga modelldatabasen.

1. Om du vill skapa en lokal kopia av gränssnittet markerar du det i sökresultaten och väljer sedan **Hämta**.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om vanliga gränssnitt, här är några ytterligare resurser:

- [Digitalt tvillingdefinitionsspråk (DTDL)](https://aka.ms/DTDL)
- [C (enhets-SDK)](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
