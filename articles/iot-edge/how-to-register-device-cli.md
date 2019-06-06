---
title: Registrera en ny enhet från kommandoraden – Azure IoT Edge | Microsoft Docs
description: Använd IoT-tillägget för Azure CLI för att registrera en ny IoT Edge-enhet och hämta anslutningssträngen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: de00c317483da9bcd93bb2e2505350d787385925
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495393"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli"></a>Registrera en ny Azure IoT Edge-enhet med Azure CLI

Innan du kan använda dina IoT-enheter med Azure IoT Edge, måste du registrera dem med IoT-hubben. När du registrerar en enhet får du en anslutningssträng som kan användas för att konfigurera din enhet för IoT Edge-arbetsbelastningar.

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) är en öppen källkod för flera plattformar kommandoradsverktyget för att hantera Azure-resurser som IoT Edge. Det gör det möjligt för dig att hantera Azure IoT Hub-resurser, tjänstinstanser för enhetsetablering och länkade hubbar rutan. Det nya IoT-tillägget får Azure CLI med funktioner som enhetshantering och full IoT Edge-funktion.

Den här artikeln visar hur du registrerar en ny IoT Edge-enhet med hjälp av Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i Azure-prenumerationen.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Azure CLI-version måste minst vara 2.0.24 eller senare. Validera med `az –-version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack.
* Den [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Skapa en enhet

Använd den [az iot hub-enhetsidentitet skapa](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) kommando för att skapa en ny enhetsidentitet i IoT hub. Exempel:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Det här kommandot innehåller tre parametrar:

* **id för enhet**: Ange ett beskrivande namn som är unik för din IoT-hubb.

* **hubbnamn**: Ange namnet på din IoT-hubb.

* **Edge-aktiverade**: Den här parametern anger att enheten är för användning med IoT Edge.

   ![AZ iot hub-enhetsidentitet skapa utdata](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Visa alla enheter

Använd den [az iot hub-enhetsidentitet lista](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) kommando för att visa alla enheter i IoT hub. Exempel:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Alla enheter som registreras som en IoT Edge-enhet har egenskapen **capabilities.iotEdge** inställd **SANT**.

## <a name="retrieve-the-connection-string"></a>Hämta anslutningssträngen

När du är redo att konfigurera din enhet, måste den anslutningssträng som länkar den fysiska enheten med sin identitet i IoT hub. Använd den [az iot hub-enhetsidentitet show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) kommando för att returnera anslutningssträngen för en enskild enhet:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

Värdet för den `device-id` parametern är skiftlägeskänsligt. Kopiera inte citattecknen runt anslutningssträngen.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuerar moduler på en enhet med Azure CLI](how-to-deploy-modules-cli.md).
