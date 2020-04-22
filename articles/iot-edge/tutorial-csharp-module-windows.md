---
title: Självstudiekurs - Utveckla C#-modulen för Windows med Azure IoT Edge
description: Den här självstudien visar hur du skapar en IoT Edge-modul med C#-kod och distribuerar den till en Windows IoT Edge-enhet.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom:
- mvc
- amqp
ms.openlocfilehash: b71db71ac61e0dcd65a2546b2164610e618dab18
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733499"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Självstudiekurs: Utveckla en C# IoT Edge-modul för Windows-enheter

Använd Visual Studio för att utveckla C#-kod och distribuera den till en Windows-enhet som kör Azure IoT Edge.

Du kan använda Azure IoT Edge-moduler till att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Använd Visual Studio för att skapa en IoT Edge-modul som baseras på C# SDK.
> * Använd Visual Studio och Docker för att skapa en Docker-avbildning och publicera den i registret.
> * distribuera modulen till din IoT Edge-enhet
> * visa genererade data.

IoT Edge-modulen du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys vid kanten är användbar när du vill minska mängden data som skickas till och lagras i molnet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Lösningsscope

Den här självstudien visar hur du utvecklar en modul i **C#** med **Visual Studio 2019** och distribuerar den till en **Windows-enhet**. Om du utvecklar moduler för Linux-enheter går du till [Utveckla en C# IoT Edge-modul för Linux-enheter](tutorial-csharp-module.md) istället.

Använd följande tabell för att förstå dina alternativ för att utveckla och distribuera C#-moduler till Windows-enheter:

| C# | Visual Studio-koden | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64 utveckla** | ![Utveckla C#-moduler för WinAMD64 i VS-kod](./media/tutorial-c-module/green-check.png) | ![Utveckla C#-moduler för WinAMD64 i Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Felsöka i Windows AMD64** |   | ![Felsök C#-moduler för WinAMD64 i Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Krav

Innan du påbörjar den här självstudien bör du ha gått igenom den tidigare självstudien för att konfigurera utvecklingsmiljön, [Utveckla en IoT Edge-modul för en Windows-enhet](tutorial-develop-for-windows.md). När du har slutfört den självstudien bör du redan ha följande förutsättningar:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En [Windows-enhet som kör Azure IoT Edge](quickstart.md).
* Ett behållarregister, till exempel [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) konfigurerad med tillägget [Azure IoT Edge Tools.](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) konfigurerad för att köra Windows-behållare.

> [!TIP]
> Om du använder Visual Studio 2017 (version 15.7 eller senare) kan du hämta och installera [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) för VS 2017 från Visual Studio-marknadsplatsen

## <a name="create-a-module-project"></a>Skapa ett modulprojekt

Följande steg skapar ett IoT Edge-modulprojekt med hjälp av Visual Studio och Azure IoT Edge Tools-tillägget. När du har skapat en projektmall lägger du till ny kod så att modulen filtrerar bort meddelanden baserat på deras rapporterade egenskaper.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Azure IoT Edge Tools tillhandahåller projektmallar för alla IoT Edge-modulspråk som stöds i Visual Studio. Dessa mallar har alla filer och kod som du behöver för att distribuera en fungerande modul för att testa IoT Edge, eller ge dig en utgångspunkt för att anpassa mallen med din egen affärslogik.

1. Starta Visual Studio 2019 och välj **Skapa nytt projekt**.

2. Sök efter **IoT Edge** och välj **Azure IoT Edge (Windows amd64)** projektet. Klicka på **Nästa**.

   ![Skapa ett nytt Azure IoT Edge-projekt](./media/tutorial-csharp-module-windows/new-project.png)

3. Byt namn på projektet och lösningen till något beskrivande som **CSharpTutorialApp**. Klicka på **Skapa** om du vill skapa projektet.

   ![Konfigurera ett nytt Azure IoT Edge-projekt](./media/tutorial-csharp-module-windows/configure-project.png)

4. Konfigurera projektet med följande värden:

   | Field | Värde |
   | ----- | ----- |
   | Välj en mall | Välj **C#-modul**. |
   | Namn på modulprojekt | Ge modulen namnet **CSharpModule**. |
   | Databas för Docker-bild | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Behållaravbildningen är förifylld från modulens projektnamnsvärde. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. <br><br> Den slutliga avbildningslagringsplatsen ser ut så här: \<registry name\>.azurecr.io/csharpmodule. |

   ![Konfigurera projektet för målenhet, modultyp och behållarregister](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Välj **Lägg till** om du vill skapa projektet.

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Distributionsmanifestet delar autentiseringsuppgifterna för behållarregistret med IoT Edge-körningen. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten. Använd autentiseringsuppgifterna från avsnittet **Access-nycklar** i ditt Azure-behållarregister.

1. Öppna **filen deployment.template.json** i Utforskaren för Visual Studio-lösningen.

2. Leta reda på egenskapen **Credentials** i de $edgeAgent önskade egenskaperna. Den bör ha registeradressen automatiskt ifylld från den information du angav när du skapade projektet, och sedan ska användarnamns- och lösenordsfält innehålla variabelnamn. Ett exempel:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Öppna **.env-filen** i modullösningen. (Den är dold som standard i Lösningsutforskaren, så du kan behöva välja knappen **Visa alla filer** för att visa den.) .env-filen ska innehålla samma användarnamns- och lösenordsvariabler som du såg i filen deployment.template.json.

4. Lägg till **värdena för användarnamn** och **lösenord** från ditt Azure-behållarregister.

5. Spara ändringarna i .env-filen.

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

Standardmodulkoden tar emot meddelanden i en indatakö och skickar dem vidare via en utdatakö. Nu ska vi lägga till ytterligare kod så att modulen bearbetar meddelandena vid kanten innan du vidarebefordrar dem till IoT Hub. Uppdatera modulen så att den analyserar temperaturdata i varje meddelande och skickar bara meddelandet till IoT Hub om temperaturen överskrider ett visst tröskelvärde.

1. Öppna **CSharpModule** > **Program.cs i**Visual Studio .

2. Överst i **CSharpModule**-namnrymden läger du till tre **using**-instruktioner för typer som ska användas senare:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Lägg till **temperatureThreshold** variabeln i **klassen Program** efter räknarvariabeln. TemperaturVärderets variabel anger det värde som den uppmätta temperaturen måste överstiga för de data som ska skickas till IoT-hubben.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Lägg till klasserna **MessageBody**, **Machine**och **Ambient** i klassen **Program** efter variabeldeklarationerna. Dessa klasser definierar det förväntade schemat för brödtexten i inkommande meddelanden.

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

5. Hitta **Init-metoden.** Den här metoden skapar och konfigurerar ett **ModuleClient-objekt,** vilket gör att modulen kan ansluta till den lokala Azure IoT Edge-körningen för att skicka och ta emot meddelanden. Koden registrerar också en motringning för att ta emot meddelanden från en IoT Edge-hubb via **endpointen input1.**

   Ersätt hela Init-metoden med följande kod:

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   Den här uppdaterade Init-metoden ställer fortfarande in anslutningen till IoT Edge-körningen med ModuleClient, men lägger också till nya funktioner. Den läser modultvillingens önskade egenskaper för att hämta **temperatureThreshold-värdet.** Sedan skapas en motringning som lyssnar efter framtida uppdateringar av modultvillingens önskade egenskaper. Med den här motringningen kan du uppdatera temperaturtröskeln i modultvillingen på distans, och ändringarna kommer att införlivas i modulen.

   Den uppdaterade Init-metoden ändrar också den befintliga **Metoden SetInputMessageHandlerAsync.** I exempelkoden bearbetas inkommande meddelanden på *indata1* med funktionen *PipeMessage,* men vi vill ändra den för att använda funktionen *FilterMessages* som vi ska skapa i följande steg.

6. Lägg till en ny **onDesiredPropertiesUpdate-metod** i **klassen Program.** Metoden tar emot uppdateringar av önskade egenskaper från modultvillingen och uppdaterar variabeln **temperatureThreshold** som ska matchas. Alla moduler har en egen modultvilling, vilket innebär att du kan konfigurera den kod som körs i en modul direkt från molnet.

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

7. Ta bort **exempelmetoden PipeMessage** och ersätt den med en ny **FilterMessages-metod.** Den här metoden anropas när modulen tar emot ett meddelande från IoT Edge-hubben. Den filtrerar ut meddelanden som rapporterar temperaturer under temperaturtröskelvärdet som angetts via modultvillingen. Den lägger också till egenskapen **MessageType** i meddelandet med värdet angivet som **Avisering**.

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
                using(var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
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

9. Öppna **filen deployment.template.json** i IoT Edge-lösningen. Den här filen talar om för IoT Edge-agenten vilka moduler som ska distribueras, i det här fallet **SimulatedTemperatureSensor** och **CSharpModule**, och talar om för IoT Edge-hubben hur meddelanden ska dirigeras mellan dem.

10. Lägg till **CSharpModule**-modultvillingen till distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet **modulesContent** efter **$edgeHub**-modultvillingen:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Lägga till modultvilling till distributionsmall](./media/tutorial-csharp-module-windows/module-twin.png)

11. Spara filen deployment.template.json.

## <a name="build-and-push-your-module"></a>Skapa och tryck på din modul

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod i **CSharpModule** som filtrerar bort meddelanden där den rapporterade maskintemperaturen ligger under det godkända tröskelvärdet. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

1. Använd följande kommando för att logga in på Docker på utvecklingsmaskinen. Använd användarnamn, lösenord och inloggningsserver från ditt Azure-behållarregister. Du kan hämta dessa värden från avsnittet **Access-nycklar** i registret i Azure-portalen.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du kan få en säkerhetsvarning `--password-stdin`som rekommenderar användning av . Även om bästa praxis rekommenderas för produktionsscenarier, ligger den utanför den här självstudiens omfattning. Mer information finns i [inloggningsreferensen för docker.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

2. Högerklicka på det projektnamn som du vill skapa i Utforskaren för Visual Studio-lösningen. Standardnamnet är **AzureIotEdgeApp1** och eftersom du skapar en Windows-modul ska tillägget vara **Windows.Amd64**.

3. Välj **Bygg och tryck på IoT Edge-moduler**.

   Kommandot Bygg och push startar tre åtgärder. Först skapas en ny mapp i lösningen som kallas **config** som innehåller hela distributionsmanifestet, inbyggt av information i distributionsmallen och andra lösningsfiler. För det `docker build` andra körs den för att skapa behållaravbildningen baserat på lämplig dockerfile för din målarkitektur. Sedan körs `docker push` det för att skicka avbildningsdatabasen till behållarregistret.

## <a name="deploy-modules-to-device"></a>Distribuera moduler till enheten

Använd Visual Studio-molnutforskaren och Azure IoT Edge Tools-tillägget för att distribuera modulprojektet till din IoT Edge-enhet. Du har redan ett distributionsmanifest förberett för ditt scenario, **filen deployment.json** i konfigurationsmappen. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

Kontrollera att din IoT Edge-enhet är igång.

1. Expandera resurserna i Visual Studio-molnutforskaren för att se listan över IoT-enheter.

2. Högerklicka på namnet på den IoT Edge-enhet som du vill ta emot distributionen.

3. Välj **Skapa distribution**.

4. I utforskaren väljer du filen **deployment.windows-amd64** i konfigurationsmappen för din lösning.

5. Uppdatera molnutforskaren för att se de distribuerade modulerna som anges under enheten.

## <a name="view-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas.

Du kan använda tillägget IoT Edge Tools för att visa meddelanden när de anländer till din IoT Hub.

1. I Visual Studio-molnutforskaren väljer du namnet på din IoT Edge-enhet.

2. Välj Starta övervakning **inbyggd händelseslutpunkt i**listan **Åtgärder** .

3. Visa meddelanden som kommer till din IoT Hub. Det kan ta ett tag innan meddelandena anländer, eftersom de ändringar vi gjorde i CSharpModule-koden väntar tills maskintemperaturen når 25 grader innan meddelanden skickas. Meddelandet läggs också till **i** alla meddelanden som når det temperaturtröskeln.

   ![Visa meddelanden som anländer till IoT Hub](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Redigera modultvillingen

Vi använde CSharpModule modultvillingen för att ställa in temperaturtröskeln till 25 grader. Du kan använda modultvillingen för att ändra funktionaliteten utan att behöva uppdatera modulkoden.

1. Öppna filen **deployment.windows-amd64.json** i Visual Studio. (Inte filen deployment.template. Om distributionsmanifestet inte visas i konfigurationsfilen i lösningsutforskaren markerar du ikonen **Visa alla filer** i explorer-verktygsfältet.)

2. Hitta twinn CSharpModule och ändra värdet på **parametern temperatureThreshold** till en ny temperatur 5 grader till 10 grader högre än den senast rapporterade temperaturen.

3. Spara filen **deployment.windows-amd64.json.**

4. Följ distributionsstegen igen för att tillämpa det uppdaterade distributionsmanifestet på enheten.

5. Övervaka inkommande meddelanden från enheten till molnet. Du bör se meddelandena sluta tills den nya temperaturtröskeln har uppnåtts.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och De Azure-resurser som du använde i den här artikeln för att undvika avgifter.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul med kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att bygga egna moduler kan du läsa mer om hur du [utvecklar egna IoT Edge-moduler](module-development.md) eller hur du [utvecklar moduler med Visual Studio](how-to-visual-studio-develop-module.md). Exempel på IoT Edge-moduler, inklusive den simulerade temperaturmodulen, finns i [IoT Edge-modulprover](https://github.com/Azure/iotedge/tree/master/edge-modules).

Du kan fortsätta med nästa självstudier för att lära dig hur Azure IoT Edge kan hjälpa dig att distribuera Azure-molntjänster för att bearbeta och analysera data på gränsen.

> [!div class="nextstepaction"]
> [Funktioner](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
