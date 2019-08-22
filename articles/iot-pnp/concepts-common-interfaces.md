---
title: Vanliga gränssnitt – IoT Plug and Play Preview | Microsoft Docs
description: Beskrivning av vanliga gränssnitt för IoT Plug and Play-utvecklare
author: ChrisGMsft
ms.author: chrisgre
ms.date: 07/16/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 848210509bf9ab0ffec35004cbb07e39d6de1bc0
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879609"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>IoT Plug and Play förhandsgranska vanliga gränssnitt

Alla IoT Plug and Play-enheter förväntas implementera några vanliga gränssnitt. Vanliga gränssnitt fördelar IoT-lösningar eftersom de ger konsekventa funktioner. För [certifiering](tutorial-build-device-certification.md) krävs att enheten implementerar flera vanliga gränssnitt. Du kan hämta vanliga gränssnitts definitioner från den globala modellens lagrings plats.

## <a name="summary-of-common-interfaces"></a>Sammanfattning av vanliga gränssnitt

| Name | id | Beskrivning | Implementerad av Azure IoT SDK | Måste deklareras i kapacitets modellen |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Modell information | urn: azureiot: ModelDiscovery: ModelInformation: 1 | För att enheter ska kunna deklarera kapacitets modellens ID och gränssnitt. Krävs för alla IoT Plug and Play-enheter. | Ja | Nej |
| Digital, dubbla klient-SDK-information | urn: azureiot: klient: SDKInformation: 1 | Klient-SDK för att ansluta enheten till Azure. Krävs för [certifiering](tutorial-build-device-certification.md) | Ja | Nej |
| Enhetsinformation | urn: azureiot: DeviceManagement: DeviceInformation: 1 | Information om maskin vara och operativ system om enheten. Krävs för [certifiering](tutorial-build-device-certification.md) | Nej | Ja |
| Modell definition | urn: azureiot: ModelDiscovery: ModelDefinition: 1 | För att enheter ska kunna deklarera den fullständiga definitionen för dess kapacitets modell och gränssnitt. Måste implementeras när modell definitioner inte finns i en modell databas. | Nej | Ja |
| Digital, dubbel | urn: azureiot: ModelDiscovery: DigitalTwin: 1 | För att lösnings utvecklare ska kunna hämta kapacitets modellens ID och gränssnitts-ID: n för en digital. Det här gränssnittet är inte deklarerat eller implementerat av en IoT Plug and Play-enhet. | Nej | Nej |

- Implementeras av Azure IoT SDK – om Azure IoT SDK implementerar de funktioner som har deklarerats i gränssnitten. IoT Plug and Play-enheter som använder Azure IoT SDK behöver inte implementera det här gränssnittet.
- Måste deklareras i kapacitets modellen-om ja, måste det här gränssnittet deklareras i `"implements":` avsnittet i enhetens kapacitets modell för den här IoT plug and Play-enheten.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Hämta gränssnitts definitioner från den offentliga lagrings platsen

### <a name="cli"></a>CLI

Du kan använda Azure IoT-tillägget för Azure CLI för att hämta de vanligaste gränssnitten från den globala modellens lagrings plats.

```cmd/sh
az iot pnp interface show --interface {InterfaceID} --login {ModelRepoConnectionString}
```

```cmd/sh
az iot pnp model show --interface {InterfaceID} --login {ModelRepoConnectionString}
```

### <a name="vs-code"></a>VS-kod

1. Använd **CTRL + SKIFT + P** för att öppna kommando paletten.

1. Ange **Plug and Play** och välj **IoT-plug and Play: Öppna modell databas** kommandot. Välj **lagrings plats för global modell**. Den globala modellens lagrings plats öppnas i VS Code.

1. Ange gränssnitts namnet i Sök fältet i den globala modellens databas.

1. Om du vill skapa en lokal kopia av gränssnittet markerar du det i Sök resultaten och väljer sedan **Hämta**.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om vanliga gränssnitt finns det några ytterligare resurser:

- [Digitalt elektroniskt definitions språk (DTDL)](https://aka.ms/DTDL)
- [C-enhets-SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST API](https://docs.microsoft.com/rest/api/iothub/device)
