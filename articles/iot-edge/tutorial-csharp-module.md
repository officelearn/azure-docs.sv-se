---
title: Självstudie om Azure IoT Edge C# | Microsoft Docs
description: Den här självstudien visar hur du skapar en IoT Edge-modul med C#-kod och distribuerar den till en gränsenhet
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 73b6397ecc97b9e289749aabddfdc4c6161375d4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38667353"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Självstudie: Utveckla en C# IoT Edge-modul och distribuera till den simulerade enheten

Du kan använda IoT Edge-moduler till att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. Du kommer att använda den simulerade IoT Edge-enheten som du skapade i självstudien Distribuera Azure IoT Edge på en simulerad enhet i [Windows][lnk-tutorial1-win] eller [Linux][lnk-tutorial1-lin]. I den här guiden får du lära dig hur man:    

> [!div class="checklist"]
> * Använda Visual Studio Code för att skapa en IoT Edge-modul som baseras på .NET Core 2.0
> * Använda Visual Studio Code och Docker för att skapa en Docker-avbildning och publicera den till ditt register 
> * Distribuera modulen till din IoT Edge-enhet
> * Visa genererade data


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
Följande steg visar hur du skapar ett IoT Edge-modulprojekt som baseras på .NET Core 2.0 med Visual Studio Code och Azure IoT Edge-tillägget.
1. I Visual Studio Code väljer du **Visa** > **Integrerad terminal** för att öppna den integrerade Visual Studio Code-terminalen.
2. Välj **Visa** > **Kommandopalett** för att öppna kommandopaletten i VS Code. 
3. Ange och kör kommandot **Azure: Logga in** i kommandopaletten och följ anvisningarna för att logga in med ditt Azure-konto. Om du redan har loggat in kan du hoppa över det här steget.
4. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) i kommandopaletten. Ange följande information i kommandopaletten för att skapa din lösning: 
   1. Välj den mapp där du vill skapa lösningen. 
   2. Ange ett namn för din lösning eller välj standardnamnet **EdgeSolution**.
   3. Välj **C# Module** som mall för modulen. 
   4. Ge modulen namnet **CSharpModule**. 
   5. Ange det Azure Container Registry som du skapade i föregående avsnitt som avbildningslagringsplats för din första modul. Ersätt **localhost:5000** med det serverinloggningsvärde som du kopierade. Den slutliga strängen ser ut som **\<registernamn\>.azurecr.io/csharpmodule**.
 
4. VS Code läser in arbetsytan för IoT Edge-lösningen. Det finns en **modules**-mapp, en **.vscode**-mapp, en mallfil för distributionsmanifestet och en **.env**-fil. Öppna **modules** > **CSharpModule** > **Program.cs**.

5. Längst upp i namnområdet **CSharpModule** lägger du till tre `using`-instruktioner för typer som vi använder vid ett senare tillfälle:

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
    ```

6. Lägg till variabeln `temperatureThreshold` i klassen **Program**. Variabeln anger det värde som den uppmätta temperaturen måste överstiga för att data ska skickas till IoT Hub. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

7. Lägg till klasserna `MessageBody`, `Machine` och `Ambient` i klassen **Program**. Dessa klasser definierar det förväntade schemat för brödtexten i inkommande meddelanden.

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

8. I metoden **Init** skapar och konfigurerar koden ett **ModuleClient**-objekt. Med det här objektet kan modulen ansluta till den lokala Azure IoT Edge-körningen för att skicka och ta emot meddelanden. Anslutningssträngen som används i **Init**-metoden skickas till modulen av IoT Edge-körningen. När du har skapat **ModuleClient** läser koden TemperatureThreshold från modultvillingens önskade egenskaper och registrerar en motringning för att ta emot meddelanden från IoT Edge-hubben via slutpunkten **input1**. Ersätt metoden `SetInputMessageHandlerAsync` med en ny och lägg till metoden `SetDesiredPropertyUpdateCallbackAsync` för önskade egenskapsuppdateringar. Gör den här ändringen genom att ersätta den sista raden i **Init**-metoden med följande kod:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read TemperatureThreshold from Module Twin Desired Properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    try {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    } catch(ArgumentOutOfRangeException e) {
        Console.WriteLine($"Property TemperatureThreshold not exist: {e.Message}"); 
    }

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. Lägg till metoden `onDesiredPropertiesUpdate` i klassen **Program**. Metoden tar emot uppdateringar av önskade egenskaper från modultvillingen och uppdaterar variabeln **temperatureThreshold** som ska matchas. Alla moduler har en egen modultvilling, vilket innebär att du kan konfigurera den kod som körs i en modul direkt från molnet.

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

10. Ersätt metoden `PipeMessage` med metoden `FilterMessages`. Den här metoden anropas när modulen tar emot ett meddelande från IoT Edge-hubben. Den filtrerar ut meddelanden som rapporterar temperaturer under temperaturtröskelvärdet som angetts via modultvillingen. Den lägger också till egenskapen **MessageType** i meddelandet med värdet angivet som **Avisering**. 

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
                await moduleClient.SendEventAsync("output1", filteredMessage);
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
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

11. Spara filen.

## <a name="build-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

I föregående avsnitt skapade du IoT Edge-lösning och lagde till kod till CSharpModule som filtrerar ut meddelanden om att temperaturen för den rapporterade datorn ligger under det godkända tröskelvärdet. Nu behöver du bygga lösningen som en containeravbildning och push-överföra den till ditt containerregister. 

1. Logga in på Docker genom att ange följande kommando i den integrerade terminalen för Visual Studio Code så att du kan push-överföra modulavbildningen till ACR: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Använd användarnamnet, lösenordet och inloggningsservern som du kopierade från Azure Container Registry i det första avsnittet. Eller hämta dem igen från avsnittet **Åtkomstnycklar** i ditt register i Azure Portal.

2. I VS Code-utforskaren öppnar du filen **deployment.template.json** i arbetsytan för IoT Edge-lösningen. Den här filen instruerar `$edgeAgent` att distribuera två moduler: **tempSensor** och **CSharpModule**. `CSharpModule.image`-värdet är inställt på en Linux amd64-version för avbildningen. Läs mer om distributionsmanifest i avsnittet om att [förstå hur IoT Edge-moduler kan användas, konfigureras och återanvändas](module-composition.md).

3. I filen **deployment.template.json** finns det ett avsnitt som heter **registryCredentials** och som lagrar dina autentiseringsuppgifter för Docker-registret. De faktiska paren för användarnamn och lösenord lagras i env-filen, som git ignorerar.

4. Lägg till CSharpModule-modultvillingen till distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet `moduleContent` efter `$edgeHub`-modultvillingen: 
    ```json
        "CSharpModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Spara filen.
5. Högerklicka på filen **deployment.template.json** och välj **Skapa IoT Edge-lösning** i VS Code-utforskaren. 

När du ger Visual Studio Code kommando att skapa din lösning hämtar den först information i distributionsmallen och genererar en `deployment.json`-fil i en ny **config**-mapp. Sedan körs två kommandon i en integrerad terminal: `docker build` och `docker push`. Dessa två kommandon skapar koden, lägger `CSharpModule.dll` i behållare och push-överför till det containerregister som du angav när du initierade lösningen. 

Den fullständiga adressen med tagg för behållaravbildningen finns i den integrerade VS Code-terminalen. Avbildningsadressen skapas utifrån information i filen `module.json` med formatet **\<lagringsplats\>:\<version\>-\<plattform\>**. För den här självstudien ska den se ut så här: **registryname.azurecr.io/csharpmodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Distribuera och kör lösningen

1. Konfigurera Azure IoT Toolkit-tillägget med anslutningssträngen för din IoT-hubb: 
    1. Öppna VS Code-utforskaren genom att välja **Visa** > **Utforskaren**. 
    2. I utforskaren klickar du på **AZURE IOT HUB-ENHETER** och sedan på **...**. Klicka på **Select IoT Hub** (Välj IoT Hub). Följ instruktionerna för att logga in på Azure-kontot och välja din IoT-hubb. 
       Observera att du även kan konfigurera genom att klicka på **Set IoT Hub Connection String** (Ange anslutningssträng för IoT-hubb). Ange anslutningssträngen för den IoT-hubb som din IoT Edge-enhet ansluter till i popup-fönstret.

2. I Azure IoT Hub Devices-utforskaren högerklickar du på din IoT Edge-enhet och klickar sedan på **Create Deployment for IoT Edge device** (Skapa distribution för IoT Edge-enhet). Välj filen **deployment.json** i mappen **config** och klicka sedan på **Select Edge Deployment Manifest** (Välj distributionsmanifest för Edge).

3. Klicka på uppdateringsknappen. Du bör se den nya **CSharpModule** köras tillsammans med **TempSensor**-modulen och **$edgeAgent** och **$edgeHub**. 

## <a name="view-generated-data"></a>Visa genererade data

1. Om du vill övervaka data som inkommer till IoT Hub klickar du på **...** och väljer **Starta övervakning av D2C-meddelanden**.
2. Om du vill övervaka D2C-meddelandet för en specifik enhet högerklickar du på enheten i listan och väljer **Starta övervakning av D2C-meddelanden**.
3. Om du vill stoppa dataövervakningen kör du kommandot **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: Sluta övervaka D2C-meddelande) i kommandopaletten. 
4. Om du vill visa eller uppdatera modultvillingen högerklickar du på modulen i listan och väljer **Redigera modultvilling**. Om du vill uppdatera modultvillingen sparar du tvilling-JSON-filen och högerklickar på redigeringsområdet och väljer **Uppdatera modultvilling**.
5. Om du vill visa Docker-loggar kan du installera [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) för VS Code och se moduler som körs lokalt i Docker-utforskaren. I kontextmenyn klickar du på **Visa loggar** för att visa i den integrerade terminalen.
 
## <a name="clean-up-resources"></a>Rensa resurser 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Om du fortsätter till nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du redan har skapat och återanvända dem.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som skapats i den här artikeln om du vill undvika kostnader. 

> [!IMPORTANT]
> Det går inte att ångra borttagning av Azure-resurser och resursgrupper. När resursgruppen och alla resurser som ingår i den tas bort är de borta permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat IoT Hub:en inuti en befintlig resursgrupp som innehåller resurser som du vill behålla, ta bara bort själva IoT Hub-resursen i stället för att ta bort resursgruppen.
>

Om du endast vill ta bort körningen av IoT Hub kan du använda följande kommando med namnet på hubben och resursgruppens namn:

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


Ta bort hela resursgruppen med namnet:

1. Logga in på [Azure Portal](https://portal.azure.com) och klicka på **Resursgrupper**.

2. I textrutan **Filtrera efter namn ...** , skriver du namnet på resursgruppen som innehåller din IoT Hub. 

3. Till höger av din resursgrupp i resultatlistan klickar du på **...** och därefter **Ta bort resursgrupp**.

4. Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på din resursgrupp igen för att bekräfta och klicka sedan på **Ta bort**. Efter en liten stund tas resursgruppen och resurser som finns i den bort.



## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att skapa egna moduler kan du läsa mer om hur du [utvecklar en C#-modul med Azure IoT Edge för Visual Studio Code](how-to-develop-csharp-module.md). Du kan fortsätta med någon av följande självstudier om du vill veta mer hur Azure IoT Edge kan hjälpa dig att omvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Lagra data på gränsen med SQL Server-databaser](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
