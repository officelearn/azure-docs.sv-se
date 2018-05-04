---
title: Använda Visual Studio-koden för att utveckla och distribuera Azure Functions Azure IoT kant | Microsoft Docs
description: Utveckla och distribuera en C# Azure Functions med Azure IoT gränsen i VS kod utan att växla kontext
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 47d420b4b283b390f67719233c4bea59495a589a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2018
---
# <a name="use-visual-studio-code-to-develop-and-deploy-azure-functions-to-azure-iot-edge"></a>Använda Visual Studio-koden för att utveckla och distribuera Azure Functions Azure IoT kant

Den här artikeln innehåller detaljerade anvisningar för att använda [Visual Studio Code](https://code.visualstudio.com/) som den huvudsakliga utvecklingsverktyg för att utveckla och distribuera Azure Functions på IoT kant. 

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du använder en dator eller virtuell dator som kör Windows eller Linux som utvecklingsdatorn. Din IoT-Edge-enhet kan vara en annan fysisk enhet eller kan du simulera enheten IoT kanten på utvecklingsdatorn.

Kontrollera att du har slutfört följande kurser innan du börjar den här vägledningen.
- Distribuera Azure IoT kanten på en simulerad enhet i [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) eller [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Distribuera Azure Functions](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function)

Här är en checklista som visar vilka objekt som du bör ha när du har slutfört föregående självstudier.

- [Visual Studio Code](https://code.visualstudio.com/). 
- [Azure IoT Edge-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
- [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 
- [Python 2.7](https://www.python.org/downloads/)
- [IoT-Edge kontrollen skript](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- AzureIoTEdgeFunction mall (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
- En aktiv IoT-hubb med minst en IoT-enhet.

Vi rekommenderar också för att installera [Docker-stöd för VS kod](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) bättre hantera modulen avbildningar och behållare.

> [!NOTE]
> För närvarande stöder Azure Functions i IoT kant endast C#.

## <a name="deploy-azure-iot-functions-in-vs-code"></a>Distribuera Azure IoT-funktioner i VS-kod
I kursen [distribuera Azure Functions](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function), du uppdatera, skapa, publicera bilderna funktionen modul i VS-kod och besöker Azure-portalen för att distribuera Azure Functions. Det här avsnittet beskriver hur du använder VS-kod för att distribuera och övervaka dina Azure-funktioner.

### <a name="start-a-local-docker-registry"></a>Starta en lokal docker-registret
Du kan använda alla Docker-kompatibel registret för den här artikeln. Två populära Docker-registertjänster som finns tillgängliga i molnet är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Det här avsnittet använder en [lokala Docker-registret](https://docs.docker.com/registry/deploying/), som är enklare för att testa syfte under din tidig utvecklingen.
I VS kod **integrerad terminal**(Ctrl + ”), kör följande kommandon för att starta en lokala registret.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> Ovanstående exempel visar registret konfigurationer som gäller endast för testning. En produktionsklara registret måste skyddas med TLS och helst ska använda en åtkomstkontroll metod. Vi rekommenderar att du använder [Azure Container registret](https://docs.microsoft.com/azure/container-registry/) eller [Docker-hubb](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) att distribuera produktionsklara IoT kant moduler.

### <a name="create-a-function-project"></a>Skapa ett funktionsprojekt
Följande steg visar du hur du skapar en IoT-Edge-modul som baseras på .NET core 2.0 med hjälp av Visual Studio Code och Azure IoT kant-tillägget. Om du har slutfört det här avsnittet i en tidigare självstudierna kan du på ett säkert sätt hoppa över det här avsnittet.

1. I Visual Studio Code väljer **visa** > **integrerad Terminal** att öppna VS koden integrerad terminal.
2. Installera (eller uppdatera) mallen för **AzureIoTEdgeFunction** i dotnet genom att köra följande kommando i den integrerade terminalen:

   ```cmd/sh
   dotnet new -i Microsoft.Azure.IoT.Edge.Function
   ```
3. Skapa ett projekt för den nya modulen. Följande kommando skapar projektmappen **FilterFunction**, i den aktuella arbetsmappen:

   ```cmd/sh
   dotnet new aziotedgefunction -n FilterFunction
   ```
 
4. Välj **fil > Öppna mappen**, bläddra sedan till den **FilterFunction** mapp och öppna projektet i VS-kod.
5. Bläddra till den **FilterFunction** mappen och klicka på **Välj mapp** öppna projektet i VS-kod.
6. I VS Code-utforskaren expanderar du **EdgeHubTrigger-Csharp**-mappen och öppnar sedan filen **run.csx**.
7. Ersätt innehållet i filen med följande kod:

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
   ```

8. Spara filen.

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Skapa en Docker-avbildning och publicera den till ditt register

1. I VS Code-utforskaren expanderar du **Docker**-mappen. Expandera sedan mappen för din behållarplattform, antingen **linux-x64** eller **windows-nano**.
2. Högerklicka på **Dockerfile**-filen och klicka **Skapa IoT Edge-modul Docker-avbildning**. 

    ![Skapa docker-bild](./media/how-to-vscode-develop-csharp-function/build-docker-image.png)

3. Navigera till projektmappen **FilterFunction** och klicka på **Välj mapp som EXE_DIR**. 
4. I popup-textrutan överst på VS Code-fönstret anger du avbildningens namn. Till exempel: `<your container registry address>/filterfunction:latest`. Om du distribuerar till lokala registret ska `localhost:5000/filterfunction:latest`.
5. Pusha bilden till din Docker-lagringsplats. Använd den **kant: Push-gräns för IoT-modulen Docker bild** kommando och ange bildens URL i popup-textrutan längst upp i fönstret VS-kod. Använd samma bild-URL som du använde i senare steg.
    ![Push-docker-bild](./media/how-to-vscode-develop-csharp-function/push-image.png)

### <a name="deploy-your-function-to-iot-edge"></a>Distribuera din funktion IoT kant

1. Öppna den `deployment.json` fil ersätter **moduler** avsnitt med nedanför innehållet:
   ```json
   "tempSensor": {
      "version": "1.0",
      "type": "docker",
      "status": "running",
      "restartPolicy": "always",
      "settings": {
         "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
         "createOptions": ""
      }
   },
   "filterfunction": {
      "version": "1.0",
      "type": "docker",
      "status": "running",
      "restartPolicy": "always",
      "settings": {
         "image": "localhost:5000/filterfunction:latest",
         "createOptions": ""
      }
   }
   ```

2. Ersätt den **vägar** avsnitt med nedanför innehållet:
   ```json
       "routes":{
           "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
           "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
   ```
   > [!NOTE]
   > Deklarativ regler i körningsmiljön definiera där dessa meddelanden. Du behöver två vägar i den här artikeln. Första vägen transporter meddelanden från temperatursensor till filterfunktionen via slutpunkten ”input1”, vilket är den slutpunkt du konfigurerade med FilterMessages-hanteraren. Andra vägen transporter meddelanden från filterfunktionen till IoT-hubb. I den här vägen är överordnad en särskild destination som talar om kant-hubb för att skicka meddelanden till IoT-hubb.

3. Spara filen.
4. I kommandot paletten väljer **kant: skapa distribution för gränsenheten**. Välj IoT kant enhets-ID att skapa en distribution. Eller högerklicka på enhets-ID i listan över enheter och välj **skapa distribution för gränsenheten**.

    ![Skapa distribution](./media/how-to-vscode-develop-csharp-function/create-deployment.png)

5. Välj den `deployment.json` du uppdateras. I utdatafönstret visas motsvarande utdata för din distribution.
6. Starta Edge-körning i kommandot palett. **Kant: Start kant**
7. Du kan se din IoT-Edge runtime börja köra i Docker explorer med funktionen simulerade sensor och filter.

    ![Lösning som körs](./media/how-to-vscode-develop-csharp-function/solution-running.png)

8. Högerklicka på ditt Edge enhets-ID och du kan övervaka D2C meddelanden i VS-kod.

    ![Övervakare-meddelanden](./media/how-to-vscode-develop-csharp-function/monitor-d2c-messages.png)


## <a name="next-steps"></a>Nästa steg

[Felsöka Azure Functions i VS-kod](how-to-vscode-debug-azure-function.md)
