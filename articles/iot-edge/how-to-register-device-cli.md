---
title: Registrera en ny Azure IoT Edge-enhet (CLI) | Microsoft Docs
description: Använd IoT-tillägget för Azure CLI 2.0 för att registrera en ny IoT Edge-enhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 07/27/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 451f4df31cd1c520b14227829923f72fe80c38c3
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325504"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli-20"></a>Registrera en ny Azure IoT Edge-enhet med Azure CLI 2.0

Innan du kan använda dina IoT-enheter med Azure IoT Edge, måste du registrera dem med IoT-hubben. När du registrerar en enhet får du en anslutningssträng som kan användas för att konfigurera din enhet för Edge-arbetsbelastningar. 

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) är ett kommandoradsverktyg med öppen källkod för flera plattformar, för hantering av Azure-resurser som IoT Edge. Det gör det möjligt för dig att hantera Azure IoT Hub-resurser, tjänstinstanser för enhetsetablering och länkade hubbar rutan. Med det nya IoT-tillägget får Azure CLI 2.0 enhetshantering och fullständig IoT Edge-funktionalitet.

Den här artikeln visar hur du registrerar en ny IoT Edge-enhet med Azure CLI 2.0.

## <a name="prerequisites"></a>Förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i Azure-prenumerationen. 
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Din Azure CLI 2.0-version måste vara 2.0.24 eller senare. Validera med `az –-version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack. 
* Den [IoT-tillägget för Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Skapa en enhet

Använd följande kommando för att skapa en ny enhetsidentitet i IoT-hubben: 

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Det här kommandot innehåller tre parametrar:
* **id för enhet**: Ange ett beskrivande namn som är unik för din IoT-hubb.
* **hubbnamn**: Ange namnet på din IoT-hubb.
* **Edge-aktiverade**: den här parametern anger att enheten är för användning med IoT Edge.

   ![Skapa IoT Edge-enhet](./media/how-to-register-device-cli/Create-edge-device.png)

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

Lär dig hur du [distribuerar moduler på en enhet med Azure CLI 2.0](how-to-deploy-modules-cli.md)