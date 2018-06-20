---
title: Hantera flera Azure IoT Edge-moduler i VS Code | Microsoft Docs
description: Skapa Azure IoT Edge-lösningar med flera moduler i Visual Studio Code.
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4a624994f93e7a3cbb04db2a0ec0b2b823a12ee7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763042"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code-preview"></a>Skapa en IoT Edge-lösning med flera moduler i Visual Studio Code (förhandsversion)

Du kan skapa en Azure IoT Edge-lösning med flera moduler i Visual Studio Code. Den här artikeln beskriver hur du skapar, uppdaterar och distribuerar en IoT Edge-lösning som skickar sensordata på en simulerad IoT Edge-enhet i VS Code. 

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa anvisningarna i den här artikeln behöver du följande:

- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure IoT Edge-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [C# för Visual Studio Code-tillägget (tillhandahålls av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)
- AzureIoTEdgeModule-mallen (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- En aktiv IoT-hubb med minst en IoT Edge-enhet

Du behöver också [Docker för VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) med Azure IoT Hub Device Explorer-integrering för att hantera avbildningar och behållare.

## <a name="prepare-your-first-iot-edge-solution"></a>Förbereda IoT Edge-lösningen

1. Skriv och kör kommandot **Edge: New IoT Edge solution** på **kommandopaletten** i VS Code. Välj mappen för din arbetsyta och ange namnet på lösningen (standardnamnet är EdgeSolution). Skapa en C#-modul (med namnet **SampleModule**) som den första användarmodulen i lösningen. Du måste också ange lagringsplatsen för Docker-avbildningar för din första modul. Standardlagringsplatsen för avbildningar baseras på ett lokalt Docker-register (**localhost:5000/<first module name>**). Du kan ändra platsen till Azure Container Registry eller Docker Hub.

   > [!NOTE]
   > Om du använder ett lokalt Docker-register kontrollerar du att registret körs. Ange följande kommando i konsolfönstret:
   > 
   > `docker run -d -p 5000:5000 --restart=always --name registry registry:2`

2. VS Code läser in arbetsytan för IoT Edge-lösningen. Rotmappen innehåller en **modules**-mapp, en **.vscode**-mapp och en mallfil för distributionsmanifestet. Felsökningskonfigurationer finns i mappen .vscode. Alla koder för användarmoduler är undermappar i mappen modules. Filen deployment.template.json är mallfilen för distributionsmanifestet. Vissa parametrar i den här filen parsas från filen module.json, som finns i alla modulmappar.

3. Lägg till en andra modul i lösningsprojektet. Ange och kör kommandot **Edge: Lägg till IoT Edge-modul**. Välj distributionsmallfilen som ska uppdateras. Välj **Azure Function – C#**-modulen med namnet **SampleFunction** och modulens lagringsplats för Docker-avbildningar.

4. Öppna filen deployment.template.json. Kontrollera att filen deklarerar tre moduler och körningsmiljön. Meddelandet genereras från modulen tempSensor. Meddelandet skickas direkt via SampleModule och SampleFunction-moduler och skickas sedan till din IoT-hubb. 

5. Uppdatera vägarna för dessa moduler med följande innehåll:

   ```json
   "routes": {
      "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
      "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
      "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
   },
   ```

6. Spara filen.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Skapa och distribuera IoT Edge-lösningen

1. Ange och kör kommandot **Edge: Build IoT Edge solution** (Edge: Skapa IoT Edge-lösning) på **kommandopaletten** i VS Code. Kommandot börjar skapa, innesluta i behållare och skicka Docker-avbildningarna för modulerna baserat på filen module.json i respektive modulmapp. Kommandot skickar sedan nödvändigt värde till filen deployment.template.json och genererar filen deployment.json med information från config-mappen. Du kan följa utvecklingsförloppet i den integrerade terminalen i VS Code.

2. Högerklicka på ID:t för en IoT Edge-enhet i Azure IoT Hub **Device Explorer** och välj kommandot **Create deployment for Edge device** (Skapa distribution för Edge-enhet). Välj filen deployment.json i config-mappen. Den integrerade terminalen i VS Code anger att distributionen har skapats och visar distributions-ID:t.

3. Om du simulerar en IoT Edge-enhet på utvecklingsdatorn startar alla behållare för modulavbildningarna inom några minuter.

## <a name="view-the-generated-data"></a>Visa genererade data

1. Om du vill övervaka data som IoT-hubben tar emot väljer du **Visa** > **Kommandopalett**. Välj sedan kommandot **IoT: Start monitoring D2C message** (IoT: Börja övervaka D2C-meddelande). 
2. Om du vill stoppa dataövervakningen använder du kommandot **IoT: Stop monitoring D2C message** (IoT: Sluta övervaka D2C-meddelande) på **kommandopaletten**. 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra scenarier för utveckling med Azure IoT Edge i Visual Studio Code:

* [Felsöka en C#-modul i VS Code](how-to-vscode-debug-csharp-module.md)
* [Felsöka en C#-funktion i VS Code](how-to-vscode-debug-azure-function.md)
