---
title: Registrera en ny enhet från kommandoraden – Azure IoT Edge | Microsoft Docs
description: Använd IoT-tillägget för Azure CLI för att registrera en ny IoT Edge-enhet och hämta anslutningssträngen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/27/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 28f20157de603cf20cb33e9db569064f87922bf8
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081841"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli"></a>Registrera en ny Azure IoT Edge-enhet med Azure CLI

Innan du kan använda dina IoT-enheter med Azure IoT Edge, måste du registrera dem med IoT-hubben. När du registrerar en enhet får du en anslutningssträng som kan användas för att konfigurera din enhet för Edge-arbetsbelastningar. 

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) är en öppen källkod för flera plattformar kommandoradsverktyget för att hantera Azure-resurser som IoT Edge. Det gör det möjligt för dig att hantera Azure IoT Hub-resurser, tjänstinstanser för enhetsetablering och länkade hubbar rutan. Det nya IoT-tillägget får Azure CLI med funktioner som enhetshantering och full IoT Edge-funktion.

Den här artikeln visar hur du registrerar en ny IoT Edge-enhet med hjälp av Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i Azure-prenumerationen. 
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Azure CLI-version måste minst vara 2.0.24 eller senare. Validera med `az –-version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack. 
* Den [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Skapa en enhet

Använd följande kommando för att skapa en ny enhetsidentitet i IoT-hubben: 

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Det här kommandot innehåller tre parametrar:
* **id för enhet**: Ange ett beskrivande namn som är unik för din IoT-hubb.
* **hubbnamn**: Ange namnet på din IoT-hubb.
* **Edge-aktiverade**: den här parametern anger att enheten är för användning med IoT Edge.

   ![AZ iot hub-enhetsidentitet skapa utdata](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Visa alla enheter

Använd följande kommando för att visa alla enheter i IoT hub:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Alla enheter som registreras som en IoT Edge-enhet har egenskapen **capabilities.iotEdge** inställd **SANT**. 

## <a name="retrieve-the-connection-string"></a>Hämta anslutningssträngen

När du är redo att konfigurera din enhet, måste den anslutningssträng som länkar den fysiska enheten med sin identitet i IoT hub. Använd följande kommando för att returnera anslutningssträngen för en enskild enhet:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name] 
   ```

Enhetens ID-parametern är skiftlägeskänsligt. Kopiera inte citattecknen runt anslutningssträngen. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuerar moduler på en enhet med Azure CLI](how-to-deploy-modules-cli.md)
