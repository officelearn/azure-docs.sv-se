---
title: Arbeta med flera IoT kant-moduler i Visual Studio Code | Microsoft Docs
description: Distribuera Azure Machine Learning som en modul till en insticksenhet
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0ea2dc723c674e7119b6ef38771a73ff4c11e98d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Utveckla en kant för IoT-lösning med flera moduler i Visual Studio Code - förhandsgranskning
Du kan använda Visual Studio-koden för att utveckla kant för IoT-lösning med flera moduler. Den här självstudiekursen vägleder dig genom att skapa, uppdatera och distribuera en lösning för IoT kant som bara skicka vidare sensordata på simulerade IoT gränsenheten i Visual Studio-koden. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Använd Visual Studio-koden för att skapa en gräns för IoT-lösning
> * Använda VS-kod för att lägga till en ny modul till verkliga IoT kant lösning. 
> * Distribuera lösningen för IoT kant (flera moduler) till din IoT-Edge-enhet
> * Visa genererade data

## <a name="prerequisites"></a>Förutsättningar
* Slutföra nedan självstudier
  * [Distribuera C#-modulen](tutorial-csharp-module.md)
  * [Distribuera C#-funktion](tutorial-deploy-function.md)
  * [Distribuera Python-modul](tutorial-python-module.md)
* [Docker för VS kod](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) med explorer-integrering för att hantera avbildningar och behållare.


## <a name="prepare-your-first-iot-edge-solution"></a>Förbered din första kant för IoT-lösning
1. Skriv i VS kod kommandot paletten och kör kommandot **kant: kant för ny IoT-lösningen**. Välj arbetsytemappen, ange lösningens namn (standardnamnet är **EdgeSolution**), och skapa en C#-modul (**SampleModule**) som den första användarmodulen i den här lösningen. Du måste också ange Docker avbildningslagringsplatsen för din första modulen. Standard avbildningslagringsplatsen baseras på en lokal Docker-registret (`localhost:5000/<first module name>`). Du kan också ändra den till Azure-behållaren registret eller Docker-hubb.

> [!NOTE]
> Om du använder en lokal Docker-registret, kontrollera registret igång genom att skriva kommandot `docker run -d -p 5000:5000 --restart=always --name registry registry:2` i din konsolfönstret.

2. Fönstret VS kod laddar IoT kant lösning arbetsytan. Det finns en `modules` mapp, en `.vscode` mapp och en distribution manifestfilen mallen i rotmappen. Du kan se felsöka konfigurationer i `.vscode` mapp. Alla användare modulen koder kommer att undermappar under `modules`. Den `deployment.template.json` är manifestet Distributionsmall. Vissa parametrar i den här filen kommer att tolkas från den `module.json`, som finns i varje modul-mapp.

3. Lägg till andra-modul i det här projektet i lösningen. Den här gången skriver och kör **kant: lägga till kant för IoT-modulen** och välj distributionen mallfilen att uppdatera. Välj en **Azure-funktion - C#** med namnet **SampleFunction** och dess Docker avbildningslagringsplatsen att lägga till.

4. Din första kant för IoT-lösning med två grundläggande moduler är nu klar. Standard C# modulen fungerar som en pipe meddelande medan C#-funktionen fungerar som pipe funktion. I den `deployment.template.json`, visas den här lösningen innehåller tre moduler. Meddelandet kommer att skapas från den `tempSensor` modulen, och ska skickas direkt `SampleModule` och `SampleFunction`, sedan skickas till din IoT-hubb. Uppdatera vägar för dessa moduler med nedan innehåll. 
   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
        },
   ```

5. Spara filen.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Skapa och distribuera din gräns för IoT-lösning
1. Skriv i VS kod kommandot paletten och kör kommandot **kant: skapa gräns för IoT-lösningen**. Baserat på de `module.json` i varje modul-mappen, detta kommando kontrollerar och börja bygga, containerize och push varje modul docker-bild. Sedan kommer att parsa värdet till `deployment.template.json`, generera den `deployment.json` med faktiska värde under `config` mapp. Du kan se förloppet build i VS koden integrerad terminal.

2. Högerklicka på en IoT-Edge enhets-ID i Azure IoT Hub-enheter explorer, och välj sedan **skapa distribution för gränsenheten**. Välj den `deployment.json` under `config` mapp. Du kan se distributionen har skapats med en distribution som ID i VS kod integrerad terminal.

3. Om du är [simulera en IoT insticksenhet](tutorial-simulate-device-linux.md) på utvecklingsdatorn. Du ser att alla behållare för modul-bilden kommer att startas om några minuter.

## <a name="view-generated-data"></a>Visa genererade data

1. Om du vill övervaka data som inkommer till IoT-hubben, Välj den **visa** > **kommandot paletten...**  och Sök efter **IoT: börja övervaka D2C meddelandet**. 
2. Om du vill stoppa övervakningen av data, Använd den **IoT: stoppa övervakningen D2C meddelandet** i paletten kommando. 

## <a name="next-steps"></a>Nästa steg

I kursen får skapat du en gräns för IoT-lösning med en C#-modulen, senare du lagt till en funktionsmodul, uppdatera vägar för lösningen, skapats och distribuerats till enheten simulerade IoT kant. Du kan fortsätta in på något av följande kurser vill veta mer om andra scenarier när du utvecklar Azure IoT gränsen i VS-kod.

> [!div class="nextstepaction"]
> [Felsöka en C#-modul i VS kod](how-to-vscode-debug-csharp-module.md)
> [felsöka en C#-funktion i VS-kod](how-to-vscode-debug-azure-function.md)