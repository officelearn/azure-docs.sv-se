---
title: Översätt modbus-protokoll med gatewayer – Azure IoT Edge | Microsoft Docs
description: Tillåt att enheter som använder Modbus TCP kan kommunicera med Azure IoT Hub genom att skapa en IoT Edge-gatewayenhet
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 1c9855f982b888e8e1d68bfe5233983db8c826ad
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873771"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Ansluta Modbus TCP-enheter via en gateway för IoT Edge-enhet

Om du vill ansluta IoT-enheter som använder Modbus TCP eller RTU-protokoll till en Azure IoT Hub ska du använda en IoT Edge-enhet som gateway. Gatewayenheten läser data från Modbus-enheterna och kommunicerar sedan dessa data till molnet med ett protokoll som stöds.

![Modbus-enheter ansluter till IoT Hub via IoT Edge-gateway](./media/deploy-modbus-gateway/diagram.png)

Den här artikeln beskriver hur du skapar en egen containeravbildning för en Modbus-modul (eller så kan du använda ett fördefinierat exempel) och sedan distribuera den till IoT Edge-enheten som fungerar som gateway.

Den här artikeln förutsätter att du använder Modbus TCP-protokollet. Mer information om hur du konfigurerar modulen så att den stöder Modbus RTU finns i den [Azure IoT Edge Modbus-modul](https://github.com/Azure/iot-edge-modbus) projekt på GitHub.

## <a name="prerequisites"></a>Förutsättningar
* En Azure IoT Edge-enhet. En genomgång om hur du ställer in en finns i [distribuera Azure IoT Edge på Windows](quickstart.md) eller [Linux](quickstart-linux.md).
* Primärnyckelns anslutningssträng för IoT Edge-enheten.
* En fysisk eller simulerad Modbus-enhet som stöder Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Förbereda en Modbus-container

Om du vill testa Modbus-gatewayens funktioner har Microsoft en exempelmodul som du kan använda. Du kan komma åt modulen från Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview), eller med du bildens URI, **mcr.microsoft.com/azureiotedge/modbus:1.0**.

Om du vill skapa en egen modul och anpassa den för din miljö, det finns en öppen källkod [Azure IoT Edge Modbus-modul](https://github.com/Azure/iot-edge-modbus) projekt på GitHub. Följ vägledningarna i projektet för att skapa en egen containeravbildning. Om du har skapat en egen behållaravbildning Se [utveckla C# moduler i Visual Studio](how-to-visual-studio-develop-csharp-module.md) eller [utveckla moduler i Visual Studio Code](how-to-vs-code-develop-module.md). Dessa artiklar innehåller anvisningar om att skapa nya moduler och publicera behållaravbildningar till ett register.

## <a name="try-the-solution"></a>Prova lösningen

Det här avsnittet beskriver distribuera Microsofts exempel Modbus-modulen till IoT Edge-enhet.

1. Gå till IoT-hubben på [Azure-portalen](https://portal.azure.com/).

2. Gå till **IoT Edge** och klicka på din IoT Edge-enhet.

3. Välj **Ange moduler**.

4. Lägg till Modbus-modulen:

   1. Klicka på **Lägg till** och välj **IoT Edge-modul**.

   2. I fältet **Namn** skriver du ”modbus”.

   3. I fältet **Bild** anger du bildens URI för exempelcontainern: `mcr.microsoft.com/azureiotedge/modbus:1.0`.

   4. Markera rutan **Aktivera** för att uppdatera önskade egenskaper för modultvillingen.

   5. Kopiera följande JSON till textrutan. Ändra värdet för **SlaveConnection** till Modbus-enhetens IPv4-adress.

      ```JSON
      {
        "properties.desired":{
          "PublishInterval":"2000",
          "SlaveConfigs":{
            "Slave01":{
              "SlaveConnection":"<IPV4 address>","HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{
                "Op01":{
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"400001",
                  "Count":"2",
                  "DisplayName":"Voltage"
                }
              }
            }
          }
        }
      }
      ```

   6. Välj **Spara**.

5. I steget **Lägg till moduler** väljer du **Nästa**.

7. I steget för att **ange vägar** kopierar du följande JSON till textrutan. Den här vägen skickar alla meddelanden som samlats in av Modbus-modulen till IoT Hub. I den här vägen är ”'modbusOutput” slutpunkten som Modbus-modulen använder för att mata ut data och ”upstream” är en specialdestination som uppmanar IoT Edge hub att skicka meddelanden till IoT Hub.
   ```JSON
   {
    "routes": {
      "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
    }
   }
   ```

8. Välj **Nästa**.

9. Välj **Skicka** i steget **Granska distribution**.

10. Återgå till informationssidan om enheten och välj **Uppdatera**. Du bör se den nya **modbus** modulen körs tillsammans med IoT Edge-körningen.

## <a name="view-data"></a>Visa data
Visa data som kommer genom modbus-modulen:
```cmd/sh
iotedge logs modbus
```

Du kan också visa telemetri som enheten skickar med hjälp av den [Azure IoT Hub Toolkit-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (tidigare Azure IoT Toolkit-tillägget).

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur IoT Edge-enheter kan fungera som gatewayer i [skapa en IoT Edge-enhet som fungerar som en transparent gateway](./how-to-create-transparent-gateway.md).
- Mer information om hur IoT Edge-moduler fungerar finns i [förstå Azure IoT Edge-moduler](iot-edge-modules.md).
