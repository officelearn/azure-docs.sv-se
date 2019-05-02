---
title: Självstudien utveckla C# -modulen för Linux – Azure IoT Edge | Microsoft Docs
description: Den här självstudien visar hur du skapar en IoT Edge-modul med C# kod och distribuera den till en Linux IoT Edge-enhet.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 55cec146382f0b64a38bf2d954b81051ba109b27
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64876050"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Självstudier: Utveckla en C# IoT Edge-modul för Linux-enheter

Använd Visual Studio Code för att utveckla C# kod och distribuera den till en Linux-enhet som kör Azure IoT Edge. 

Du kan använda Azure IoT Edge-moduler för att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. Du kommer att använda den simulerade IoT Edge-enheten som du skapade i snabbstarterna Distribuera Azure IoT Edge på en simulerad enhet i [Windows](quickstart.md) eller [Linux](quickstart-linux.md). I den här guiden får du lära dig att:    

> [!div class="checklist"]
> * Använd Visual Studio Code för att skapa en IoT Edge-modul som baseras på .NET Core 2.1 SDK.
> * använda Visual Studio Code och Docker till att skapa en Docker-avbildning och publicera den till registret
> * distribuera modulen till din IoT Edge-enhet
> * visa genererade data.

IoT Edge-modulen du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys vid kanten är användbar när du vill minska mängden data som skickas till och lagras i molnet. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Lösningen omfattning

Den här kursen visar hur du utvecklar en modul i **C#** med **Visual Studio Code**, och hur du distribuerar det till en **Linux enheten**. Om du utvecklar moduler för Windows-enheter kan du gå till [utveckla en C# IoT Edge-modul för Windows-enheter](tutorial-csharp-module-windows.md) i stället.

Använd följande tabell för att förstå dina alternativ för utveckling och distribution C-moduler till Linux: 

| C# | Visual Studio-koden | Visual Studio 2017 | 
| -- | ------------------ | ------------------ |
| **Linux AMD64** | ![C#moduler för LinuxAMD64 i VS Code](./media/tutorial-c-module/green-check.png) | ![C#moduler för LinuxAMD64 i Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![C#moduler för LinuxARM32 i VS Code](./media/tutorial-c-module/green-check.png) | ![C#moduler för LinuxARM64 i Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du påbörjar den här självstudiekursen ska du har gått igenom den tidigare självstudiekursen för att ställa in din utvecklingsmiljö [utveckla en IoT Edge-modul för en Linux-enhet](tutorial-develop-for-linux.md). När du har slutfört självstudien är bör redan du ha följande krav: 

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En [Linux-enhet som kör Azure IoT Edge](quickstart-linux.md).
* Ett behållarregister, till exempel [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) konfigurerats med den [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) tillägget.
* [Docker CE](https://docs.docker.com/install/) konfigurerats för att köra Linux-behållare.

För att slutföra de här självstudierna måste du förbereda följande ytterligare krav på utvecklingsdatorn: 

* [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>Skapa en modul-projekt

Följande steg skapar ett projekt med IoT Edge-modul för C# med hjälp av Visual Studio Code och tillägget Azure IoT Tools. När du har skapat en projektmall kan du lägga till ny kod så att modulen filtrerar ut meddelanden baserat på deras rapporterade egenskaper. 

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Skapa en C#-lösningsmall som du kan anpassa med din egen kod. 

1. I Visual Studio Code väljer du **Visa** > **Kommandopalett** för att öppna kommandopaletten i VS Code. 

2. I kommandopaletten skriver och kör du kommandot **Azure: Logga in** och följer anvisningarna för att logga in på ditt Azure-konto. Om du redan är inloggad kan du hoppa över det här steget.

3. Skriv och kör kommandot **Azure IoT Edge på kommandopaletten: New IoT Edge solution** (Ny IoT Edge-lösning). Skapa lösningen genom att följ anvisningarna på kommandopaletten.

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **C#-modul**. |
   | Ange ett modulnamn | Ge modulen namnet **CSharpModule**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen fylls i baserat på det namn du angav i föregående steg. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. <br><br>Den slutliga avbildningslagringsplatsen ser ut så här: \<registry name\>.azurecr.io/csharpmodule. |
 
   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-csharp-module/repository.png)


### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerregistret och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten. Använd autentiseringsuppgifter från den **åtkomstnycklar** avsnitt av Azure container registry. 

1. Öppna **.env**-filen i VS Code-utforskaren. 
2. Uppdatera fälten med den **användarnamn** och **lösenord** värden från Azure container registry. 
3. Spara filen. 

### <a name="select-your-target-architecture"></a>Välj din mål-arkitektur

Visual Studio Code kan för närvarande kan utveckla C-moduler för Linux AMD64- och Linux ARM32v7-enheter. Du måste välja vilken arkitektur som mål med varje lösning eftersom behållaren har skapats och körs på olika sätt för varje arkitekturtyp av. Standardvärdet är Linux AMD64. 

1. Öppna kommandopaletten och Sök efter **Azure IoT Edge: Ange standard målplattform för lösning**, eller klicka på genvägsikonen i Sidopanel längst ned i fönstret. 

2. Välj mål-arkitektur i kommandopaletten, från listan med alternativ. Den här självstudien använder vi en Ubuntu-dator som IoT Edge-enhet, så kommer behåller du standardvärdet **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

1. Öppna **modules** > **CSharpModule** > **Program.cs** i VS Code-utforskaren.

2. Överst i **CSharpModule**-namnrymden läger du till tre **using**-instruktioner för typer som ska användas senare:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Lägg till variabeln **temperatureThreshold** till klassen **Program**. Variabeln anger det värde som den uppmätta temperaturen måste överstiga för att data ska skickas till IoT-hubben. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Lägg till klasserna **MessageBody**, **Machine** och **Ambient** till klassen **Program**. Dessa klasser definierar det förväntade schemat för brödtexten i inkommande meddelanden.

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

5. Hitta den **Init** funktion. Den här funktionen skapar och konfigurerar en **ModuleClient** objektet, vilket innebär att modulen kan ansluta till den lokala Azure IoT Edge-körningen för att skicka och ta emot meddelanden. När du har skapat **ModuleClient** läser koden **temperatureThreshold**-värdet från modultvillingens önskade egenskaper. Koden registrerar ett återanrop för att ta emot meddelanden från IoT Edge-hubben via **input1**-slutpunkten. Ersätt metoden **SetInputMessageHandlerAsync** med en ny och lägg till en **SetDesiredPropertyUpdateCallbackAsync**-metod för uppdateringar av de önskade egenskaperna. Gör den här ändringen genom att ersätta den sista raden i **Init**-metoden med följande kod:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);
    
    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. Lägg till metoden **onDesiredPropertiesUpdate** till klassen **Program**. Metoden tar emot uppdateringar av önskade egenskaper från modultvillingen och uppdaterar variabeln **temperatureThreshold** som ska matchas. Alla moduler har en egen modultvilling, vilket innebär att du kan konfigurera den kod som körs i en modul direkt från molnet.

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

7. Ersätt metoden **PipeMessage** med metoden **FilterMessages**. Den här metoden anropas när modulen tar emot ett meddelande från IoT Edge-hubben. Den filtrerar ut meddelanden som rapporterar temperaturer under temperaturtröskelvärdet som angetts via modultvillingen. Den lägger också till egenskapen **MessageType** i meddelandet med värdet angivet som **Avisering**. 

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

8. Spara filen Program.cs.

9. I VS Code-utforskaren öppnar du filen **deployment.template.json** i arbetsytan för IoT Edge-lösningen. 

10. Lägg till **CSharpModule**-modultvillingen till distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet **modulesContent** efter **$edgeHub**-modultvillingen: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Lägga till modultvilling till distributionsmall](./media/tutorial-csharp-module/module-twin.png)

11. Spara filen deployment.template.json.


## <a name="build-and-push-your-module"></a>Skapa och skicka din modul

I det föregående avsnittet du skapade en IoT Edge-lösning och lagt till kod till CSharpModule som filtrerar ut meddelanden där temperaturen som rapporteras datorn ligger inom godkända gränser. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

1. Öppna den VS Code-integrerade terminalen genom att välja **Visa** > **Terminal**.

1. Logga in på Docker genom att ange följande kommando i terminalen. Logga in med användarnamn, lösenord och inloggningsserver från Azure container registry. Du kan hämta dessa värden från den **åtkomstnycklar** avsnitt på ditt register i Azure-portalen.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du får en säkerhetsvarning som rekommenderar användning av `--password-stdin`. Även om den bästa metoden rekommenderas för produktionsscenarier, ligger utanför omfånget för den här självstudien. Mer information finns i den [docker-inloggning](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referens.

2. I VS Code-utforskaren högerklickar du på filen **deployment.template.json** och väljer **Build and Push IoT Edge solution** (Skapa och skicka IoT Edge-lösning).

   Versions- och push-kommando startar tre åtgärder. Först skapar den en ny mapp i lösningen kallas **config** som innehåller fullständig distribution manifest, byggas ut information i distributionsmallen och andra lösningsfiler. Dessutom körs `docker build` att skapa behållaravbildningen baserat på dockerfile som är lämpliga för din mål-arkitektur. Sedan körs `docker push` att skicka lagringsplatsen för avbildningen till behållarregistret.

## <a name="deploy-and-run-the-solution"></a>Distribuera och kör lösningen

Använd Visual Studio Code-Utforskaren och verktyg för Azure IoT-tillägget för att distribuera projektet modulen till din IoT Edge-enhet. Du redan har ett manifest för distribution som förberedd för ditt scenario, den **deployment.json** fil i config-mappen. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

Se till att din IoT Edge-enhet är igång.

1. I Visual Studio Code-Utforskaren expanderar den **Azure IoT Hub-enheter** avsnitt för att se din lista över IoT-enheter.

2. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Create Deployment for Single Device** (Skapa distribution för en enskild enhet). 

5. Välj filen **deployment.json** i mappen **config** och klicka sedan på **Select Edge Deployment Manifest** (Välj distributionsmanifest för Edge). Använd inte filen deployment.template.json. 

6. Klicka på uppdateringsknappen. Du bör se den nya **CSharpModule** köras tillsammans med **TempSensor**-modulen och **$edgeAgent** och **$edgeHub**.  

## <a name="view-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas.

Du kan visa statusen för din IoT Edge-enhet i avsnittet om **Azure IoT Hub-enheter** i Visual Studio Code-utforskaren. Expandera enhetsinformationen så ser du en lista med moduler som distribueras och körs.

1. I Visual Studio Code-Utforskaren högerklickar du på namnet på din IoT Edge-enhet och välj **börja övervaka D2C meddelanden**.

2. Visa meddelanden som inkommer på din IoT-hubb. Det kan ta en stund innan meddelanden tas emot, eftersom IoT Edge-enheten måste ta emot dess ny distribution och starta alla moduler. Vi ändringar i koden CModule vänta sedan tills datorn temperaturen når 25 grader innan du skickar meddelanden. Det ger också meddelandetypen **avisering** att alla meddelanden som når gränsen temperatur. 

   ![Visa meddelanden anländer till IoT Hub](./media/tutorial-csharp-module/view-d2c-message.png)
 
## <a name="edit-the-module-twin"></a>Redigera modultvillingen

Vi använde modultvilling CSharpModule i manifestet distribution Ange tröskelvärdet för temperatur vid 25 grader. Du kan använda modultvillingen för att ändra funktionen utan att behöva uppdatera modulen koden.

1. Expandera information under din IoT Edge-enhet för att se modulerna som körs i Visual Studio Code. 

2. Högerklicka på **CSharpModule** och välj **redigera modultvilling**. 

3. Hitta **TemperatureThreshold** i önskade egenskaper. Ändra värdet till en ny temperatur 5 grader och 10 grader som är högre än den senaste rapporterade temperaturen. 

4. Spara filen för modulen twin.

5. Högerklicka någonstans i modultvillingen redigera rutan och välj **uppdatering modultvilling**. 

5. Övervaka inkommande meddelanden för enhet-till-moln. Du bör se meddelanden stoppa tills det nya temperatur-tröskelvärdet har uppnåtts. 

## <a name="clean-up-resources"></a>Rensa resurser 

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet. 

I annat fall kan du ta bort de lokala konfigurationerna och Azure-resurser som du använt i den här artikeln för att undvika avgifter. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att skapa egna moduler kan du läsa mer om [utveckla dina egna IoT Edge-moduler](module-development.md) eller hur du [utveckla moduler med Visual Studio Code](how-to-vs-code-develop-module.md). Du kan fortsätta till nästa självstudierna för att lära dig hur Azure IoT Edge kan hjälpa dig att distribuera Azure-molntjänster för att bearbeta och analysera data på gränsen.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Maskininlärning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
