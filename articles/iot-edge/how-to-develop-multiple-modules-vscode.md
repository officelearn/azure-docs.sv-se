---
title: Arbeta med flera Azure IoT Edge-moduler i VS Code | Microsoft Docs
description: Använd IoT-tillägget för Visual Studio Code för att utveckla flera moduler på en gång för Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 31fe210b87a052438956d813db0d104e0f2cdb6e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041267"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code"></a>Utveckla en IoT Edge-lösning med flera moduler i Visual Studio Code

Du kan skapa en Azure IoT Edge-lösning med flera moduler i Visual Studio Code. Den här artikeln beskriver hur du skapar, uppdaterar och distribuerar en IoT Edge-lösning som skickar sensordata på en simulerad IoT Edge-enhet i VS Code. 

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa anvisningarna i den här artikeln behöver du följande:

- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure IoT Edge-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [C# för Visual Studio Code-tillägget (tillhandahålls av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- Den [.NET Core SDK 2.1](https://www.microsoft.com/net/download)
- En aktiv IoT-hubb med minst en IoT Edge-enhet

Du behöver också [Docker för VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) med Azure IoT Hub Device Explorer-integrering för att hantera avbildningar och containrar.

## <a name="create-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

1. I Visual Studio code, öppna den integrerade terminalen genom att välja **visa** > **integrerade terminalen**. 

1. I VS Code **Kommandopaletten**anger du och kör kommandot **Azure IoT Edge: nya IoT-Edge lösning**. Välj mappen för din arbetsyta och ange namnet på lösningen (standardnamnet är EdgeSolution). Skapa en C#-modul (med namnet **PipeModule**) som den första användarmodulen i den här lösningen. Standardmall för C#-modul är en pipe-modul som rör direkt meddelanden från överordnad till nedströms. Du måste också ange lagringsplatsen för Docker-avbildningar för din första modul. Standardlagringsplatsen för avbildningar baseras på ett lokalt Docker-register (**localhost:5000/<first module name>**). Du kan ändra platsen till Azure Container Registry eller Docker Hub. 

2. VS Code läser in arbetsytan för IoT Edge-lösningen. Rotmappen innehåller en **modules**-mapp, en **.vscode**-mapp och en mallfil för distributionsmanifestet. Felsökningskonfigurationer finns i mappen .vscode. Alla koder för användarmoduler är undermappar i mappen modules. Filen deployment.template.json är mallfilen för distributionsmanifestet. Vissa parametrar i den här filen parsas från filen module.json, som finns i alla modulmappar.

3. Lägg till en andra modul i lösningsprojektet. Det finns flera sätt att lägga till en ny modul denna lösning. Ange och kör kommandot **Azure IoT Edge: Lägg till IoT Edge-modul**. Välj distributionsmallfilen som ska uppdateras. Eller högerklicka på modulmappen eller högerklicka på filen deployment.template.json och välj **Lägg till IoT Edge-modul**. Kommer att det finnas en listruta för att välja modultypen. Välj en **Azure Functions – C#** modulen med namnet **PipeFunction** och dess Docker avbildningslagringsplatsen. Standardmall för C# functions-modul är en pipe-modul som rör direkt meddelanden från överordnad till nedströms.

4. Öppna filen deployment.template.json. Kontrollera att filen deklarerar tre moduler och körningsmiljön. Meddelandet genereras från modulen tempSensor. Meddelandet skickas direkt via SampleModule och SampleFunction-moduler och skickas sedan till din IoT-hubb. 

5. Uppdatera vägarna för dessa moduler med följande innehåll:

   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/PipeModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/PipeModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/PipeFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/PipeFunction/outputs/output1 INTO $upstream"
        },
   ```

5. Spara filen.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Skapa och distribuera IoT Edge-lösningen

1. I VS Code **Kommandopaletten**anger du och kör kommandot **Azure IoT Edge: skapa IoT Edge-lösningen**. Kommandot börjar skapa, innesluta i containrar och skicka Docker-avbildningarna för modulerna baserat på filen module.json i respektive modulmapp. Kommandot skickar sedan nödvändigt värde till filen deployment.template.json och genererar filen deployment.json med information från config-mappen. Du kan följa utvecklingsförloppet i den integrerade terminalen i VS Code. 

2. Högerklicka på ID:t för en IoT Edge-enhet i Azure IoT Hub **Device Explorer** och välj kommandot **Create deployment for Edge device** (Skapa distribution för Edge-enhet). Välj filen deployment.json i config-mappen. Den integrerade terminalen i VS Code anger att distributionen har skapats och visar distributions-ID:t.

3. Om du simulera en IoT Edge-enhet på utvecklingsdatorn, kan du se att se att alla modulen bild behållare börjar inom några minuter.

## <a name="view-the-generated-data"></a>Visa genererade data

1. Om du vill övervaka data som IoT-hubben tar emot väljer du **Visa** > **Kommandopalett**. Välj sedan kommandot **IoT: Start monitoring D2C message** (IoT: Börja övervaka D2C-meddelande). 
2. Om du vill stoppa dataövervakningen använder du kommandot **IoT: Stop monitoring D2C message** (IoT: Sluta övervaka D2C-meddelande) på **kommandopaletten**. 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra scenarier för utveckling med Azure IoT Edge i Visual Studio Code:

* Utveckla moduler i VS Code med [C#](how-to-develop-csharp-module.md) eller [Node.js](how-to-develop-node-module.md).
* Utveckla Azure Functions i VS Code med [C#](how-to-develop-csharp-function.md).

Att utveckla moduler för dina IoT Edge-enheter, [förstå och använda Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).