---
title: Självstudien utveckla C# -modulen för Windows - Azure IoT Edge | Microsoft Docs
description: Den här självstudien visar hur du skapar en IoT Edge-modul med C# kod och distribuera den till en Windows IoT Edge-enhet.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 046398af8678e708784614dfdc231778454ed945
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576602"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Självstudier: Utveckla en C# IoT Edge-modul för Windows-enheter

Använd Visual Studio för att utveckla C# kod och distribuera den till en Windows-enhet som kör Azure IoT Edge. 

Du kan använda Azure IoT Edge-moduler för att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. I den här guiden får du lära dig att:    

> [!div class="checklist"]
> * Använda Visual Studio för att skapa en IoT Edge-modul som baseras på den C# SDK.
> * Använda Visual Studio och Docker för att skapa en Docker-avbildning och publicera den till registret.
> * distribuera modulen till din IoT Edge-enhet
> * visa genererade data.

IoT Edge-modulen du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys vid kanten är användbar när du vill minska mängden data som skickas till och lagras i molnet. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Lösningen omfattning

Den här kursen visar hur du utvecklar en modul i **C#** med **Visual Studio 2017**, och hur du distribuerar det till en **Windows-enhet**. Om du utvecklar moduler för Linux-enheter kan du gå till [utveckla en C# IoT Edge-modul för Linux-enheter](tutorial-csharp-module.md) i stället. 

Använd följande tabell för att förstå dina alternativ för utveckling och distribution C-moduler till Windows-enheter: 

| C# | Visual Studio-koden | Visual Studio 2017 | 
| -- | ------------------ | ------------------ |
| **Utveckla Windows AMD64** | ![Utveckla C# moduler för WinAMD64 i VS Code](./media/tutorial-c-module/green-check.png) | ![Utveckla C# moduler för WinAMD64 i Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Windows AMD64-felsökning** |   | ![Felsöka C# moduler för WinAMD64 i Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du påbörjar den här självstudiekursen ska du har gått igenom den tidigare självstudiekursen för att ställa in din utvecklingsmiljö [utveckla en IoT Edge-modul för en Windows-enhet](tutorial-develop-for-windows.md). När du har slutfört självstudien är bör redan du ha följande krav: 

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En [Windows-enhet som kör Azure IoT Edge](quickstart.md).
* Ett behållarregister, till exempel [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio?view=vs-2017), version 15.7 eller högre, som har konfigurerats med den [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) tillägget.
* [Docker CE](https://docs.docker.com/install/) konfigurerats för att köra Windows-behållare.

## <a name="create-a-module-project"></a>Skapa en modul-projekt

Följande steg kan du skapa ett projekt med IoT Edge-modulen med hjälp av Visual Studio och Azure IoT Edge-Tools-tillägget. När du har skapat en projektmall kan du lägga till ny kod så att modulen filtrerar ut meddelanden baserat på deras rapporterade egenskaper. 

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Verktyg för Azure IoT-tillägget innehåller projektmallar för alla stöds IoT Edge modulen språk i Visual Studio 2017. Dessa mallar kan alla filer och kod som du behöver distribuera en fungerande-modul för att testa IoT Edge, eller ge dig en utgångspunkt för att anpassa mallen med egen affärslogik. 

1. Köra Visual Studio som administratör.

2. Välj **Arkiv** > **Nytt** > **Projekt**. 

3. I fönstret nytt projekt väljer du den **Azure IoT** programprojektet typ och väljer den **Azure IoT Edge** projekt. Byt namn på projektet och lösningen till något beskrivande liknande **CSharpTutorialApp**. Klicka på **OK** för att skapa projektet. 

   ![Skapa ett nytt projekt i Azure IoT Edge](./media/tutorial-csharp-module-windows/new-project.png)

4. Konfigurera ditt projekt i IoT Edge-programmet och modulfönstret, med följande värden: 

   | Fält | Värde |
   | ----- | ----- |
   | Programplattform | Avmarkera **Linux Amd64**, och kontrollera **WindowsAmd64**. |
   | Välj en mall | Välj  **C# modulen**. | 
   | Modulen projektnamn | Ge modulen namnet **CSharpModule**. | 
   | Lagringsplatsen för docker-avbildningen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Behållaravbildningen innehåller redan från modulen Projekt namn-värde. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. <br><br> Den slutliga avbildningslagringsplatsen ser ut så här: \<registry name\>.azurecr.io/csharpmodule. |

   ![Konfigurera ditt projekt för målenhet och Modultyp behållarregister](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Välj **OK** att tillämpa ändringarna. 

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Distribution av manifestet delar autentiseringsuppgifterna för behållarregistret med IoT Edge-körningen. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten. Använd autentiseringsuppgifter från den **åtkomstnycklar** avsnitt av Azure container registry. 

1. I Visual Studio solution explorer, öppna den **deployment.template.json** fil. 

2. Hitta den **registryCredentials** -egenskapen i $edgeAgent önskade egenskaper. 

3. Uppdatera egenskapen med dina autentiseringsuppgifter efter det här formatet: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }

4. Save the deployment.template.json file. 

### Update the module with custom code

The default module code receives messages on an input queue and passes them along through an output queue. Let's add some additional code so that the module processes the messages at the edge before forwarding them to IoT Hub. Update the module so that it analyzes the temperature data in each message, and only sends the message to IoT Hub if the temperature exceeds a certain threshold. 

1. In Visual Studio, open **CSharpModule** > **Program.cs**.

2. At the top of the **CSharpModule** namespace, add three **using** statements for types that are used later:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Lägg till den **temperatureThreshold** variabeln den **programmet** klass efter räknarvariabeln. Variabeln temperatureThreshold anger det värde som uppmätta temperaturen måste överskrida innan data skickas till IoT hub. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Lägg till den **MessageBody**, **datorn**, och **Ambient** klasserna till i **programmet** klass efter variabeldeklarationer. Dessa klasser definierar det förväntade schemat för brödtexten i inkommande meddelanden.

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

5. Hitta den **Init** metod. Den här metoden skapar och konfigurerar en **ModuleClient** objektet, vilket innebär att modulen kan ansluta till den lokala Azure IoT Edge-körningen för att skicka och ta emot meddelanden. Koden registrerar också ett återanrop för att ta emot meddelanden från en IoT Edge hub via den **indata1** slutpunkt.

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
   
   Den här uppdaterade Init-metoden fortfarande ställer in anslutningen till IoT Edge-körningen med ModuleClient, men medför också nya funktioner. Det läser modulen twin's önskade egenskaper för att hämta den **temperatureThreshold** värde. Sedan skapar den ett återanrop som lyssnar efter eventuella framtida uppdateringar till önskade egenskaper för modultvillingen. Du kan uppdatera temperatur tröskelvärdet i modultvillingen via en fjärranslutning med det här återanrop och ändringarna kommer införlivas i modulen. 

   Uppdaterade Init-metoden ändras också det befintliga **SetInputMessageHandlerAsync** metod. I exempelkoden, inkommande meddelanden på *indata1* bearbetas med den *PipeMessage* funktion, men vi vill ändra som ska användas för den *FilterMessages* fungera som Vi skapar i följande steg. 

6. Lägga till en ny **onDesiredPropertiesUpdate** metod för att den **programmet** klass. Metoden tar emot uppdateringar av önskade egenskaper från modultvillingen och uppdaterar variabeln **temperatureThreshold** som ska matchas. Alla moduler har en egen modultvilling, vilket innebär att du kan konfigurera den kod som körs i en modul direkt från molnet.

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

7. Ta bort exemplet **PipeMessage** metod och Ersätt den med en ny **FilterMessages** metod. Den här metoden anropas när modulen tar emot ett meddelande från IoT Edge-hubben. Den filtrerar ut meddelanden som rapporterar temperaturer under temperaturtröskelvärdet som angetts via modultvillingen. Den lägger också till egenskapen **MessageType** i meddelandet med värdet angivet som **Avisering**. 

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

9. Öppna den **deployment.template.json** fil i din IoT Edge-lösning. Den här filen talar om för IoT Edge-agenten vilka moduler som ska distribueras, i detta fall **tempSensor** och **CSharpModule**, och talar om för IoT Edge-hubben hur meddelanden ska dirigeras mellan dem.

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


## <a name="build-and-push-your-module"></a>Skapa och skicka din modul

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod i **CSharpModule** som filtrerar bort meddelanden där den rapporterade maskintemperaturen ligger under det godkända tröskelvärdet. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister. 

1. Använd följande kommando för att logga in på Docker på din utvecklingsdator. Använd användarnamn, lösenord och inloggningsserver från Azure container registry. Du kan hämta dessa värden från den **åtkomstnycklar** avsnitt på ditt register i Azure-portalen.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du får en säkerhetsvarning som rekommenderar användning av `--password-stdin`. Även om den bästa metoden rekommenderas för produktionsscenarier, ligger utanför omfånget för den här självstudien. Mer information finns i den [docker-inloggning](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referens.

2. I Visual Studio solution explorer, högerklicka på projektnamnet som du vill skapa. Standardnamnet är **AzureIotEdgeApp1** och eftersom du skapar en Windows-modul, tillägget bör vara **Windows.Amd64**. 

3. Välj **bygga och Push IoT Edge-moduler**. 

   Versions- och push-kommando startar tre åtgärder. Först skapar den en ny mapp i lösningen kallas **config** som innehåller fullständig distribution manifest, byggas ut information i distributionsmallen och andra lösningsfiler. Dessutom körs `docker build` att skapa behållaravbildningen baserat på dockerfile som är lämpliga för din mål-arkitektur. Sedan körs `docker push` att skicka lagringsplatsen för avbildningen till behållarregistret. 

## <a name="deploy-modules-to-device"></a>Distribuera moduler till enhet

Använda Visual Studio cloud explorer och verktyg för Azure IoT Edge-tillägget för att distribuera projektet modulen till din IoT Edge-enhet. Du redan har ett manifest för distribution som förberedd för ditt scenario, den **deployment.json** fil i config-mappen. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

Se till att din IoT Edge-enhet är igång. 

1. I Visual Studio cloud explorer, expandera resurser för att se din lista över IoT-enheter. 

2. Högerklicka på namnet på IoT Edge-enhet som du vill ta emot distributionen. 

3. Välj **skapa distribution**.

4. I Utforskaren, Välj den **deployment.windows amd64** fil i config-mappen i din lösning. 

5. Uppdatera cloud explorer om du vill se de distribuerade moduler som visas under din enhet. 

## <a name="view-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas. 

Du kan använda tillägget IoT Edge-verktyg för att visa meddelanden när de anländer till din IoT-hubb. 

1. I Visual Studio cloud explorer, väljer du namnet på din IoT Edge-enhet. 

2. I den **åtgärder** väljer **börja övervaka D2C-meddelande**. 

3. Visa meddelanden som inkommer på din IoT-hubb. Det kan ta en stund innan meddelanden tas emot, eftersom vi ändringar i koden CSharpModule vänta tills datorn temperaturen når 25 grader innan du skickar meddelanden. Det ger också meddelandetypen **avisering** att alla meddelanden som når gränsen temperatur. 

   ![Visa meddelanden anländer till IoT Hub](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Redigera modultvillingen

Vi använde modultvilling CSharpModule Ange tröskelvärdet för temperatur vid 25 grader. Du kan använda modultvillingen för att ändra funktionen utan att behöva uppdatera modulen koden.

1. Visual Studio, öppna den **deployment.windows amd64.json** fil. (Inte deployment.template-fil. Om du inte ser distributionen visas i config-filen i solution explorer, Välj den **visa alla filer** ikon i explorer-verktygsfältet.)

2. Hitta den CSharpModule läsningen och ändra värdet för den **temperatureThreshold** parametern till en ny temperatur 5 grader och 10 grader som är högre än den senaste rapporterade temperaturen. 

3. Spara den **deployment.windows amd64.json** fil.

4. Gör distributionen igen om du vill tillämpa det uppdaterade distributionen manifestet till din enhet. 

5. Övervaka inkommande meddelanden för enhet-till-moln. Du bör se meddelanden stoppa tills det nya temperatur-tröskelvärdet har uppnåtts. 

## <a name="clean-up-resources"></a>Rensa resurser 

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet. 

I annat fall kan du ta bort de lokala konfigurationerna och Azure-resurser som du använt i den här artikeln för att undvika avgifter. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul med kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att skapa egna moduler kan du läsa mer om [utveckla dina egna IoT Edge-moduler](module-development.md) eller hur du [utveckla moduler med Visual Studio](how-to-visual-studio-develop-module.md). Du kan fortsätta till nästa självstudierna för att lära dig hur Azure IoT Edge kan hjälpa dig att distribuera Azure-molntjänster för att bearbeta och analysera data på gränsen.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Maskininlärning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
