---
title: Använd Visual Studio-koden för att utveckla en C#-modul med Azure IoT kant | Microsoft Docs
description: Utveckla och distribuera en C#-modulen med Azure IoT gränsen i Visual Studio Code utan att växla kontext.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 48c6cacebdeb7505c8dc2bcaed099c33862589ac
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2018
---
# <a name="use-visual-studio-code-to-develop-a-c-module-with-azure-iot-edge"></a>Använd Visual Studio-koden för att utveckla en C#-modul med Azure IoT kant
Den här artikeln innehåller detaljerade anvisningar för att använda [Visual Studio Code](https://code.visualstudio.com/) som den huvudsakliga utvecklingsverktyg för att utveckla och distribuera dina Azure IoT kant-moduler. 

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du använder en dator eller virtuell dator som kör Windows eller Linux som utvecklingsdatorn. Din IoT-Edge-enhet kan vara en annan fysisk enhet eller så kan du simulera en IoT Edge-enhet på utvecklingsdato.

Innan du börjar den här vägledningen måste du slutföra följande kurser:
- Distribuera Azure IoT kanten på en simulerad enhet i [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) eller [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Utveckla och distribuera en C# IoT kant-modul till den simulerade enheten](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

Här är en checklista som visar vilka objekt som du bör ha när du har slutfört föregående Självstudier:

- [Visual Studio Code](https://code.visualstudio.com/) 
- [Azure IoT kant-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [C# för Visual Studio Code (drivs av OmniSharp)-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 
- [Python 2.7](https://www.python.org/downloads/)
- [IoT-Edge kontrollen skript](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- AzureIoTEdgeModule mall (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- En aktiv IoT-hubb med minst en IoT-enhet

Det är också bra att installera [Docker-stöd för VS kod](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) bättre hantera modulen avbildningar och behållare.

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>Distribuera en Azure IoT kant-modul i VS-kod

### <a name="list-your-iot-hub-devices"></a>Visa en lista med din IoT hub-enheter
Det finns två sätt att lista IoT hub-enheter i VS-kod. Du kan välja antingen sätt att fortsätta.

#### <a name="sign-in-to-your-azure-account-in-vs-code-and-choose-your-iot-hub"></a>Logga in på ditt Azure-konto i VS-kod och välj din IoT-hubb
1. Skriv i paletten kommando (F1 eller Ctrl + Skift + P) och välj **Azure: Logga in**. Välj sedan **Kopiera & Öppna**. Klistra in (Ctrl + V) kod i webbläsaren och välj **Fortsätt**. Logga sedan in med ditt Azure-konto. Du kan se din kontoinformation i statusfältet VS-kod.
2. Skriv i paletten kommando, och välj **IoT: Välj IoT-hubb**. Välj den prenumeration där du skapade din IoT-hubb i föregående självstudiekursen först. Välj IoT-hubb som innehåller den IoT-enheten.

    ![Skärmbild av listan över enheter](./media/how-to-vscode-develop-csharp-module/device-list.png)

#### <a name="set-the-iot-hub-connection-string"></a>Ange anslutningssträngen för IoT-hubb
Skriv i paletten kommando, och välj **IoT: ange anslutningssträngen för IoT-hubb**. Kontrollera att du klistra in den anslutande strängen under princip **iothubowner**. (Du hittar den i principer för delad åtkomst i IoT-hubb i Azure-portalen.)
 
Du kan se i listan över enheter i IoT Hub-enheter Explorer i sidorutan till vänster.

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>Starta IoT kant-runtime och distribuera en modul
Installera och starta Azure IoT kant-körningsmiljön på enheten. Distribuera en simulerad sensor-modul som skickar telemetridata till Azure IoT Hub.
1. I kommandot paletten väljer **kant: installationsprogrammet Edge** och välj IoT-Edge enhets-ID. Du kan också högerklicka IoT kant enhets-ID i **enhetslistan**, och välj **installationsprogrammet Edge**.

    ![Skärmbild av installationsprogrammet Edge runtime](./media/how-to-vscode-develop-csharp-module/setup-edge.png)

2. I paletten kommandot väljer **kant: starta Edge** att starta IoT kant-körning. Du kan se motsvarande utdata i en integrerad terminal.

    ![Skärmbild av starta Edge runtime](./media/how-to-vscode-develop-csharp-module/start-edge.png)

3. Kontrollera IoT kant Körningsstatus i Docker Explorer. Grönt innebär att den körs och IoT kant-körning har startats. Datorn nu simulerar en IoT-enhet.

    ![Skärmbild av Edge Körningsstatus](./media/how-to-vscode-develop-csharp-module/edge-runtime.png)

4. Simulera en sensor som fortsätter att skicka meddelanden till enheten IoT kant. Skriv i paletten kommando, och välj **kant: Generera Edge konfigurationsfilen**. Välj en mapp för att skapa den här filen. Ersätt innehållet i filen som har genererats för deployment.json `<registry>/<image>:<tag>` med `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`, och spara filen.

    ![Skärmbild av sensor modul](./media/how-to-vscode-develop-csharp-module/sensor-module.png)

5. Välj **kant: skapa distribution för gränsenheten**, och välj IoT-Edge enhets-ID för att skapa en ny distribution. Du kan också högerklicka på IoT kant enhets-ID i listan över enheter och välja **skapa distribution för gränsenheten**. 

6. Du bör se din IoT-Edge börja köra i Docker Explorer med simulerade sensor. Högerklicka på behållaren i Docker Explorer. Du kan titta på Docker-loggarna för varje modul. Du kan också visa modullistan i listan över enheter.

    ![Skärmbild av Modullista](./media/how-to-vscode-develop-csharp-module/module-list.png)

7. Högerklicka på ditt IoT kant enhets-ID och du kan övervaka D2C meddelanden i VS-kod.
8. Ange om du vill stoppa IoT kant-runtime och sensor-modulen, och välj **kant: stoppa Edge** i paletten kommando.

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>Utveckla och distribuera en C#-modul i VS-kod
I kursen [utveckla en C#-modul](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module), uppdatera, skapa och publicera modulen avbildningen i VS-kod. Sedan gå till Azure portal för att distribuera C#-modulen. Det här avsnittet beskriver hur du använder VS-kod för att distribuera och övervaka C#-modulen.

### <a name="start-a-local-docker-registry"></a>Starta en lokala Docker-registret
Du kan använda alla Docker-kompatibel registret för den här artikeln. Två populära Docker-registertjänster som finns tillgängliga i molnet är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Det här avsnittet använder en [lokala Docker-registret](https://docs.docker.com/registry/deploying/), som är enklare för att testa under din tidig utvecklingen.
I koden VS **integrerad terminal** (Ctrl + ”), kör följande kommando för att starta en lokala registret:  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> Det här exemplet visar registret konfigurationer som gäller endast för testning. En produktionsklara registret måste skyddas med TLS och helst ska använda en åtkomstkontroll metod. Vi rekommenderar att du använder [Azure Container registret](https://docs.microsoft.com/azure/container-registry/) eller [Docker-hubb](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) att distribuera produktionsklara IoT kant moduler.

### <a name="create-an-iot-edge-module-project"></a>Skapa ett projekt för IoT kant-modul
Följande steg visar hur du skapar en IoT kant-modul som baseras på .NET Core 2.0, med hjälp av Visual Studio Code och Azure IoT kant-tillägget. Om du har slutfört det här avsnittet i föregående självstudierna kan du på ett säkert sätt hoppa över det här avsnittet.
1. I Visual Studio Code väljer **visa** > **integrerad Terminal** att öppna VS koden integrerad terminal.
3. Ange följande kommando för att installera (eller uppdatera) i terminalen integrerad i **AzureIoTEdgeModule** mallen i dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. Skapa ett projekt för den nya modulen. Följande kommando skapar projektmappen **FilterModule**, i den aktuella arbetsmappen:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```
 
3. Välj **filen** > **öppna mappen**.
4. Bläddra till den **FilterModule** mappen och klicka på **Välj mapp** öppna projektet i VS-kod.
5. I VS kod Explorer väljer **Program.cs** att öppna den. Överst i **program.cs**, inkluderar följande namnområden:
   ```csharp
   using Microsoft.Azure.Devices.Shared;
   using System.Collections.Generic;  
   using Newtonsoft.Json;
   ```

6. Lägg till den `temperatureThreshold` variabeln i **programmet** klass. Den här variabeln anger det värde som uppmätta temperaturen får överstiga för data som ska skickas till IoT-hubb. 

   ```csharp
   static int temperatureThreshold { get; set; } = 25;
   ```

7. Lägg till den `MessageBody`, `Machine`, och `Ambient` klasser till den **programmet** klass. Dessa klasser definierar det förväntade schemat för brödtexten för inkommande meddelanden.

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

8. I den **Init** metoden koden skapar och konfigurerar en **DeviceClient** objekt. Det här objektet kan modulen som ska ansluta till den lokala körningsmiljön IoT Edge att skicka och ta emot meddelanden. IoT-Edge runtime tillhandahåller till modulen anslutningssträngen som används i den **Init** metod. När du har skapat den **DeviceClient** objekt koden registrerar ett återanrop för att ta emot meddelanden från kant för IoT-hubben via den **input1** slutpunkt. Ersätt den `SetInputMessageHandlerAsync` metod med en ny och lägga till en `SetDesiredPropertyUpdateCallbackAsync` metod för egenskaper uppdateringar. För att göra den här ändringen kan du ersätta den sista raden i det **Init** metoden med följande kod:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. Lägg till den `onDesiredPropertiesUpdate` metod för att den **programmet** klass. Den här metoden tar emot uppdateringar på egenskaperna från modulen dubbla och uppdaterar den **temperatureThreshold** variabeln för att matcha. Alla moduler som har sina egna modulen dubbla där du kan konfigurera den kod som körs i en modul direkt från molnet.

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

10. Ersätt den `PipeMessage` metod med den `FilterMessages` metoden. Den här metoden anropas när modulen tar emot ett meddelande från IoT-hubb kant. Den filtrerar ut meddelanden som rapporterar temperaturer under temperatur tröskelvärdet som angetts via module dubbla. Det ger också den **MessageType** egenskap i meddelandet med värdet **avisering**. 

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

11. Om du vill skapa projektet, högerklicka på den **FilterModule.csproj** filen i Utforskaren och välj **skapa IoT kant modulen**. Den här processen kompilerar modulen och exporterar den binära filen och dess beroenden till en mapp som används för att skapa en Docker-avbildning. 

    ![Skärmbild av VS kod Explorer](./media/how-to-vscode-develop-csharp-module/build-module.png)

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Skapa en Docker-avbildning och publicera den till ditt register

1. I VS kod Explorer expanderar den **Docker** mapp. Expandera sedan mappen för din behållarplattform, antingen **linux-x64** eller **windows-nano**.
2. Högerklicka på den **Dockerfile** fil och markera **skapa IoT kant modulen Docker bild**. 

    ![Skärmbild av VS kod Explorer](./media/how-to-vscode-develop-csharp-module/build-docker-image.png)

3. I den **Välj mappen** och bläddra till eller ange `./bin/Debug/netcoreapp2.0/publish`. Välj **Välj mapp som EXE_DIR**.
4. I popup-textrutan överst på VS Code-fönstret anger du avbildningens namn. Till exempel: `<your container registry address>/filtermodule:latest`. Om du distribuerar till lokala registret ska `localhost:5000/filtermodule:latest`.
5. Pusha bilden till din Docker-lagringsplats. Använd den **kant: Push-gräns för IoT-modulen Docker bild** kommando och ange bildens URL i popup-textrutan längst upp i fönstret VS-kod. Använd samma bild-URL som du använde i föregående steg. Läs i loggen konsolen för att kontrollera avbildningen har aviserats.

    ![Skärmbild av push-överföra avbildningen Docker](./media/how-to-vscode-develop-csharp-module/push-image.png) ![Skärmbild av konsolen logg](./media/how-to-vscode-develop-csharp-module/pushed-image.png)

### <a name="deploy-your-iot-edge-modules"></a>Distribuera din IoT-Edge-moduler

1. Öppna den `deployment.json` filen och ersätter den **moduler** avsnitt med följande:
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
    "filtermodule": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "localhost:5000/filtermodule:latest",
            "createOptions": ""
        }
    }
    ```

2. Ersätt den **vägar** avsnitt med följande:
    ```json
    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
    ```
   > [!NOTE]
   > Deklarativ regler i körningsmiljön definiera där dessa meddelanden. Du behöver två vägar i den här artikeln. Den första vägen transporterar meddelanden från temperatursensorn till filtermodulen via slutpunkten input1. Det här är den slutpunkt du konfigurerade med FilterMessages-hanteraren. Den andra vägen transporterar meddelanden från filtermodulen till IoT Hub. I den här vägen är överordnad en särskild mål som talar om för IoT-hubb Edge att skicka meddelanden till IoT-hubb.

3. Spara filen.
4. Välj i paletten kommandot **kant: skapa distribution för gränsenheten**. Välj IoT kant enhets-ID att skapa en distribution. Högerklicka enhets-ID i listan över enheter eller och välj **skapa distribution för gränsenheten**.

    ![Skärmbild av skapar distributionsalternativ](./media/how-to-vscode-develop-csharp-module/create-deployment.png)

5. Välj den `deployment.json` du uppdateras. I utdatafönstret visas motsvarande utdata för din distribution.

    ![Skärmbild av utdata](./media/how-to-vscode-develop-csharp-module/deployment-succeeded.png)

6. Starta IoT kant-körning i paletten kommando (Välj **kant: starta Edge**).
7. Du kan se din IoT-Edge runtime börja köra i Docker-Explorer med simulerade sensor och filtermodul.

    ![Skärmbild av Docker Explorer](./media/how-to-vscode-develop-csharp-module/solution-running.png)

8. Högerklicka på ditt IoT kant enhets-ID och du kan övervaka D2C meddelanden i VS-kod.


## <a name="next-steps"></a>Nästa steg

[Felsöka C#-modulen i VS-kod](how-to-vscode-debug-csharp-module.md)
