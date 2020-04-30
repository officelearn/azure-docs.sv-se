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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "75564778"
---
En av de viktigaste funktionerna i Azure IoT Edge är möjligheten att distribuera kod till IoT Edge-enheter från molnet. **IoT Edge-moduler** är körbara paket som implementeras som containrar. I det här avsnittet ska du distribuera en fördefinierad modul från [avsnittet IoT Edge moduler på Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) direkt från azure-IoT Hub.

Den modul som du distribuerar i det här avsnittet simulerar en sensor och skickar genererade data. Modulen är användbar kod när du kör igång med IoT Edge eftersom du kan använda simulerade data för utveckling och testning. Om du vill se exakt vad den här modulen gör kan du visa [källkoden för den simulerade temperatursensorn](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Använd följande steg för att distribuera din första modul från Azure Marketplace:

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT-hubb.

1. I menyn i den vänstra rutan, under **Automatisk enhets hantering**, väljer du **IoT Edge**.

1. Klicka på enhets-ID: t för mål enheten i listan med enheter.

1. I den övre stapeln väljer du **Ange moduler**.

1. I avsnittet **IoT Edge moduler** på sidan klickar du på **Lägg till**.

1. Välj **Marketplace-modul**på den nedrullningsbara menyn.

   ![Simulerad temperatursensor i sökning på Azure-portalen](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

1. I **IoT Edge module Marketplace**söker du efter "simulerad temperatur sensor" och väljer den modulen.

1. Observera att modulen SimulatedTemperatureSensor fylls i automatiskt. I självstudierna använder du den här sidan för att lägga till ytterligare moduler i distributionen. I den här snabbstarten distribuerar du enbart den här modulen. Inga autentiseringsuppgifter krävs eftersom den är offentlig.

   ![Ange moduler på enheten](./media/iot-edge-deploy-module/set-modules-on-device.png)

   Välj **Nästa: vägar** för att fortsätta till nästa steg i guiden.

1. På fliken **vägar** i guiden definierar du hur meddelanden ska skickas mellan moduler och IoT Hub. Meddelanden skapas med hjälp av namn/värde-par. I snabbstarten vill du att alla meddelanden från alla moduler går till IoT Hub (`$upstream`). Om den inte fylls i automatiskt lägger du till följande kod för **värdet** för **namnet** `upstream`:

   ```sql
    FROM /messages/* INTO $upstream
   ```

   Välj **Nästa: granska + skapa** för att fortsätta till nästa steg i guiden.

1. På fliken **Granska + skapa** i guiden kan du förhandsgranska JSON-filen som definierar alla moduler som distribueras till din IoT Edge-enhet. Observera att modulen **SimulatedTemperatureSensor** innefattas samt två ytterligare systemmoduler som kallas **edgeAgent** och **edgeHub**. Välj **skapa** när du är klar med att granska.

   När du skickar en ny distribution till en IoT Edge-enhet distribueras ingenting till enheten. I stället frågar enheten regelbundet IoT Hub efter nya instruktioner. Om enheten hittar ett uppdaterat distributionsmanifest använder den informationen om den nya distributionen för att hämta modulavbildningarna från molnet och börjar sedan köra modulerna lokalt. Den här processen kan ta ett par minuter.

1. När du har skapat modulens distributions information återgår guiden till **IoT Edge** sida i IoT Hub. Välj din enhet från listan över IoT Edge-enheter för att se dess information.

1. Rulla ned till fliken **moduler** på sidan enhets information. Tre moduler ska listas: $edgeAgent, $edgeHub och SimulatedTemperatureSensor. Om en eller fler av modulerna visas som angivna i distributionen men inte rapporteras av enheten håller IoT Edge-enheten fortfarande på att starta dem. Vänta en stund och välj sedan **Uppdatera** överst på sidan.

   ![Visa SimulatedTemperatureSensor i listan över distribuerade moduler](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
