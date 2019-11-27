---
title: Översätt modbus-protokoll med gatewayer – Azure IoT Edge | Microsoft Docs
description: Tillåt att enheter som använder Modbus TCP kan kommunicera med Azure IoT Hub genom att skapa en IoT Edge-gatewayenhet
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: kgremban
ms.openlocfilehash: 649c7f620b83464d1bb56cf4b8191b0747105f01
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457206"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Ansluta Modbus TCP-enheter via en gateway för IoT Edge-enhet

Om du vill ansluta IoT-enheter som använder Modbus TCP eller RTU-protokoll till en Azure IoT Hub ska du använda en IoT Edge-enhet som gateway. Gatewayenheten läser data från Modbus-enheterna och kommunicerar sedan dessa data till molnet med ett protokoll som stöds.

![Modbus-enheter ansluter till IoT Hub via IoT Edge Gateway](./media/deploy-modbus-gateway/diagram.png)

Den här artikeln beskriver hur du skapar en egen containeravbildning för en Modbus-modul (eller så kan du använda ett fördefinierat exempel) och sedan distribuera den till IoT Edge-enheten som fungerar som gateway.

Den här artikeln förutsätter att du använder Modbus TCP-protokollet. Mer information om hur du konfigurerar modulen så att den stöder Modbus-RTU finns i [Azure IoT Edge Modbus module](https://github.com/Azure/iot-edge-modbus) -projekt på GitHub.

## <a name="prerequisites"></a>Krav
* En Azure IoT Edge-enhet. En genom gång av hur du konfigurerar en sådan finns i [distribuera Azure IoT Edge på Windows](quickstart.md) eller [Linux](quickstart-linux.md).
* Primärnyckelns anslutningssträng för IoT Edge-enheten.
* En fysisk eller simulerad Modbus-enhet som stöder Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Förbereda en Modbus-container

Om du vill testa Modbus-gatewayens funktioner har Microsoft en exempelmodul som du kan använda. Du kan komma åt modulen från Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)eller med avbildnings-URI: n, **MCR.Microsoft.com/azureiotedge/Modbus:1.0**.

Om du vill skapa en egen modul och anpassa den för din miljö finns det ett projekt med öppen källkod [Azure IoT Edge Modbus-modul](https://github.com/Azure/iot-edge-modbus) på GitHub. Följ vägledningarna i projektet för att skapa en egen containeravbildning. Information om hur du skapar en behållar avbildning finns i [utveckla C# moduler i Visual Studio](how-to-visual-studio-develop-csharp-module.md) eller [utveckla moduler i Visual Studio Code](how-to-vs-code-develop-module.md). Dessa artiklar innehåller instruktioner om hur du skapar nya moduler och publicerar behållar avbildningar till ett register.

## <a name="try-the-solution"></a>Testa lösningen

Det här avsnittet beskriver hur du distribuerar Microsofts exempel-Modbus-modul till din IoT Edge-enhet.

1. Gå till IoT-hubben på [Azure-portalen](https://portal.azure.com/).

2. Gå till **IoT Edge** och klicka på IoT Edge enheten.

3. Välj **Ange moduler**.

4. Lägg till Modbus-modulen:

   1. Klicka på **Lägg till** och välj **IoT Edge modul**.

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
                  "StartAddress":"40001",
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

7. I steget för att **ange vägar** kopierar du följande JSON till textrutan. Den här vägen skickar alla meddelanden som samlats in av Modbus-modulen till IoT Hub. I den här vägen är **modbusOutput** den slut punkt som Modbus-modulen använder för att mata ut data och **$upstream** är ett särskilt mål som talar IoT Edge Hub för att skicka meddelanden till IoT Hub.

   ```JSON
   {
     "routes": {
       "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
     }
   }
   ```

8. Välj **Nästa**.

9. Välj **Skicka** i steget **Granska distribution**.

10. Återgå till informationssidan om enheten och välj **Uppdatera**. Du bör se den nya **Modbus** -modulen som körs tillsammans med IoT Edge Runtime.

## <a name="view-data"></a>Visa data
Visa data som kommer genom modbus-modulen:
```cmd/sh
iotedge logs modbus
```

Du kan också Visa telemetri som enheten skickar med hjälp av [tillägget Azure IoT Hub Toolkit för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (tidigare Azure IoT Toolkit-tillägget).

## <a name="next-steps"></a>Nästa steg

- Mer information om hur IoT Edge enheter kan fungera som gatewayer finns i [skapa en IoT Edge enhet som fungerar som en transparent Gateway](./how-to-create-transparent-gateway.md).
- Mer information om hur IoT Edge moduler fungerar finns i [förstå Azure IoT Edge moduler](iot-edge-modules.md).
