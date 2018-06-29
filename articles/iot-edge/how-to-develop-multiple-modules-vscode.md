---
title: Arbeta med flera Azure IoT kant-moduler i VS kod | Microsoft Docs
description: Använd IoT-tillägget för Visual Studio-koden för att utveckla flera moduler på en gång för Azure IoT kant
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 4e9aac5f19fa75613dee2aba3853a0243d7d966b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048268"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code"></a>Utveckla en kant för IoT-lösning med flera moduler i Visual Studio Code

Du kan skapa en Azure IoT Edge-lösning med flera moduler i Visual Studio Code. Den här artikeln beskriver hur du skapar, uppdaterar och distribuerar en IoT Edge-lösning som skickar sensordata på en simulerad IoT Edge-enhet i VS Code. 

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa anvisningarna i den här artikeln behöver du följande:

- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure IoT Edge-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [C# för Visual Studio Code-tillägget (tillhandahålls av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- Den [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)
- En aktiv IoT-hubb med minst en IoT Edge-enhet

Du behöver också [Docker för VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) med Azure IoT Hub Device Explorer-integrering för att hantera avbildningar och behållare.

## <a name="create-your-iot-edge-solution"></a>Skapa en IoT kant

1. Öppna integrerad terminalen i Visual Studio-koden genom att välja **visa** > **integrerad terminal**. 

1. I koden VS **kommandot paletten**, ange och kör kommandot **Azure IoT kant: kant för ny IoT-lösningen**. Välj mappen för din arbetsyta och ange namnet på lösningen (standardnamnet är EdgeSolution). Skapa en C#-modul (med namnet **PipeModule**) som den första användarmodulen i den här lösningen. Standardmallen för C#-modulen är en pipe-modul, som kommer direkt meddelanden från överordnad till nedströms. Du måste också ange lagringsplatsen för Docker-avbildningar för din första modul. Standardlagringsplatsen för avbildningar baseras på ett lokalt Docker-register (**localhost:5000/<first module name>**). Du kan ändra platsen till Azure Container Registry eller Docker Hub. 

2. VS Code läser in arbetsytan för IoT Edge-lösningen. Rotmappen innehåller en **modules**-mapp, en **.vscode**-mapp och en mallfil för distributionsmanifestet. Felsökningskonfigurationer finns i mappen .vscode. Alla koder för användarmoduler är undermappar i mappen modules. Filen deployment.template.json är mallfilen för distributionsmanifestet. Vissa parametrar i den här filen parsas från filen module.json, som finns i alla modulmappar.

3. Lägg till en andra modul i lösningsprojektet. Det finns flera sätt att lägga till en ny modul denna lösning. Ange och kör kommandot **Azure IoT kant: lägga till kant för IoT-modulen**. Välj distributionsmallfilen som ska uppdateras. Eller högerklicka på modulmappen eller högerklicka på filen deployment.template.json och välj **Lägg till IoT kant modul**. Kommer att det finnas en listruta för att välja modultypen. Välj en **Azure Functions - C#** modulen med namnet **PipeFunction** och dess Docker avbildningslagringsplatsen. Standardmallen för C# funktioner modul är en pipe-modul, som kommer direkt meddelanden från överordnad till nedströms.

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

1. I koden VS **kommandot paletten**, ange och kör kommandot **Azure IoT kant: skapa gräns för IoT-lösningen**. Kommandot börjar skapa, innesluta i behållare och skicka Docker-avbildningarna för modulerna baserat på filen module.json i respektive modulmapp. Kommandot skickar sedan nödvändigt värde till filen deployment.template.json och genererar filen deployment.json med information från config-mappen. Du kan följa utvecklingsförloppet i den integrerade terminalen i VS Code. 

2. Högerklicka på ID:t för en IoT Edge-enhet i Azure IoT Hub **Device Explorer** och välj kommandot **Create deployment for Edge device** (Skapa distribution för Edge-enhet). Välj filen deployment.json i config-mappen. Den integrerade terminalen i VS Code anger att distributionen har skapats och visar distributions-ID:t.

3. Om du simulera en IoT-enhet på utvecklingsdatorn kan du titta på att se att alla modulen avbildningen behållare startar inom några minuter.

## <a name="view-the-generated-data"></a>Visa genererade data

1. Om du vill övervaka data som IoT-hubben tar emot väljer du **Visa** > **Kommandopalett**. Välj sedan kommandot **IoT: Start monitoring D2C message** (IoT: Börja övervaka D2C-meddelande). 
2. Om du vill stoppa dataövervakningen använder du kommandot **IoT: Stop monitoring D2C message** (IoT: Sluta övervaka D2C-meddelande) på **kommandopaletten**. 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra scenarier för utveckling med Azure IoT Edge i Visual Studio Code:

* [Utveckla en C#-modul i VS-kod](how-to-develop-csharp-module.md)
* [Utveckla en C#-funktion i VS-kod](how-to-develop-csharp-function.md)
