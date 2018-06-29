---
title: Registrera en ny Azure IoT Edge-enhet (CLI) | Microsoft Docs
description: Använd IoT-tillägget för Azure CLI 2.0 för att registrera en ny IoT Edge-enhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 05/30/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a6935dfe5b32364e402017cd3005ab17969ce1ce
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036384"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli-20"></a>Registrera en ny Azure IoT Edge-enhet med Azure CLI 2.0

Innan du kan använda din IoT-enheter med Azure IoT gräns, måste du registrera dem med din IoT-hubb. När du registrerar en enhet kan få du en anslutningssträng som kan användas för att konfigurera din enhet för Edge arbetsbelastningar. 

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) är ett kommandoradsverktyg med öppen källkod för flera plattformar, för hantering av Azure-resurser som IoT Edge. Det kan du hantera Azure IoT Hub resurser enheten etablering tjänstinstanser och länkade hubs direkt. Med det nya IoT-tillägget får Azure CLI 2.0 enhetshantering och fullständig IoT Edge-funktionalitet.

Den här artikeln visar hur du registrerar en ny IoT Edge-enhet som använder Azure CLI 2.0.

## <a name="prerequisites"></a>Förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i din Azure-prenumeration. 
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Din Azure CLI 2.0-version måste vara 2.0.24 eller senare. Validera med `az –-version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack. 
* Den [IoT-tillägg för Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Skapa en enhet

Använd följande kommando för att skapa en ny enhetsidentitet i din IoT-hubb: 

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Det här kommandot innehåller tre parametrar:
* **enhetens id**: Ange ett beskrivande namn som är unik för din IoT-hubb.
* **hubbnamn**: Ange namnet på din IoT-hubb.
* **Edge-aktiverade**: den här parametern försäkrar att enheten är för användning med IoT kant.

   ![Skapa IoT Edge-enhet](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Visa alla enheter

Använd följande kommando för att visa alla enheter i din IoT-hubb:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Alla enheter som registrerats som en IoT-enhet har egenskapen **capabilities.iotEdge** inställd på **SANT**. 

## <a name="retrieve-the-connection-string"></a>Hämta anslutningssträngen

När du är redo att konfigurera din enhet måste den anslutningssträng som länkar den fysiska enheten med sin identitet i IoT-hubben. Använd följande kommando för att returnera anslutningssträngen för en enskild enhet:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name] 
   ```

Enheten ID-parametern är skiftlägeskänslig. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuerar moduler på en enhet med Azure CLI 2.0](how-to-deploy-modules-cli.md)