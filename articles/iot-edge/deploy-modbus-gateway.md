---
title: Översätt modbusprotokoll med gateways – Azure IoT Edge | Microsoft-dokument
description: Tillåt att enheter som använder Modbus TCP kan kommunicera med Azure IoT Hub genom att skapa en IoT Edge-gatewayenhet
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 23fbbd87230ea0a0147dc9d90c77729f4d531e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511152"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Ansluta Modbus TCP-enheter via en IoT Edge-enhetsgateway

Om du vill ansluta IoT-enheter som använder Modbus TCP- eller RTU-protokoll till en Azure IoT-hubb kan du använda en IoT Edge-enhet som en gateway. Gatewayenheten läser data från Modbus-enheterna och kommunicerar sedan dessa data till molnet med ett protokoll som stöds.

![Modbus-enheter ansluter till IoT Hub via IoT Edge-gateway](./media/deploy-modbus-gateway/diagram.png)

Den här artikeln beskriver hur du skapar en egen containeravbildning för en Modbus-modul (eller så kan du använda ett fördefinierat exempel) och sedan distribuera den till IoT Edge-enheten som fungerar som gateway.

Den här artikeln förutsätter att du använder Modbus TCP-protokollet. Mer information om hur du konfigurerar modulen för att stödja Modbus RTU finns i [Azure IoT Edge Modbus-modulprojektet](https://github.com/Azure/iot-edge-modbus) på GitHub.

## <a name="prerequisites"></a>Krav

* En Azure IoT Edge-enhet. En genomgång av hur du konfigurerar en finns i [Distribuera Azure IoT Edge på Windows](quickstart.md) eller [Linux](quickstart-linux.md).
* Primärnyckelns anslutningssträng för IoT Edge-enheten.
* En fysisk eller simulerad Modbus-enhet som stöder Modbus TCP. Du måste känna till dess IPv4-adress.

## <a name="prepare-a-modbus-container"></a>Förbereda en Modbus-container

Om du vill testa Modbus-gatewayens funktioner har Microsoft en exempelmodul som du kan använda. Du kan komma åt modulen från Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)eller med avbildningen URI, `mcr.microsoft.com/azureiotedge/modbus:1.0`.

Om du vill skapa en egen modul och anpassa den för din miljö finns det ett [Azure IoT Edge Modbus-modulprojekt](https://github.com/Azure/iot-edge-modbus) med öppen källkod på GitHub. Följ vägledningarna i projektet för att skapa en egen containeravbildning. Information om hur du skapar en behållaravbildning finns [i Utveckla C#-moduler i Visual Studio](how-to-visual-studio-develop-csharp-module.md) eller Utveckla moduler i Visual Studio [Code](how-to-vs-code-develop-module.md). Dessa artiklar innehåller instruktioner om hur du skapar nya moduler och publicerar behållaravbildningar till ett register.

## <a name="try-the-solution"></a>Prova lösningen

Det här avsnittet går igenom distributionen av Microsofts exempelmodul Modbus till din IoT Edge-enhet.

1. Gå till IoT-hubben på [Azure-portalen](https://portal.azure.com/).

2. Gå till **IoT Edge** och klicka på din IoT Edge-enhet.

3. Välj **Ange moduler**.

4. Lägg till Modbus-modulen i avsnittet **IoT Edge Modules:**

   1. Klicka på listrutan **Lägg till** och välj **Marketplace-modul**.
   2. Sök `Modbus` efter och välj **Modbus TCP-modul** från Microsoft.
   3. Modulen konfigureras automatiskt för din IoT Hub och visas i listan över IoT Edge-moduler. Rutterna konfigureras också automatiskt. Välj **Granska + skapa**.
   4. Granska distributionsmanifestet och välj **Skapa**.

5. Välj Modbus-modulen, `ModbusTCPModule`i listan och välj fliken **Modultvillinginställningar.** Den nödvändiga JSON för modulen dubbla önskade egenskaper är auto populated.

6. Leta efter egenskapen **SlaveConnection** i JSON och ange dess värde till IPv4-adressen för din Modbus-enhet.

7. Välj **Uppdatera**.

8. Välj **Granska + skapa**, granska distributionen och välj sedan **Skapa**.

9. Återgå till informationssidan om enheten och välj **Uppdatera**. Du bör se `ModbusTCPModule` den nya modulen som körs tillsammans med IoT Edge-körningen.

## <a name="view-data"></a>Visa data

Visa data som kommer via Modbus-modulen:

```cmd/sh
iotedge logs modbus
```

Du kan också visa den telemetri som enheten skickar med hjälp av [Azure IoT Hub-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (tidigare Azure IoT Toolkit-tillägg).

## <a name="next-steps"></a>Nästa steg

* Mer information om hur IoT Edge-enheter kan fungera som gateways finns i [Skapa en IoT Edge-enhet som fungerar som en transparent gateway](./how-to-create-transparent-gateway.md).
* Mer information om hur IoT Edge-moduler fungerar finns i [Förstå Azure IoT Edge-moduler](iot-edge-modules.md).
