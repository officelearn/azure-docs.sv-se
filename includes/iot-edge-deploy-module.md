---
title: ta med fil
description: ta med fil
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/31/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: abbde0cb8137650faad563248c8fafe7dc0f469a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75564778"
---
En av de viktigaste funktionerna i Azure IoT Edge är möjligheten att distribuera kod till IoT Edge-enheter från molnet. **IoT Edge-moduler** är körbara paket som implementeras som containrar. I det här avsnittet distribuerar du en färdigmodul från [avsnittet IoT Edge Modules på Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) direkt från din Azure IoT Hub.

Den modul som du distribuerar i det här avsnittet simulerar en sensor och skickar genererade data. Modulen är användbar kod när du kör igång med IoT Edge eftersom du kan använda simulerade data för utveckling och testning. Om du vill se exakt vad den här modulen gör kan du visa [källkoden för den simulerade temperatursensorn](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Använd följande steg för att distribuera din första modul från Azure Marketplace:

1. Logga in på [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb.

1. Välj **IoT Edge**på menyn i den vänstra rutan under **Automatisk enhetshantering**.

1. Klicka på enhets-ID för målenheten från listan över enheter.

1. Välj **Ange moduler**i det övre fältet .

1. Klicka på **Lägg till**i avsnittet **IoT-kantmoduler** på sidan .

1. Välj **Marketplace-modul**på den nedrullningsbara menyn.

   ![Simulerad temperatursensor i sökning på Azure-portalen](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

1. Sök efter "Simulerad temperatursensor" på **IoT Edge Module Marketplace**och välj den modulen.

1. Observera att modulen SimuleradTemperatureSensor är ifylld automatiskt. I självstudierna använder du den här sidan för att lägga till ytterligare moduler i distributionen. I den här snabbstarten distribuerar du enbart den här modulen. Inga autentiseringsuppgifter krävs eftersom de är offentliga.

   ![Ställa in moduler på enheten](./media/iot-edge-deploy-module/set-modules-on-device.png)

   Välj **Nästa: Rutter** för att fortsätta till nästa steg i guiden.

1. På fliken **Rutter** i guiden definierar du hur meddelanden skickas mellan moduler och IoT Hub. Meddelanden skapas med namn-/värdepar. I snabbstarten vill du att alla meddelanden från alla moduler går till IoT Hub (`$upstream`). Om den inte fylls i automatiskt lägger du till följande kod för **Värdet** för **namnet:** `upstream`

   ```sql
    FROM /messages/* INTO $upstream
   ```

   Välj **Nästa: Granska + skapa** för att fortsätta till nästa steg i guiden.

1. På fliken **Granska + skapa** i guiden kan du förhandsgranska JSON-filen som definierar alla moduler som distribueras till din IoT Edge-enhet. Observera att modulen **SimulatedTemperatureSensor** innefattas samt två ytterligare systemmoduler som kallas **edgeAgent** och **edgeHub**. Välj **Skapa** när du är klar med granskningen.

   När du skickar en ny distribution till en IoT Edge-enhet distribueras ingenting till enheten. I stället frågar enheten regelbundet IoT Hub efter nya instruktioner. Om enheten hittar ett uppdaterat distributionsmanifest använder den informationen om den nya distributionen för att hämta modulavbildningarna från molnet och börjar sedan köra modulerna lokalt. Den här processen kan ta ett par minuter.

1. När du har skapat information om moduldistributionen returneras du till **IoT Edge-sidan** i IoT-hubben. Välj din enhet från listan över IoT Edge-enheter för att se dess information.

1. Bläddra ned till fliken Moduler på sidan **Enhetsinformation.** Tre moduler ska listas: $edgeAgent, $edgeHub och SimulatedTemperatureSensor. Om en eller fler av modulerna visas som angivna i distributionen men inte rapporteras av enheten håller IoT Edge-enheten fortfarande på att starta dem. Vänta en stund och välj sedan **Uppdatera** överst på sidan.

   ![Visa SimulatedTemperatureSensor i listan över distribuerade moduler](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
