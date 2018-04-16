---
title: Distribuera Azure-funktion med Azure IoT Edge | Microsoft Docs
description: Distribuera Azure-funktion som en modul till en edge-enhet
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 04/02/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: f1c6b5cd07752c6b29234a365b3298d76b639b3a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Distribuera Azure-funktioner som en IoT Edge-modul – förhandsgranskning
Du kan använda Azure Functions för att distribuera kod som implementerar din affärslogik direkt till dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en Azure-funktion som filtrerar sensordata på simulerade IoT Edge-enheter som du skapade i självstudierna Distribuera Azure IoT Edge på en simulerad enhet i [Windows][lnk-tutorial1-win] eller [Linux][lnk-tutorial1-lin]. I den här guiden får du lära dig att:     

> [!div class="checklist"]
> * Använd Visual Studio Code för att skapa en Azure-funktion
> * Använd VS Code och Docker för att skapa en Docker-avbildning och publicera den till ditt register 
> * Distribuera modulen till din IoT Edge-enhet
> * Visa genererade data


Azure-funktionen som du skapar i den här självstudien filtrerar temperaturdata som genereras av din enhet och skickar enbart meddelanden uppströms till Azure IoT Hub när temperaturen är högre än det angivna tröskelvärdet. 

## <a name="prerequisites"></a>Nödvändiga komponenter

* Azure IoT Edge-enheten som du skapade i snabbstarten eller tidigare självstudier.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Azure IoT Edge-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Docker](https://docs.docker.com/engine/installation/). Community Edition (CE) för din plattform räcker för den här kursen. 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Skapa ett behållarregister
I den här självstudien använder du Azure IoT Edge-tillägget för VS Code för att skapa en modul och skapa en **behållaravbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.  

Du kan använda valfritt Docker-kompatibelt register för den här självstudien. Två populära Docker-registertjänster som finns tillgängliga i molnet är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). I den här kursen använder vi Azure Container Registry. 

1. I [Azure Portal](https://portal.azure.com) väljer du **Skapa en resurs** > **Behållare** > **Azure Container Registry**.
2. Namnge ditt register, välj en prenumeration, välj en resursgrupp och ange SKU:n till **Basic**. 
3. Välj **Skapa**.
4. När du har skapat ditt behållarregister, navigerar du till det och väljer **Åtkomstnycklar**. 
5. Växla till **Administratörsanvändare** för att **Aktivera**.
6. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du kommer att använda de här värdena senare i självstudien. 

## <a name="create-a-function-project"></a>Skapa ett funktionsprojekt
Följande steg visar hur du skapar en IoT Edge-funktion med Visual Studio Code och Azure IoT Edge-tillägget.
1. Öppna Visual Studio Code.
2. Välj **Visa** > **Integrerad terminal** för att öppna den VS Code-integrerade terminalen.
3. Installera (eller uppdatera) mallen för **AzureIoTEdgeFunction** i dotnet genom att köra följande kommando i den integrerade terminalen:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. Skapa ett projekt för den nya modulen. Följande kommando skapar projektmappen **FilterFunction** i den aktuella arbetsmappen. Den andra parametern bör ha formatet `<your container registry name>.azurecr.io` om du använder Azure-behållarregistret. Ange följande kommando i den aktuella arbetsmappen:

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction -r <your container registry address>/filterfunction
    ```

3. Välj **Fil** > **Öppna mapp**, bläddra sedan till **FilterFunction**-mappen och öppna projektet i VS Code.
4. I VS Code-utforskaren expanderar du **EdgeHubTrigger-Csharp**-mappen och öppnar sedan filen **run.csx**.
5. Ersätt innehållet i filen med följande kod:

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
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
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

11. Spara filen.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Skapa en Docker-avbildning och publicera den till ditt register

1. Logga in på Docker genom att ange följande kommando i den integrerade VS Code-terminalen: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Om du vill hitta användarnamn, lösenord och inloggningsserver som du ska använda i det här kommandot, gå till [Azure Portal] (https://portal.azure.com). Från **Alla resurser**, klickar du på panelen för ditt Azure-behållarregister för att öppna dess egenskaper och klickar sedan på **Åtkomstnycklar**. Kopiera värdena i fälten **Användarnamn**, **lösenord** och **Inloggningsserver**. 

2. Öppna **module.json**. Du kan också uppdatera `"version"` till exempelvis **1.0**. Dessutom visas namnet på databasen som du skrev in i `-r`-parametern för `dotnet new aziotedgefunction`.

3. Spara filen **module.json**.

4. I VS Code-utforskaren högerklickar du på filen **module.json** och klickar sedan på **Docker-avbildning för Build and Push IoT-modul**. I popup-listrutan överst i VS Code-fönstret väljer du din behållarplattform, antingen **amd64** för Linux-behållare eller **windows amd64** för Windows-behållare. VS Code lägger sedan dina funktionskoder i en behållare och push-överför den till det behållarregister som du har angett.

5. Den fullständiga adressen med tagg för behållaravbildningen finns i den integrerade VS Code-terminalen. Mer information om versions- och push-definitionen finns i filen `module.json`.

## <a name="add-registry-credentials-to-your-edge-device"></a>Lägg till autentiseringsuppgifter för registret i din Edge-enhet
Lägg till autentiseringsuppgifterna för ditt register i Edge-runtimen på den dator där du kör din Edge-enhet. Det ger runtimen åtkomst att hämta behållaren. 

- I Windows kör du följande kommando:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- I Linux kör du följande kommando:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Kör lösningen

1. I **Azure Portal**, navigerar du till din IoT-hubb.
2. Gå till **IoT Edge (förhandsversion)** och välj IoT Edge-enhet.
1. Välj **Ange moduler**. 
2. Om du redan har distribuerat **tempSensor**-modulen till den här enheten, kan den vara automatiskt ifylld. Annars följer du de här stegen för att lägga till den:
    1. Välj **Lägg till IoT Edge-modul**.
    2. I **Namn**-fältet skriver du `tempSensor`.
    3. I **URI för avbildning**-fältet skriver du `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Lämna de andra inställningarna oförändrade och klicka på **Spara**.
1. Lägg till **filterFunction**-modulen.
    1. Välj **Lägg till IoT Edge-modul** igen.
    2. I **Namn**-fältet skriver du `filterFunction`.
    3. I **URI för avbildning**-fältet, anger du adressen till din avbildning, till exempel `<your container registry address>/filterfunction:0.0.1-amd64`. Avbildningens fullständiga adress kan hittas i föregående avsnitt.
    74. Klicka på **Spara**.
2. Klicka på **Nästa**.
3. I steget **Ange vägar** kopierar du den JSON nedanför till textrutan. Den första vägen transporterar meddelanden från temperatursensorn till filtermodulen via slutpunkten input1. Den andra vägen transporterar meddelanden från filtermodulen till IoT Hub. I den här vägen är `$upstream` ett särskilt mål som talar om för Edge-hubben att skicka meddelanden till IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterFunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterFunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Klicka på **Nästa**.
5. I steget **Granska mallen**, klickar du på **Skicka**. 
6. Återgå till IoT Edge-enhetens informationssida och välj **Uppdatera**. Du borde se att den nya **filterfunktion**-modulen körs tillsammans med **tempSensor**-modulen och **IoT Edge-runtimen**. 

## <a name="view-generated-data"></a>Visa genererade data

Övervaka meddelanden från enhet till molnet som skickas från din IoT Edge-enhet till din IoT-hubb:
1. Konfigurera Azure IoT Toolkit-tillägget med anslutningssträngen för din IoT-hubb: 
    1. Navigera till din IoT-hubb i Azure Portal och välj **principer för delad åtkomst**. 
    2. Välj **iothubowner** och kopiera värdet för **Anslutningssträng-primär nyckel**.
    3. I VS Code-utforskaren, klickar du på **IOT HUB-ENHETER** och klickar sedan på **...** . 
    4. Välj **Ange anslutningssträngen för IoT-hubb** och ange anslutningssträngen för IoT Hub i popup-fönstret. 

2. Om du vill övervaka data som kommer till IoT-hubben, väljer du **Visa** > **Kommandopaletten...** och söker efter **IoT: börja övervaka D2C-meddelande**. 
3. Om du vill stoppa dataövervakningen, använder du kommandot **IoT: stoppa övervakning av D2C-meddelande** i kommandopaletten. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien, skapade du en Azure-funktion som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet. Om du vill fortsätta utforska Azure IoT Edge, lära dig hur man använder en IoT Edge-enhet som en gateway. 

> [!div class="nextstepaction"]
> [Skapa en IoT Edge-gatewayenhet](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
