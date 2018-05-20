---
title: Hantera flera Azure IoT kant-moduler i VS kod | Microsoft Docs
description: Använd Visual Studio-koden för att utveckla kant för IoT-lösningar som använder flera moduler.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4d9caa7aa95c99fa30e8ec76c5b6362615725622
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Utveckla en kant för IoT-lösning med flera moduler i Visual Studio Code - förhandsgranskning
Du kan använda Visual Studio-koden för att utveckla kant för IoT-lösning med flera moduler. Den här artikeln vägleder genom att skapa, uppdatera och distribuera en gräns för IoT-lösning som rör sensordata på simulerade IoT gränsenheten i Visual Studio-koden. 

## <a name="prerequisites"></a>Förutsättningar

Har följande krav för att slutföra alla steg i den här artikeln:

- [Visual Studio Code](https://code.visualstudio.com/) 
- [Azure IoT kant-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [C# för Visual Studio Code (drivs av OmniSharp)-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 
- AzureIoTEdgeModule mall (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- En aktiv IoT-hubb med minst en IoT-enhet


* [Docker för VS kod](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) med explorer-integrering för att hantera avbildningar och behållare.


## <a name="prepare-your-first-iot-edge-solution"></a>Förbered din första kant för IoT-lösning
1. Skriv i VS kod kommandot paletten och kör kommandot **kant: kant för ny IoT-lösningen**. Välj arbetsytemappen, ange lösningens namn (standardnamnet är **EdgeSolution**), och skapa en C#-modul (**SampleModule**) som den första användarmodulen i den här lösningen. Du måste också ange Docker avbildningslagringsplatsen för din första modulen. Standard avbildningslagringsplatsen baseras på en lokal Docker-registret (`localhost:5000/<first module name>`). Du kan också ändra den till Azure-behållaren registret eller Docker-hubb.

   > [!NOTE]
   > Om du använder en lokal Docker-registret, kontrollerar du registret genom att skriva kommandot `docker run -d -p 5000:5000 --restart=always --name registry registry:2` i din konsolfönstret.

2. Fönstret VS kod läser in gräns för IoT-lösning arbetsytan. Rotmappen innehåller en `modules` mapp, en `.vscode` mapp och en manifestet mallfil för distribution. Du kan se felsöka konfigurationer i `.vscode` mapp. Alla användare modulen koder kommer att undermappar under `modules`. Den `deployment.template.json` är manifestet Distributionsmall. Vissa parametrar i den här filen kommer att tolkas från den `module.json`, som finns i varje modul-mapp.

3. Lägg till andra-modul i det här projektet i lösningen. Den här gången skriver och kör **kant: lägga till kant för IoT-modulen** och välj distributionen mallfilen att uppdatera. Välj en **Azure-funktion - C#** med namnet **SampleFunction** och dess Docker avbildningslagringsplatsen.

4. Öppna den `deployment.template.json` filen och kontrollera att det deklarerar tre moduler förutom körningsmiljön. Meddelandet kommer att skapas från den `tempSensor` modulen, och ska skickas direkt `SampleModule` och `SampleFunction`, sedan skickas till din IoT-hubb. 
5. Uppdatera vägar för dessa moduler med följande innehåll:
   ```json
   "routes": {
      "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
      "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
      "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
   },
   ```

6. Spara filen.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Skapa och distribuera din gräns för IoT-lösning
1. Skriv i VS kod kommandot paletten och kör kommandot **kant: skapa gräns för IoT-lösningen**. Baserat på de `module.json` i det här kommandot start att bygga, containerize och push varje modul docker avbildning varje modul-mappen. Därefter passerar det obligatoriska värdet till `deployment.template.json` och genererar den `deployment.json` filen med information från den `config` mapp. Du kan se förloppet build i VS koden integrerad terminal.

2. Högerklicka på en IoT-Edge enhets-ID i Azure IoT Hub-enheter explorer, och välj sedan **skapa distribution för gränsenheten**. Välj den `deployment.json` under `config` mapp. Du kan se distributionen har skapats med en distribution som ID i VS kod integrerad terminal.

3. Om simulerar en IoT-enhet på utvecklingsdatorn, visas alla behållare för modul-avbildningen startas om några minuter.

## <a name="view-generated-data"></a>Visa genererade data

1. Om du vill övervaka data som kommer till IoT-hubben, väljer du **Visa** > **Kommandopaletten...** och söker efter **IoT: börja övervaka D2C-meddelande**. 
2. Om du vill stoppa dataövervakningen, använder du kommandot **IoT: stoppa övervakning av D2C-meddelande** i kommandopaletten. 

## <a name="next-steps"></a>Nästa steg

Mer information om andra scenarier för att utveckla Azure IoT gränsen i Visual Studio-koden:

* [Felsöka en C#-modul i VS-kod](how-to-vscode-debug-csharp-module.md)
* [Felsöka en C#-funktion i VS-kod](how-to-vscode-debug-azure-function.md)