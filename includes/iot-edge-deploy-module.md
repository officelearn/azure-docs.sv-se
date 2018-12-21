---
title: ta med fil
description: ta med fil
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/7/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 1a750a97cdc940c0f0a3d7e33d6be0d33f811425
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53108030"
---
En av de viktigaste funktionerna i Azure IoT Edge är möjligheten att distribuera moduler till IoT Edge-enheter från molnet. En IoT Edge-modul är ett körbart paket som implementeras som en container. I det här avsnittet ska vi distribuera en färdig modul från avsnittet [IoT Edge-moduler i Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Den här modulen genererar telemetri för den simulerade enheten.

1. På Azure-portalen anger du `Simulated Temperature Sensor` i sökningen öppnar Marketplace-resultatet.

   ![Simulerad temperatursensor i sökning på Azure-portalen](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. I fältet **Prenumeration** väljer du prenumerationen med IoT-hubben som du använder, om den inte redan är vald.

3. I fältet **IoT Hub** väljer du namnet på IoT-hubben som du använder, om det inte redan är valt.

4. Klicka på **Hitta enhet**, välj din IoT Edge-enhet (med namnet `myEdgeDevice`) och välj sedan **Skapa**.

5. I steget **Lägg till moduler** i guiden klickar du på modulen **SimulatedTemperatureSensor** för att verifiera dess konfigurationsinställningar, klickar på **Spara** och väljer **Nästa**.

6. I steget **Ange rutter** i guiden ska du verifiera att vägarna är rätt konfigurerade med standardvägen som skickar alla meddelanden från alla moduler till IoT Hub.(`$upstream`). Om du inte har det lägger du till följande kod och väljer sedan **Nästa**.

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```

7. I steget **Granska distribution** i guiden väljer du **Skicka**.

8. Återgå till informationssidan om enheten och välj **Uppdatera**. Förutom edgeAgent-modulen som skapades när du startade tjänsten för första gången ska du även se en annan körningsmodul med namnet **edgeHub** och modulen **SimulatedTemperatureSensor**.

   Det kan ta några minuter för de nya modulerna att visas. IoT Edge-enheten måste hämta sin nya distributionsinformation från molnet, starta containrarna och rapportera sin nya status tillbaka till IoT Hub. 

   ![Visa SimulatedTemperatureSensor i listan över distribuerade moduler](./media/iot-edge-deploy-module/deployed-modules-marketplace-temp.png)
