---
title: inkludera fil
description: inkludera fil
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/30/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 76c8eb7acf20d8cf68d3573defd947efbc6c3c43
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "85801744"
---
En av de viktigaste funktionerna i Azure IoT Edge är möjligheten att distribuera kod till IoT Edge-enheter från molnet. *IoT Edge moduler* är körbara paket som implementeras som behållare. I det här avsnittet ska du distribuera en fördefinierad modul från [avsnittet IoT Edge moduler på Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) direkt från azure-IoT Hub.

Den modul som du distribuerar i det här avsnittet simulerar en sensor och skickar genererade data. Modulen är användbar kod när du kör igång med IoT Edge eftersom du kan använda simulerade data för utveckling och testning. Om du vill se exakt vad den här modulen gör kan du visa [källkoden för den simulerade temperatursensorn](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Använd följande steg för att distribuera din första modul från Azure Marketplace:

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT-hubb.

1. I menyn i den vänstra rutan, under **Automatisk enhets hantering**, väljer du **IoT Edge**.

1. Klicka på enhets-ID: t för mål enheten i listan med enheter.

1. I den övre stapeln väljer du **Ange moduler**.

   ![Välj Ange moduler från sidan enhets information](./media/iot-edge-deploy-module/select-set-modules.png)

1. I avsnittet **IoT Edge moduler** på sidan klickar du på **Lägg till** och väljer **Marketplace-modul** på den nedrullningsbara menyn.

   ![Lägg till Marketplace-modul](./media/iot-edge-deploy-module/add-marketplace-module.png)

1. I **IoT Edge module Marketplace**söker du efter "simulerad temperatur sensor" och väljer den modulen.

1. Observera att modulen SimulatedTemperatureSensor läggs till i avsnittet IoT Edge moduler, med önskad status **körs**.

   Välj **Nästa: vägar** för att fortsätta till nästa steg i guiden.

   ![Gå vidare till nästa steg när modulen temperatur sensor visas](./media/iot-edge-deploy-module/view-temperature-sensor-next-routes.png)

1. På fliken **vägar** i guiden kan du definiera hur meddelanden skickas mellan moduler och IoT Hub. Vägar konstrueras med hjälp av namn/värde-par. Du bör se två vägar på den här sidan. Standard vägen med namnet **Route** skickar alla meddelanden till IoT Hub (som kallas `$upstream` ). En andra väg med namnet **SimulatedTemperatureSensorToIoTHub** skapades automatiskt när du lade till modulen från Marketplace. Den här vägen skickar alla meddelanden specifikt från modulen simulerad temperatur till IoT Hub. Du kan ta bort standard vägen eftersom den är redundant i det här fallet.

   Välj **Nästa: granska + skapa** för att fortsätta till nästa steg i guiden.

   ![Ta bort standard vägen och gå sedan vidare till nästa steg](./media/iot-edge-deploy-module/delete-route-next-review-create.png)

1. På fliken **Granska + skapa** i guiden kan du förhandsgranska JSON-filen som definierar alla moduler som distribueras till din IoT Edge-enhet. Observera att **SimulatedTemperatureSensor** -modulen ingår samt de två modulerna för körning, **edgeAgent** och **edgeHub**. Välj **skapa** när du är klar med att granska.

   När du skickar en ny distribution till en IoT Edge-enhet distribueras ingenting till enheten. I stället frågar enheten regelbundet IoT Hub efter nya instruktioner. Om enheten hittar ett uppdaterat distributionsmanifest använder den informationen om den nya distributionen för att hämta modulavbildningarna från molnet och börjar sedan köra modulerna lokalt. Den här processen kan ta ett par minuter.

1. När du har skapat modulens distributions information återgår guiden till enhets informations sidan. På sidan enhets information visar du distributions status på fliken **moduler** . Tre moduler ska listas: $edgeAgent, $edgeHub och SimulatedTemperatureSensor. Om en eller fler av modulerna visas som angivna i distributionen men inte rapporteras av enheten håller IoT Edge-enheten fortfarande på att starta dem. Vänta en stund och välj **Uppdatera** överst på sidan.

   ![Visa SimulatedTemperatureSensor i listan över distribuerade moduler](./media/iot-edge-deploy-module/view-deployed-modules.png)
