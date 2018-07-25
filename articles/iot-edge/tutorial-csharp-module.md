---
title: Självstudie om Azure IoT Edge C# | Microsoft Docs
description: Den här självstudien visar hur du skapar en IoT Edge-modul med C#-kod och distribuerar den till en gränsenhet.
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 12a17edc74ef0fbc573be0fc167aa7921e599341
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005874"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Självstudie: Utveckla en C# IoT Edge-modul och distribuera till den simulerade enheten

Du kan använda Azure IoT Edge-moduler för att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. Du kommer att använda den simulerade IoT Edge-enheten som du skapade i snabbstarterna Distribuera Azure IoT Edge på en simulerad enhet i [Windows][lnk-tutorial1-win] eller [Linux][lnk-tutorial1-lin]. I den här guiden får du lära dig att:    

> [!div class="checklist"]
> * Använd Visual Studio Code för att skapa en IoT Edge-modul som baseras på .NET Core 2.0 SDK.
> * Använd Visual Studio Code och Docker för att skapa en Docker-avbildning och publicera den till ditt register.
> * Distribuera modulen till din IoT Edge-enhet.
> * Visa genererade data.


IoT Edge-modulen som du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys är användbar för att minska mängden data som kommuniceras till och lagras i molnet. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.


## <a name="prerequisites"></a>Nödvändiga komponenter

* Azure IoT Edge-enheten som du skapade i snabbstarten för [Linux-](quickstart-linux.md) eller [Windows-enheter](quickstart.md).
* Primärnyckelns anslutningssträng för IoT Edge-enheten.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Azure IoT Edge-tillägg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) för Visual Studio Code. 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) på utvecklingsdatorn. 


## <a name="create-a-container-registry"></a>Skapa ett containerregister
I den här självstudien använder du Azure IoT Edge-tillägget för VS Code för att skapa en modul och skapa en **containeravbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.  

Du kan använda valfritt Docker-kompatibelt register för den här självstudien. Två populära Docker-registertjänster som finns tillgängliga i molnet är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). I den här kursen använder vi Azure Container Registry. 

1. I [Azure Portal](https://portal.azure.com) väljer du **Skapa en resurs** > **Behållare** > **Azure Container Registry**.
2. Namnge ditt register, välj en prenumeration, välj en resursgrupp och ange SKU:n till **Basic**. 
3. Välj **Skapa**.
4. När du har skapat ditt containerregister går du till det och väljer **Åtkomstnycklar**. 
5. Växla till **Administratörsanvändare** för att **Aktivera**.
6. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du kan använda dessa värden senare under kursen för att publicera Docker-avbildningen till registret och för att lägga till autentiseringsuppgifterna för registret i Azure IoT Edge-körningen. 

## <a name="create-an-iot-edge-module-project"></a>Skapa ett projekt för IoT Edge-modulen
I följande steg skapas ett IoT Edge-modulprojekt baserat på .NET Core 2.0 SDK med hjälp av Visual Studio Code och Azure IoT Edge-tillägget.
1. I Visual Studio Code väljer du **Visa** > **Integrerad terminal** för att öppna den integrerade Visual Studio Code-terminalen.
2. Välj **Visa** > **Kommandopalett** för att öppna kommandopaletten i VS Code. 
3. Ange och kör kommandot **Azure: Logga in** på kommandopaletten och följ anvisningarna för att logga in med ditt Azure-konto. Om du redan har loggat in kan du hoppa över det här steget.
4. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) på kommandopaletten. Ange följande information i kommandopaletten för att skapa din lösning: 

   1. Välj den mapp där du vill skapa lösningen. 
   2. Ange ett namn för din lösning eller välj standardnamnet **EdgeSolution**.
   3. Välj **C# Module** som mall för modulen. 
   4. Ge modulen namnet **CSharpModule**. 
   5. Ange det Azure-containerregister som du skapade i föregående avsnitt som avbildningslagringsplats för din första modul. Ersätt **localhost:5000** med det serverinloggningsvärde som du kopierade. Den slutliga strängen ser ut så här: \<registernamn\>.azurecr.io/csharpmodule.

4.  VS Code-fönstret läser in IoT Edge-lösningens arbetsyta: modulmappen, en \.vscode-mapp, en mallfil för distributionsmanifestet och en \.env-fil. Öppna **modules** > **CSharpModule** > **Program.cs** i VS Code-utforskaren.

5. Överst i **CSharpModule**-namnrymden läger du till tre **using**-instruktioner för typer som ska användas senare:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

6. Lägg till variabeln **temperatureThreshold** till klassen **Program**. Variabeln anger det värde som den uppmätta temperaturen måste överstiga för att data ska skickas till IoT-hubben. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

7. Lägg till klasserna **MessageBody**, **Machine** och **Ambient** till klassen **Program**. Dessa klasser definierar det förväntade schemat för brödtexten i inkommande meddelanden.

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

8. I metoden **Init** skapar och konfigurerar koden ett **ModuleClient**-objekt. Med det här objektet kan modulen ansluta till den lokala Azure IoT Edge-körningen för att skicka och ta emot meddelanden. Anslutningssträngen som används i **Init**-metoden skickas till modulen av IoT Edge-körningen. När du har skapat **ModuleClient** läser koden **temperatureThreshold**-värdet från modultvillingens önskade egenskaper. Koden registrerar ett återanrop för att ta emot meddelanden från IoT Edge-hubben via **input1**-slutpunkten. Ersätt metoden **SetInputMessageHandlerAsync** med en ny och lägg till en **SetDesiredPropertyUpdateCallbackAsync**-metod för uppdateringar av de önskade egenskaperna. Gör den här ändringen genom att ersätta den sista raden i **Init**-metoden med följande kod:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    try {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    } catch(ArgumentOutOfRangeException e) {
        Console.WriteLine($"Property TemperatureThreshold not exist: {e.Message}"); 
    }

    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. Lägg till metoden **onDesiredPropertiesUpdate** till klassen **Program**. Metoden tar emot uppdateringar av önskade egenskaper från modultvillingen och uppdaterar variabeln **temperatureThreshold** som ska matchas. Alla moduler har en egen modultvilling, vilket innebär att du kan konfigurera den kod som körs i en modul direkt från molnet.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
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

10. Ersätt metoden **PipeMessage** med metoden **FilterMessages**. Den här metoden anropas när modulen tar emot ett meddelande från IoT Edge-hubben. Den filtrerar ut meddelanden som rapporterar temperaturer under temperaturtröskelvärdet som angetts via modultvillingen. Den lägger också till egenskapen **MessageType** i meddelandet med värdet angivet som **Avisering**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
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
                await moduleClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

11. Spara filen.

## <a name="build-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod i **CSharpModule** som filtrerar bort meddelanden där den rapporterade maskintemperaturen ligger under det godkända tröskelvärdet. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister. 

1. Logga in på Docker genom att ange följande kommando i den integrerade Visual Studio Code-terminalen: Sedan kan du skicka modulavbildningen till ditt Azure-containerregister.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Använd användarnamnet, lösenordet och inloggningsservern som du kopierade från Azure-containerregistret i det första avsnittet. Du kan också hämta dessa värden från avsnittet **Åtkomstnycklar** i ditt register på Azure Portal.

2. I VS Code-utforskaren öppnar du filen deployment.template.json på arbetsytan för IoT Edge-lösningen. Den här filen instruerar **$edgeAgent** att distribuera två moduler: **tempSensor** och **CSharpModule**. En Linux amd64-version av avbildningen har angetts för **CSharpModule.image**-värdet. Läs mer om distributionsmanifest i avsnittet om att [förstå hur IoT Edge-moduler kan användas, konfigureras och återanvändas](module-composition.md).

3. Avsnittet **registryCredentials** i filen deployment.template.json lagrar dina autentiseringsuppgifter för Docker-registret. Själva användarnamns- och lösenordsparen lagras i .env-filen, som ignoreras av git.  

4. Lägg till **CSharpModule**-modultvillingen till distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet **moduleContent** efter **$edgeHub**-modultvillingen: 
    ```json
        "CSharpModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Spara filen.

5. Högerklicka på filen deployment.template.json och välj **Skapa IoT Edge-lösning** i VS Code-utforskaren. 

När du instruerar Visual Studio Code att skapa din lösning hämtar den först information i distributionsmallen och genererar en .json-distributionsfil i en ny mapp med namnet **config**. Sedan körs två kommandon i en integrerad terminal: `docker build` och `docker push`. Dessa två kommandon skapar din kod, bäddar in CSharpModule.dll i en container och skickar sedan koden till det containerregister som du angav när du initierade lösningen. 

Den fullständiga adressen med tagg för containeravbildningen finns i den integrerade VS Code-terminalen. Avbildningsadressen skapas från information som finns i filen module.json med formatet \<lagringsplats\>:\<version\>-\<plattform\>. I den här självstudien bör den se ut så här: registryname.azurecr.io/csharpmodule:0.0.1-amd64.

## <a name="deploy-and-run-the-solution"></a>Distribuera och kör lösningen

1. Konfigurera Azure IoT Toolkit-tillägget med anslutningssträngen för din IoT-hubb: 

    1. Öppna VS Code-utforskaren genom att välja **Visa** > **Utforskaren**.

    1. Välj **Azure IoT Hub Devices** (Azure IoT Hub-enheter) i utforskaren, välj ellipsen (**...**) och sedan **Select IoT Hub** (Välj IoT Hub). Följ anvisningarna för att logga in på ditt Azure-konto och välja din IoT-hubb. 

       > [!Note]
       > Du kan också slutföra konfigurationen genom att välja **Set IoT Hub Connection String** (Ange IoT Hub-anslutningssträng). Ange anslutningssträngen för den IoT-hubb som din IoT Edge-enhet ansluter till i popup-fönstret.

2. Högerklicka på din IoT Edge-enhet i utforskaren för Azure IoT Hub-enheter och välj sedan **Create Deployment for IoT Edge device** (Skapa distribution för IoT Edge-enhet). Välj filen deployment.json i mappen config och välj sedan **Select Edge Deployment Manifest** (Välj distributionsmanifest för Edge).

3. Uppdatera avsnittet **Azure IoT Hub-enheter**. Du bör se den nya **CSharpModule** köras tillsammans med **TempSensor**-modulen och **$edgeAgent** och **$edgeHub**. 

## <a name="view-generated-data"></a>Visa genererade data

1. Om du vill övervaka data som kommer till IoT-hubben väljer du ellipsen (**...** ) och sedan **Start Monitoring D2C Messages** (Starta övervakning av D2C-meddelanden).
2. Om du vill övervaka D2C-meddelandet för en specifik enhet högerklickar du på enheten i listan och väljer **Starta övervakning av D2C-meddelanden**.
3. Om du vill stoppa dataövervakningen kör du kommandot **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: Sluta övervaka D2C-meddelande) på kommandopaletten. 
4. Om du vill visa eller uppdatera modultvillingen högerklickar du på modulen i listan och väljer **Redigera modultvilling**. Om du vill uppdatera modultvillingen sparar du tvilling-JSON-filen, högerklickar på redigeringsområdet och väljer **Uppdatera modultvilling**.
5. Om du vill visa Docker-loggar installerar du [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) för VS Code. Du kan hitta moduler som körs lokalt i Docker-utforskaren. Du kan visa dem i den integrerade terminalen genom att välja **Visa loggar** på snabbmenyn.
 
## <a name="clean-up-resources"></a>Rensa resurser 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader. 

> [!IMPORTANT]
> Det går inte att ångra borttagningen av Azure-resurser och Azure-resursgrupper. När dessa objekt tas bort, tas resursgruppen och alla resurser som finns i den bort permanent. Var noga så att du inte tar bort fel resursgrupp eller resurser av misstag. Om du har skapat IoT-hubben i en befintlig resursgrupp som innehåller resurser som du vill behålla, tar du bara bort själva IoT-hubbresursen i stället för att ta bort resursgruppen.
>

Om du bara vill ta bort IoT-hubben kör du följande kommando med namnet på hubben och resursgruppens namn:

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


Ta bort hela resursgruppen med namnet:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Resursgrupper**.

2. I textrutan **Filtrera efter namn** anger du namnet på resursgruppen som innehåller din IoT-hubb. 

3. Till höger om resursgruppen i resultatlistan väljer du ellipsen (**...** ) och väljer sedan **Ta bort resursgrupp**.

4. Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på resursgruppen igen för att bekräfta och välj sedan **Ta bort**. Efter en liten stund tas resursgruppen och resurser som finns i den bort.



## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul med kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att skapa egna moduler kan du läsa mer om hur du [utvecklar en C#-modul med Azure IoT Edge för Visual Studio Code](how-to-develop-csharp-module.md). Du kan fortsätta med någon av följande självstudier om du vill veta mer om hur Azure IoT Edge kan hjälpa dig att omvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Lagra data på gränsen med SQL Server-databaser](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
