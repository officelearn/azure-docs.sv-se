---
title: Azure IoT kant C#-modulen | Microsoft Docs
description: Skapa en IoT-Edge-modul med C#-kod och distribuerar den till en insticksenhet
services: iot-edge
keywords: 
author: JimacoMS2
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: f93cfcdffd79b4cccdbd5f7c67ec42499bf7628c
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2017
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Utveckla och distribuera en C# IoT kant-modul till den simulerade enheten - förhandsgranskning

Du kan använda IoT kant-moduler för att distribuera kod som implementerar affärslogiken direkt till IoT Edge-enheter. Den här självstudiekursen vägleder dig genom att skapa och distribuera en IoT-Edge-modul som filtrerar sensordata på simulerade IoT gränsenheten som du skapade i distribuera Azure IoT kanten på en simulerad enhet på [Windows] [ lnk-tutorial1-win]eller [Linux] [ lnk-tutorial1-lin] självstudier. I den här guiden får du lära dig hur man:    

> [!div class="checklist"]
> * Använd Visual Studio-koden för att skapa en IoT kant-modul som baseras på .NET core 2.0
> * Använda Visual Studio Code och Docker för att skapa en docker-avbildning och publicera den i registret 
> * Distribuera modulen till din IoT-Edge-enhet
> * Visa genererade data


Modulen IoT kant som du skapar i den här självstudiekursen filtrerar temperatur data som genereras av enheten och endast skickar meddelanden uppströms när temperaturen är högre än angivet tröskelvärde. 

## <a name="prerequisites"></a>Krav

* Azure IoT gränsenheten som du skapade i Snabbstart eller tidigare kursen.
* IoT-hubb anslutningssträngen för IoT-hubb som din IoT insticksenhet ansluter till.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT kant-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). (Du kan installera tillägget panelen tillägg i Visual Studio Code.)
* [C# för Visual Studio Code (drivs av OmniSharp) tillägg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). (Du kan installera tillägget panelen tillägg i Visual Studio Code.)
* [Docker](https://docs.docker.com/engine/installation/). Community Edition (CE) för din plattform är tillräcklig för den här kursen. Kontrollera att du installerar den på den dator som VS kod körs på.
* [.NET core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 

## <a name="choose-or-sign-up-for-a-docker-registry"></a>Välj eller registrera dig för en Docker-registret
I de här självstudierna använder Azure IoT kant-tillägget för VS-kod att skapa en modul och skapa en [Docker bild](https://docs.docker.com/glossary/?term=image). Sedan du push Docker avbildningen till en [Docker databasen](https://docs.docker.com/glossary/?term=repository) finns på en [Docker registret](https://docs.docker.com/glossary/?term=registry). Slutligen kan du distribuera Docker avbildningen paketeras som en [dockerbehållare](https://docs.docker.com/glossary/?term=container) från registret på enheten IoT kant.  

Du kan använda alla Docker-kompatibel registret för den här självstudiekursen. Två populära Docker registret tjänster som är tillgängliga i molnet är **Azure Container registret** och **Docker-hubb**:

- [Azure Container registret](https://docs.microsoft.com/en-us/azure/container-registry/) är tillgänglig med en [betald prenumeration](https://azure.microsoft.com/en-us/pricing/details/container-registry/). I den här självstudien den **grundläggande** prenumerationen är tillräckliga. 

- [Docker-hubb](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) erbjuder en kostnadsfri privata databas om du registrerar dig för en (kostnadsfritt) Docker-ID. 
    1. Om du vill registrera dig för en Docker-ID, följ instruktionerna i [registrera dig för en Docker-ID](https://docs.docker.com/docker-id/#register-for-a-docker-id) på Docker-webbplatsen. 

    2. Om du vill skapa en privat Docker-databas, följ instruktionerna i [en ny databas skapas på Docker hubben](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub) på Docker-webbplatsen.

I den här självstudien förekommande anges kommandon för både Azure-behållare registret och Docker-hubb.

## <a name="create-an-iot-edge-module-project"></a>Skapa ett projekt för IoT kant-modul
Följande steg visar du hur du skapar en IoT-Edge-modul som baseras på .NET core 2.0 med hjälp av Visual Studio Code och Azure IoT kant-tillägget.
1. Öppna VS-kod.
2. Använd den **visa | Integrerad Terminal** menyn för att öppna VS koden integrerad terminal.
3. Ange följande kommando för att installera (eller uppdatera) i terminalen integrerad i **AzureIoTEdgeModule** mallen i dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. Ange följande kommando för att skapa ett projekt för ny modul i integrerad terminalen:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```

    >[!NOTE]
    > Det här kommandot skapar projektmappen, **FilterModule**, i den aktuella arbetsmappen. Om du vill skapa den på en annan plats, ändra kataloger innan kommandot körs.
 
3. Använd den **filen | Öppna mappen** menykommandot, bläddra till den **FilterModule** mappen och klicka på **Välj mapp** öppna projektet i VS-kod.
4. Klicka i VS kod explorer **Program.cs** att öppna den.
5. Lägg till följande fält till den **programmet** klass.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. Lägg till följande klasser till den **programmet** klass. Dessa klasser definierar det förväntade schemat för brödtexten för inkommande meddelanden.

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

1. I den **Init** metoden koden skapar och konfigurerar en **DeviceClient** objekt. Det här objektet kan modulen som ska ansluta till den lokala körningsmiljön Azure IoT Edge att skicka och ta emot meddelanden. Strängparametern anslutningen används i den **Init** modulen metoden anges av IoT kant runtime i miljövariabeln **EdgeHubConnectionString**. När du har skapat den **DeviceClient**, koden registrerar ett återanrop för att ta emot meddelanden från kant för IoT-hubben via den **input1** slutpunkt. Ersätt den metod som används som återanrop för hantering av meddelanden med en ny och koppla ett återanrop för uppdateringar av egenskaper på modulen dubbla. För att göra den här ändringen kan du ersätta den sista raden i det **Init** metoden med följande kod:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

1. Lägg till följande metod för att den **programmet** klassen för att uppdatera den **temperatureThreshold** fältet baserat på de skickas av backend-tjänsten via module dubbla egenskaper. Alla moduler som har sina egna modulen dubbla. En modul dubbla kan en backend-tjänst som konfigurerar koden körs i en modul.

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

1. Ersätt den **PipeMessage** metod med följande metod. Den här metoden anropas när modulen skickas ett meddelande från kant-hubben. Den filtrerar meddelanden utifrån temperatur värdet i själva meddelandet och temperatur tröskelvärdet som angetts via module dubbla.

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        int counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            byte[] messageBytes = message.GetBytes();
            string messageString = Encoding.UTF8.GetString(messageBytes);
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
            DeviceClient deviceClient = (DeviceClient)userContext;
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

11. Bygga projektet. Använd den **visa | Explorer** menyn för att öppna Utforskaren VS-kod. I Utforskaren, högerklicka på den **FilterModule.csproj** fil och klicka på **skapa IoT kant modulen** att kompilera modulen och exportera binärfilen och dess beroenden till en mapp att Docker-avbildning har skapats från i nästa steg.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Skapa en Docker-avbildning och publicera den i registret

1. Skapa Docker-bild.
    1. Klicka i VS kod explorer den **Docker** mapp för att öppna den. Välj sedan antingen mappen för din plattform för behållaren **linux x64** eller **windows nano**. 
    2. Högerklicka på den **Dockerfile** fil och klicka på **skapa IoT kant modulen Docker bild**. 
    3. I den **Välj mappen** rutan antingen bläddra till eller ange `./bin/Debug/netcoreapp2.0/publish`. Klicka på **Välj mapp som EXE_DIR**.
    4. Ange avbildningens namn i popup-textrutan längst upp i fönstret VS-kod. Till exempel:`<docker registry address>/filtermodule:latest`; där *docker registret adress* är Docker-ID om du använder Docker-hubb eller liknande `<your registry name>.azurecr.io`, om du använder Azure-behållare registret.
 
4. Logga in på Docker. Ange följande kommando i integrerad terminal: 

    - Docker-hubb (ange dina autentiseringsuppgifter när du uppmanas):
        
        ```csh/sh
        docker login
        ```

    - För Azure-behållaren registernyckeln:
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        Användarnamn, lösenord och logga in server som ska användas i det här kommandot finns [Azure-portalen] (https://portal.azure.com). Från **alla resurser**, klickar du på panelen för din Azure-behållaren registret för att öppna dess egenskaper och klicka sedan på **åtkomstnycklar**. Kopiera värdena i den **användarnamn**, **lösenord**, och **inloggningsserver** fält. Inloggningen server sould vara i formatet: `<your registry name>.azurecr.io`.

3. Skicka bilden till Docker-databasen. Använd den **visa | Kommandot paletten... | Edge: Push IoT kant modulen Docker bild** menyn kommando och ange avbildningens namn i textrutan popup visas överst i fönstret VS-kod. Använd samma namn för bilden som du använde i steg 1.d.

## <a name="add-registry-credentials-to-edge-runtime-on-your-edge-device"></a>Lägga till registret autentiseringsuppgifter Edge runtime i Edge-enhet
Lägg till autentiseringsuppgifterna för registret Edge körningsmiljön på datorn där du kör Edge-enhet. Detta ger runtime-åtkomst till pull-behållaren. 

- För Windows, kör du följande kommando:
    
    ```cmd/sh
    iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

- Kör följande kommando för Linux:
    
    ```cmd/sh
    sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

## <a name="run-the-solution"></a>Köra lösningen

1. I den [Azure-portalen](https://portal.azure.com), navigera till din IoT-hubb.
2. Gå till **IoT kant (förhandsgranskning)** och välj IoT-Edge-enhet.
3. Välj **ange moduler**. 
2. Lägg till den **tempSensor** modul. Detta krävs om du inte har använt den **tempSensor** modul till din IoT-Edge-enhet.
    1. Välj **lägga till IoT kant modul**.
    2. I den **namn** anger `tempSensor`.
    3. I den **avbildningen URI** anger `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Lämna de andra inställningarna oförändrad och klicka på **spara**.
9. Lägg till den **filtermodule**
    1. Välj **Lägg till IoT kant modul** igen.
    2. I den **namn** anger `filtermodule`.
    3. I den **bild** , ange din adress bild, till exempel `<docker registry address>/filtermodule:latest`.
    4. Kontrollera den **redigera modulen dubbla** rutan.
    5. Ersätt JSON i textrutan för modulen dubbla med följande JSON: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Klicka på **Spara**.
12. Klicka på **Nästa**.
13. I den **ange vägar** steg, kopiera JSON nedan i textrutan. Moduler publicera alla meddelanden i Edge-körningsmiljön. Deklarativ regler i körningsmiljön definiera där dessa meddelanden. I den här kursen behöver du två vägar. Första vägen transporter meddelanden från temperatursensorn till filtermodul via slutpunkten ”input1”, vilket är den slutpunkt du konfigurerade med den **FilterMessages** hanterare. Andra vägen transporter meddelanden från modulen filter till IoT-hubb. I den här vägen `upstream` är särskilda mål som talar om kant-hubb för att skicka meddelanden till IoT-hubb. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Klicka på **Nästa**.
5. I den **granska mallen** , klicka på **skicka**. 
6. Gå tillbaka till sidan IoT kant enheten information och klickar på **uppdatera**. Du bör se den nya **filtermodule** körs tillsammans med den **tempSensor** modulen och **IoT kant runtime**. 

## <a name="view-generated-data"></a>Visa genererade data

Så här övervakar enhet till moln-meddelanden som skickas från IoT-Edge-enhet till din IoT-hubb:
1. Konfigurera Azure IoT Toolkit-tillägget med anslutningssträngen för din IoT-hubb: 
    1. Använd den **visa | Explorer** menyn för att öppna Utforskaren VS-kod. 
    3. I Utforskaren klickar du på **IOT HUB-enheter** och klicka sedan på **...** . Klicka på **ange anslutningssträngen för IoT-hubb** och ange anslutningssträngen för IoT-hubb som din IoT insticksenhet ansluter till i popup-fönstret. 

        För att hitta anslutningssträngen panelen för din IoT-hubb i Azure-portalen och klicka sedan på **principer för delad åtkomst**. I **principer för delad åtkomst**, klicka på den **iothubowner** princip och kopiera IoT-hubb anslutningen sträng i den **iothubowner** fönster.   

1. Du övervakar data som inkommer till IoT-hubben i **visa | Kommandot paletten... | IoT: Börja övervaka D2C meddelandet** kommando. 
2. Om du vill stoppa övervakningen av data, Använd den **visa | Kommandot paletten... | IoT: Stoppa övervakningen D2C meddelandet** kommando. 

## <a name="next-steps"></a>Nästa steg

I kursen får skapat du en gräns för IoT-modul som innehåller kod för att filtrera rådata som genereras av enheten IoT kant. Du kan fortsätta in på något av följande kurser vill veta mer om andra sätt att Azure IoT gräns kan du göra data till affärsinsikter kant.

> [!div class="nextstepaction"]
> [Distribuera Azure-funktion som en modul](tutorial-deploy-function.md)
> [distribuera Azure Stream Analytics som en modul](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
