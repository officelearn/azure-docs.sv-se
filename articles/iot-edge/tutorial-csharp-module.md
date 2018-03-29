---
title: Azure IoT kant C#-modulen | Microsoft Docs
description: Skapa en IoT-Edge-modul med C#-kod och distribuerar den till en insticksenhet
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 95ca66f34548f86e25c1e7af331fa88797847906
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Utveckla och distribuera en C# IoT kant-modul till den simulerade enheten - förhandsgranskning

Du kan använda IoT kant-moduler för att distribuera kod som implementerar affärslogiken direkt till IoT Edge-enheter. Den här självstudiekursen vägleder dig genom att skapa och distribuera en IoT-Edge-modul som filtrerar sensordata. Ska du använda simulerade IoT gränsenheten som du skapade i distribuera Azure IoT kanten på en simulerad enhet i [Windows] [ lnk-tutorial1-win] eller [Linux] [ lnk-tutorial1-lin]självstudier. I den här guiden får du lära dig hur man:    

> [!div class="checklist"]
> * Använd Visual Studio-koden för att skapa en IoT kant-modul som baseras på .NET core 2.0
> * Använda Visual Studio Code och Docker för att skapa en docker-avbildning och publicera den i registret 
> * Distribuera modulen till din IoT-Edge-enhet
> * Visa genererade data


Modulen IoT kant som du skapar i den här självstudiekursen filtrerar temperatur data som genereras av enheten. Den endast skickar meddelanden uppströms om är överskrider ett angivet tröskelvärde. Den här typen av analys i utkanten är användbart för att minska mängden data meddelas och lagras i molnet. 

## <a name="prerequisites"></a>Förutsättningar

* Azure IoT gränsenheten som du skapade i Snabbstart eller första självstudierna.
* Primärnyckelns anslutningssträng för IoT Edge-enheten.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT kant-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [C# för Visual Studio Code (drivs av OmniSharp) tillägg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/) på samma dator som har Visual Studio-koden. Community Edition (CE) är tillräcklig för den här kursen. 
* [.NET core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Skapa ett behållarregister
I de här självstudierna använder Azure IoT kant-tillägget för VS-kod att skapa en modul och skapa en **behållaren image** från filer. Sedan du överför avbildningen till en **registret** som lagrar och hanterar dina bilder. Slutligen kan distribuera du avbildningen från registret ska köras på enheten IoT kant.  

Du kan använda alla Docker-kompatibel registret för den här självstudiekursen. Två populära Docker registret tjänster som är tillgängliga i molnet är [Azure Container registret](https://docs.microsoft.com/azure/container-registry/) och [Docker-hubb](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Den här kursen används Azure Container registret. 

1. I den [Azure-portalen](https://portal.azure.com)väljer **skapar du en resurs** > **behållare** > **Azure Container registret** .
2. Namnge din registret, Välj en prenumeration, välja en resursgrupp och ange SKU: N till **grundläggande**. 
3. Välj **Skapa**.
4. När du har skapat behållaren registret går du till den och väljer **åtkomstnycklar**. 
5. Växla **administratörsanvändare** till **aktivera**.
6. Kopiera värdena för **inloggningsserver**, **användarnamn**, och **lösenord**. Du ska använda dessa värden senare under kursen när du publicerar Docker-avbildningen i registret, och när du lägger till registernyckeln autentiseringsuppgifterna Edge-körningsmiljön. 

## <a name="create-an-iot-edge-module-project"></a>Skapa ett projekt för IoT kant-modul
Följande steg visar du hur du skapar en IoT-Edge-modul som baseras på .NET core 2.0 med hjälp av Visual Studio Code och Azure IoT kant-tillägget.
1. I Visual Studio Code väljer **visa** > **integrerad Terminal** att öppna VS koden integrerad terminal.
2. Ange följande kommando för att installera (eller uppdatera) i terminalen integrerad i **AzureIoTEdgeModule** mallen i dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

3. Skapa ett projekt för den nya modulen. Följande kommando skapar projektmappen **FilterModule**, med lagringsplatsen för behållaren. Den andra parametern bör vara i form av `<your container registry name>.azurecr.io` om du använder Azure-behållaren registret. Ange följande kommando i den aktuella arbetsmappen:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule -r <your container registry address>/filtermodule
    ```
 
4. Välj **filen** > **öppna mappen**.
5. Bläddra till den **FilterModule** mappen och klicka på **Välj mapp** öppna projektet i VS-kod.
6. Klicka i VS kod explorer **Program.cs** att öppna den.

   ![Öppna Program.cs][1]

7. Längst upp i den **FilterModule** namnområde, lägga till tre `using` instruktioner för typer som används vid ett senare tillfälle:

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
    ```

8. Lägg till den `temperatureThreshold` variabeln i **programmet** klass. Den här variabeln anger det värde som uppmätta temperaturen får överstiga för data som ska skickas till IoT-hubb. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

9. Lägg till den `MessageBody`, `Machine`, och `Ambient` klasser till den **programmet** klass. Dessa klasser definierar det förväntade schemat för brödtexten för inkommande meddelanden.

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

10. I den **Init** metoden koden skapar och konfigurerar en **DeviceClient** objekt. Det här objektet kan modulen som ska ansluta till den lokala körningsmiljön Azure IoT Edge att skicka och ta emot meddelanden. Anslutningssträngen som används i den **Init** modulen metoden anges av IoT kant runtime. När du har skapat den **DeviceClient**, koden läser TemperatureThreshold från modulen-dubbla egenskaper och registrerar ett återanrop för att ta emot meddelanden från kant för IoT-hubben via den **input1**slutpunkt. Ersätt den `SetInputMessageHandlerAsync` metod med en ny och lägga till en `SetDesiredPropertyUpdateCallbackAsync` metod för egenskaper uppdateringar. För att göra den här ändringen kan du ersätta den sista raden i det **Init** metoden med följande kod:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read TemperatureThreshold from Module Twin Desired Properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    if (moduleTwinCollection["TemperatureThreshold"] != null)
    {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    }

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

11. Lägg till den `onDesiredPropertiesUpdate` metod för att den **programmet** klass. Den här metoden tar emot uppdateringar på egenskaperna från modulen dubbla och uppdaterar den **temperatureThreshold** variabeln för att matcha. Alla moduler som har sina egna modulen dubbla där du kan konfigurera den kod som körs i en modul direkt från molnet.

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

12. Ersätt den `PipeMessage` metod med den `FilterMessages` metoden. Den här metoden anropas när modulen tar emot ett meddelande från kant för IoT-hubben. Den filtrerar ut meddelanden som rapporterar temperaturer under temperatur tröskelvärdet som angetts via module dubbla. Det ger också den **MessageType** egenskap i meddelandet med värdet **avisering**. 

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

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Skapa en Docker-avbildning och publicera den i registret

1. Logga in på Docker genom att ange följande kommando i en integrerad terminal VS-kod: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

2. VS kod explorer, högerklicka på den **module.json** fil och klicka på **Build och kant Push IoT modulen Docker avbildning**. I popup-listrutan överst i fönstret VS-kod, väljer du den behållare plattformen antingen **amd64** för Linux-behållare eller **windows amd64** för Windows-behållaren. VS-kod och skapar sedan koden, containerize i `FilterModule.dll` och push i registret för behållaren som du angav.


3. Du kan få fullständig behållaren image-adress med taggen i VS koden integrerad terminal. Mer information om bygg- och push-definition kan referera till den `module.json` filen.

## <a name="add-registry-credentials-to-edge-runtime"></a>Lägga till registret autentiseringsuppgifter Edge runtime
Lägg till autentiseringsuppgifterna för registret Edge körningsmiljön på datorn där du kör Edge-enhet. Dessa autentiseringsuppgifter ge runtime-åtkomst till pull-behållaren. 

- För Windows, kör du följande kommando:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- Kör följande kommando för Linux:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Kör lösningen

1. I den [Azure-portalen](https://portal.azure.com), navigera till din IoT-hubb.
2. Gå till **IoT Edge (förhandsversion)** och välj IoT Edge-enhet.
3. Välj **ange moduler**. 
4. Kontrollera att den **tempSensor** modulen fylls i automatiskt. Om inte, Använd följande steg för att lägga till den:
    1. Välj **lägga till IoT kant modul**.
    2. I den **namn** anger `tempSensor`.
    3. I den **avbildningen URI** anger `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Lämna de andra inställningarna oförändrad och klicka på **spara**.
5. Lägg till den **filterModule** modul som du skapade i föregående avsnitt. 
    1. Välj **lägga till IoT kant modul**.
    2. I den **namn** anger `filterModule`.
    3. I den **avbildningen URI** , ange din adress bild, till exempel `<your container registry address>/filtermodule:0.0.1-amd64`. Adressen för fullständig avbildning finns i föregående avsnitt.
    4. Kontrollera den **aktivera** så att du kan redigera modulen dubbla. 
    5. Ersätt JSON i textrutan för modulen dubbla med följande JSON: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Klicka på **Spara**.
6. Klicka på **Nästa**.
7. I den **ange vägar** steg, kopiera JSON nedan i textrutan. Moduler publicera alla meddelanden i Edge-körningsmiljön. Deklarativ regler i körningsmiljön definiera där meddelanden. I den här kursen behöver du två vägar. Första vägen transporter meddelanden från temperatursensorn till filtermodul via slutpunkten ”input1”, vilket är den slutpunkt du konfigurerade med den **FilterMessages** hanterare. Andra vägen transporter meddelanden från modulen filter till IoT-hubb. I den här vägen `upstream` är särskilda mål som talar om kant-hubb för att skicka meddelanden till IoT-hubb. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

8. Klicka på **Nästa**.
9. I den **granska mallen** , klicka på **skicka**. 
10. Gå tillbaka till sidan IoT kant enheten information och klickar på **uppdatera**. Du bör se den nya **filtermodule** körs tillsammans med den **tempSensor** modulen och **IoT kant runtime**. 

## <a name="view-generated-data"></a>Visa genererade data

Så här övervakar enhet till moln-meddelanden som skickas från IoT-Edge-enhet till din IoT-hubb:
1. Konfigurera Azure IoT Toolkit-tillägget med anslutningssträngen för din IoT-hubb: 
    1. Öppna Utforskaren VS kod genom att välja **visa** > **Explorer**. 
    2. I Utforskaren klickar du på **IOT HUB-enheter** och klicka sedan på **...** . Klicka på **ange anslutningssträngen för IoT-hubb** och ange anslutningssträngen för IoT-hubb som din IoT insticksenhet ansluter till i popup-fönstret. 

        För att hitta anslutningssträngen panelen för din IoT-hubb i Azure-portalen och klicka sedan på **principer för delad åtkomst**. I **principer för delad åtkomst**, klicka på den **iothubowner** princip och kopiera IoT-hubb anslutningen sträng i den **iothubowner** fönster.   

2. Om du vill övervaka data som inkommer till IoT-hubben, Välj **visa** > **kommandot paletten** och Sök efter den **IoT: börja övervaka D2C meddelande** menykommandot. 
3. Om du vill stoppa övervakningen av data, Använd den **IoT: stoppa övervakningen D2C meddelandet** kommando. 

## <a name="next-steps"></a>Nästa steg

I kursen får skapat du en gräns för IoT-modul som innehåller kod för att filtrera rådata som genereras av enheten IoT kant. Du kan fortsätta in på något av följande kurser vill veta mer om andra sätt att Azure IoT gräns kan du göra data till affärsinsikter kant.

> [!div class="nextstepaction"]
> [Distribuera Azure-funktion som en modul](tutorial-deploy-function.md)
> [distribuera Azure Stream Analytics som en modul](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
