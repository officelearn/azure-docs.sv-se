---
title: Azure IoT Edge C#-modul | Microsoft Docs
description: Skapa en IoT Edge-modul med C#-kod och distribuera den till en gränsenhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1da3a246a2ad33a4563f491058f5d4d115f3954d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631079"
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Utveckla och distribuera en C# IoT Edge-modul till den simulerade enheten – förhandsversion

Du kan använda IoT Edge-moduler till att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. Du kommer att använda den simulerade IoT Edge-enheten som du skapade i självstudien Distribuera Azure IoT Edge på en simulerad enhet i [Windows][lnk-tutorial1-win] eller [Linux][lnk-tutorial1-lin]. I den här guiden får du lära dig hur man:    

> [!div class="checklist"]
> * Använda Visual Studio Code för att skapa en IoT Edge-modul som baseras på .NET Core 2.0
> * Använda Visual Studio Code och Docker för att skapa en Docker-avbildning och publicera den till ditt register 
> * Distribuera modulen till din IoT Edge-enhet
> * Visa genererade data


IoT Edge-modulen som du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys är användbar för att minska mängden data som kommuniceras till och lagras i molnet. 

## <a name="prerequisites"></a>Nödvändiga komponenter

* Azure IoT Edge-enheten som du skapade i snabbstarten eller den första självstudien.
* Primärnyckelns anslutningssträng för IoT Edge-enheten.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/) på samma dator som har Visual Studio Code. Community Edition (CE) räcker för den här självstudien. 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Skapa ett behållarregister
I den här självstudien använder du Azure IoT Edge-tillägget för VS Code för att skapa en modul och skapa en **behållaravbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.  

Du kan använda valfritt Docker-kompatibelt register för den här självstudien. Två populära Docker-registertjänster som finns tillgängliga i molnet är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). I den här kursen använder vi Azure Container Registry. 

1. I [Azure Portal](https://portal.azure.com) väljer du **Skapa en resurs** > **Behållare** > **Azure Container Registry**.
2. Namnge ditt register, välj en prenumeration, välj en resursgrupp och ange SKU:n till **Basic**. 
3. Välj **Skapa**.
4. När du har skapat ditt behållarregister, navigerar du till det och väljer **Åtkomstnycklar**. 
5. Växla till **Administratörsanvändare** för att **Aktivera**.
6. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du använder dessa värden senare i självstudien när du publicerar Docker-avbildningen till registret, samt när du lägger till autentiseringsuppgifterna för registret i Edge-körningen. 

## <a name="create-an-iot-edge-module-project"></a>Skapa ett projekt för IoT Edge-modulen
Följande steg visar hur du skapar en IoT Edge-modul som baseras på .NET Core 2.0 med Visual Studio Code och Azure IoT Edge-tillägget.
1. I Visual Studio Code väljer du **Visa** > **Integrerad terminal** för att öppna den integrerade Visual Studio Code-terminalen.
2. Installera (eller uppdatera) mallen **AzureIoTEdgeModule** i dotnet genom att köra följande kommando i den integrerade terminalen:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

3. Skapa ett projekt för den nya modulen. Följande kommando skapar projektmappen **FilterModule** på behållarens lagringsplats. Den andra parametern bör ha formatet `<your container registry name>.azurecr.io` om du använder Azure-behållarregistret. Ange följande kommando i den aktuella arbetsmappen:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule -r <your container registry address>/filtermodule
    ```
 
4. Välj **Arkiv** > **Öppna mapp**.
5. Bläddra till mappen **FilterModule** och klicka på **Välj mapp** för att öppna projektet i VS Code.
6. Klicka på **Program.cs** i VS Code-utforskaren för att öppna den.

   ![Öppna Program.cs][1]

7. Längst upp i namnområdet **FilterModule** lägger du till tre `using`-instruktioner för typer som vi använder vid ett senare tillfälle:

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
    ```

8. Lägg till variabeln `temperatureThreshold` i klassen **Program**. Variabeln anger det värde som den uppmätta temperaturen måste överstiga för att data ska skickas till IoT Hub. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

9. Lägg till klasserna `MessageBody`, `Machine` och `Ambient` i klassen **Program**. Dessa klasser definierar det förväntade schemat för brödtexten i inkommande meddelanden.

    ```csharp
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

10. I metoden **Init** skapar och konfigurerar koden ett **DeviceClient**-objekt. Med det här objektet kan modulen ansluta till den lokala Azure IoT Edge-körningen för att skicka och ta emot meddelanden. Anslutningssträngen som används i **Init**-metoden skickas till modulen av IoT Edge-körningen. När du har skapat **DeviceClient** läser koden TemperatureThreshold från modultvillingens önskade egenskaper och registrerar en motringning för att ta emot meddelanden från IoT Edge-hubben via slutpunkten **input1**. Ersätt metoden `SetInputMessageHandlerAsync` med en ny och lägg till metoden `SetDesiredPropertyUpdateCallbackAsync` för önskade egenskapsuppdateringar. Gör den här ändringen genom att ersätta den sista raden i **Init**-metoden med följande kod:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read TemperatureThreshold from Module Twin Desired Properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    try {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    } catch(ArgumentOutOfRangeException e) {
        Console.WriteLine("Property TemperatureThreshold not exist");
    }

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

11. Lägg till metoden `onDesiredPropertiesUpdate` i klassen **Program**. Metoden tar emot uppdateringar av önskade egenskaper från modultvillingen och uppdaterar variabeln **temperatureThreshold** som ska matchas. Alla moduler har en egen modultvilling, vilket innebär att du kan konfigurera den kod som körs i en modul direkt från molnet.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

12. Ersätt metoden `PipeMessage` med metoden `FilterMessages`. Den här metoden anropas när modulen tar emot ett meddelande från IoT Edge-hubben. Den filtrerar ut meddelanden som rapporterar temperaturer under temperaturtröskelvärdet som angetts via modultvillingen. Den lägger också till egenskapen **MessageType** i meddelandet med värdet angivet som **Avisering**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get message body
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await deviceClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed
            var deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

13. Spara filen.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Skapa en Docker-avbildning och publicera den till ditt register

1. Logga in på Docker genom att ange följande kommando i den integrerade VS Code-terminalen: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Om du vill hitta användarnamn, lösenord och inloggningsserver som du ska använda i det här kommandot, gå till [Azure Portal] (https://portal.azure.com). Från **Alla resurser**, klickar du på panelen för ditt Azure-behållarregister för att öppna dess egenskaper och klickar sedan på **Åtkomstnycklar**. Kopiera värdena i fälten **Användarnamn**, **lösenord** och **Inloggningsserver**. 

2. I VS Code-utforskaren högerklickar du på filen **module.json** och klickar sedan på **Docker-avbildning för Build and Push IoT-modul**. I popup-listrutan överst i VS Code-fönstret väljer du din behållarplattform, antingen **amd64** för Linux-behållare eller **windows amd64** för Windows-behållare. VS Code skapar sedan koden, lägger `FilterModule.dll` i en behållare och push-överför den till det behållarregister som du har angett.


3. Den fullständiga adressen med tagg för behållaravbildningen finns i den integrerade VS Code-terminalen. Mer information om versions- och push-definitionen finns i filen `module.json`.

## <a name="add-registry-credentials-to-edge-runtime"></a>Lägga till autentiseringsuppgifter för registret i Edge-körningen
Lägg till autentiseringsuppgifterna för ditt register i Edge-runtimen på den dator där du kör din Edge-enhet. Med dessa autentiseringsuppgifter får körningen åtkomst till att hämta behållaren. 

- I Windows kör du följande kommando:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- I Linux kör du följande kommando:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Kör lösningen

1. I [Azure Portal](https://portal.azure.com), navigerar du till din IoT-hubb.
2. Gå till **IoT Edge (förhandsversion)** och välj IoT Edge-enhet.
3. Välj **Ange moduler**. 
4. Kontrollera att modulen **tempSensor** fylls i automatiskt. Om inte använder du följande steg för att lägga till den:
    1. Välj **Lägg till IoT Edge-modul**.
    2. I **Namn**-fältet skriver du `tempSensor`.
    3. I **URI för avbildning**-fältet skriver du `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Lämna de andra inställningarna oförändrade och klicka på **Spara**.
5. Lägg till modulen **filterModule** som du skapade i föregående avsnitt. 
    1. Välj **Lägg till IoT Edge-modul**.
    2. I **Namn**-fältet skriver du `filterModule`.
    3. I **URI för avbildning**-fältet, anger du adressen till din avbildning, till exempel `<your container registry address>/filtermodule:0.0.1-amd64`. Avbildningens fullständiga adress kan hittas i föregående avsnitt.
    4. Markera kryssrutan **Aktivera** så att du kan redigera modultvillingen. 
    5. Ersätt JSON i textrutan för modultvillingen med följande JSON: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Klicka på **Spara**.
6. Klicka på **Nästa**.
7. I steget **Ange vägar** kopierar du den JSON nedanför till textrutan. Moduler publicerar alla meddelanden i Edge-körningen. Deklarativa regler i körningen definierar var meddelandena flödar. I den här självstudien behöver du två vägar. Den första vägen transporterar meddelanden från temperatursensorn till filtermodulen via slutpunkten ”input1”, vilket är den slutpunkt som du konfigurerade med hanteraren **FilterMessages**. Den andra vägen transporterar meddelanden från filtermodulen till IoT Hub. I den här vägen är `upstream` ett särskilt mål som talar om för Edge-hubben att skicka meddelanden till IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

8. Klicka på **Nästa**.
9. I steget **Granska mallen**, klickar du på **Skicka**. 
10. Återgå till IoT Edge-enhetens informationssida och välj **Uppdatera**. Du bör nu se att den nya **filtermodule** körs tillsammans med **tempSensor**-modulen och **IoT Edge-körning**. 

## <a name="view-generated-data"></a>Visa genererade data

Övervaka meddelanden från enhet till molnet som skickas från din IoT Edge-enhet till din IoT-hubb:
1. Konfigurera Azure IoT Toolkit-tillägget med anslutningssträngen för din IoT-hubb: 
    1. Öppna VS Code-utforskaren genom att välja **Visa** > **Utforskaren**. 
    2. I utforskaren klickar du på **IOT HUB-ENHETER** och sedan på **...**. Klicka på **Ange anslutningssträngen för IoT Hub** och ange anslutningssträngen för den IoT-hubb som din IoT Edge-enhet ansluter till i popup-fönstret. 

        För att hitta anslutningssträngen klickar du på panelen för din IoT-hubb i Azure Portal och sedan på **Principer för delad åtkomst**. I **Principer för delad åtkomst** klickar du på principen **iothubowner** och kopierar IoT Hub-anslutningssträngen i fönstret **iothubowner**.   

2. Om du vill övervaka data som kommer till IoT-hubben väljer du **Visa** > **Kommandopalett** och söker efter menykommandot **IoT: Börja övervaka D2C-meddelande**. 
3. Om du vill stoppa dataövervakningen använder du kommandot **IoT: Stoppa övervakning av D2C-meddelande**. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet. Du kan fortsätta med någon av följande självstudier om du vill lära dig mer om hur Azure IoT Edge kan förvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Distribuera Azure-funktionen som en modul](tutorial-deploy-function.md)
> [Distribuera Azure Stream Analytics som en modul](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
