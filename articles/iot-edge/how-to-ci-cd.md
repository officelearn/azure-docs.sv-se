---
title: Azure IoT Edge kontinuerlig integrering och kontinuerlig distribution | Microsoft Docs
description: Översikt över kontinuerlig integrering och kontinuerlig distribution för Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5099ca70503ba2ed4ae8f4969a9199816c4986fb
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302579"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge

Den här artikeln visar hur du kan använda kontinuerlig integrering och kontinuerlig distribution funktionerna i Azure DevOps-tjänsterna och Microsoft Team Foundation Server (TFS) för att bygga, testa och distribuera program snabbt och effektivt till Azure IoT Edge. 

I den här artikeln får du lära dig hur du:
* Skapa och kontrollera i ett exempel på IoT Edge-lösning som innehåller enhet tester.
* Installera Azure IoT Edge-tillägget för Azure DevOps.
* Konfigurera kontinuerlig integrering (CI) för att skapa lösningen och köra enhetstesterna.
* Konfigurera kontinuerlig distribution (CD) för att distribuera lösningen och visa svar.

Det tar 30 minuter att slutföra stegen i den här artikeln.

![CI och CD](./media/how-to-ci-cd/cd.png)

## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Skapa en exempel Azure IoT Edge-lösning som använder Visual Studio Code

I det här avsnittet skapar du ett exempel på IoT Edge lösning som innehåller enhetstester kan du köra som en del av skapandeprocessen. Innan du följer riktlinjerna i det här avsnittet utför du stegen i [utveckla en IoT Edge-lösning med flera moduler i Visual Studio Code](tutorial-multiple-modules-in-vscode.md).

1. I VS Code kommandopalett skriver och kör kommandot **Edge: nya IoT-Edge lösning**. Välj sedan din arbetsytemapp, anger du Lösningsnamnet (standardnamnet är **EdgeSolution**), och skapa en C#-modul (**FilterModule**) som den första användarmodulen i den här lösningen. Du måste också ange lagringsplatsen för Docker-avbildningar för din första modul. Standard avbildningslagringsplatsen baseras på en lokal Docker-register (`localhost:5000/filtermodule`). Du behöver ändra den till Azure Container Registry (`<your container registry address>/filtermodule`) eller Docker-hubb för ytterligare kontinuerlig integrering.

    ![Konfigurera ACR](./media/how-to-ci-cd/acr.png)

2. VS Code-fönstret läser in din arbetsyta för IoT Edge-lösning. Du kan också skriva och kör **Edge: Lägg till IoT Edge-modul** att lägga till flera moduler. Det finns en `modules` mapp, ett `.vscode` mappen och ett manifest mallfilen distribution i rotmappen. Alla användare modulen koder kommer att undermappar i mappen `modules`. Den `deployment.template.json` är manifestet Distributionsmall. Några av parametrarna i den här filen kommer att tolkas från den `module.json`, som finns i varje modul-mapp.

3. Din lösning för IoT Edge-exemplet är nu klar. Standard C# modulen fungerar som en modul för pipe-meddelande. I den `deployment.template.json`, visas den här lösningen innehåller två moduler. Meddelandet kommer att genereras från den `tempSensor` -modulen och kommer skickas direkt `FilterModule`, skickas sedan till din IoT-hubb. Ersätt hela **Program.cs** filen med nedan innehåll. Mer information om det här kodfragmentet kan du referera till [skapa en IoT Edge C#-modulen projekt](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module#create-an-iot-edge-module-project).

    ```csharp
    namespace FilterModule
    {
        using System;
        using System.IO;
        using System.Runtime.InteropServices;
        using System.Runtime.Loader;
        using System.Security.Cryptography.X509Certificates;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using System.Collections.Generic;     // for KeyValuePair<>
        using Microsoft.Azure.Devices.Shared; // for TwinCollection
        using Newtonsoft.Json;                // for JsonConvert

        public class MessageBody
        {
            public Machine machine { get; set; }
            public Ambient ambient { get; set; }
            public string timeCreated { get; set; }
        }
        public class Machine
        {
            public double temperature { get; set; }
            public double pressure { get; set; }
        }
        public class Ambient
        {
            public double temperature { get; set; }
            public int humidity { get; set; }
        }

        public class Program
        {
            static int counter;
            static int temperatureThreshold { get; set; } = 25;

            static void Main(string[] args)
            {
                Init().Wait();

                // Wait until the app unloads or is cancelled
                var cts = new CancellationTokenSource();
                AssemblyLoadContext.Default.Unloading += (ctx) => cts.Cancel();
                Console.CancelKeyPress += (sender, cpe) => cts.Cancel();
                WhenCancelled(cts.Token).Wait();
            }

            /// <summary>
            /// Handles cleanup operations when app is cancelled or unloads
            /// </summary>
            public static Task WhenCancelled(CancellationToken cancellationToken)
            {
                var tcs = new TaskCompletionSource<bool>();
                cancellationToken.Register(s => ((TaskCompletionSource<bool>)s).SetResult(true), tcs);
                return tcs.Task;
            }

            /// <summary>
            /// Initializes the ModuleClient and sets up the callback to receive
            /// messages containing temperature information
            /// </summary>
            static async Task Init()
            {
                MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
                ITransportSettings[] settings = { mqttSetting };

                // Open a connection to the Edge runtime
                ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
                await ioTHubModuleClient.OpenAsync();
                Console.WriteLine("IoT Hub module client initialized.");

                // Register callback to be called when a message is received by the module
                await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessage, ioTHubModuleClient);
            }

            /// <summary>
            /// This method is called whenever the module is sent a message from the EdgeHub. 
            /// It just pipe the messages without any change.
            /// It prints all the incoming messages.
            /// </summary>
            static async Task<MessageResponse> FilterMessage(Message message, object userContext)
            {
                int counterValue = Interlocked.Increment(ref counter);

                var moduleClient = userContext as ModuleClient;
                if (moduleClient == null)
                {
                    throw new InvalidOperationException("UserContext doesn't contain " + "expected values");
                }

                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                var filteredMessage = filter(message);

                if (filteredMessage != null && !string.IsNullOrEmpty(messageString))
                {
                    var pipeMessage = new Message(messageBytes);
                    foreach (var prop in message.Properties)
                    {
                        pipeMessage.Properties.Add(prop.Key, prop.Value);
                    }
                    await moduleClient.SendEventAsync("output1", pipeMessage);
                    Console.WriteLine("Received message sent");
                }
                return MessageResponse.Completed;
            }

            public static Message filter(Message message)
            {
                var counterValue = Interlocked.Increment(ref counter);

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
                    return filteredMessage;
                }
                return null;
            }
        }
    }
    ```

4. Skapa en .net Core enhet test-projektet. Skapa en ny mapp i VS Code-Utforskaren, **tests\FilterModuleTest** i din arbetsyta. I VS Code-integrerade terminalen (**Ctrl + '**), kör följande kommandon för att skapa ett testprojekt för xunit och Lägg till referens till den **FilterModule** projekt.

    ```cmd
    cd tests\FilterModuleTest
    dotnet new xunit
    dotnet add reference ../../modules/FilterModule/FilterModule.csproj
    ```

    ![Mappstruktur](./media/how-to-ci-cd/add-test-project.png)

5. I den **FilterModuleTest** mappen uppdatera namnet på **UnitTest1.cs** till **FilterModuleTest.cs**. Välj och öppna **FilterModuleTest.cs**, Ersätt hela koden med under kodfragment som innehåller enhetstester mot FilterModule-projektet.

    ```csharp
    using Xunit;
    using FilterModule;
    using Newtonsoft.Json;
    using System;
    using System.IO;
    using System.Runtime.InteropServices;
    using System.Runtime.Loader;
    using System.Security.Cryptography.X509Certificates;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;

    namespace FilterModuleTest
    {
        public class FilterModuleTest
        {
            [Fact]
            public void filterLessThanThresholdTest()
            {
                var source = createMessage(25 - 1);
                var result = Program.filter(source);
                Assert.True(result == null);
            }

            [Fact]
            public void filterMoreThanThresholdAlertPropertyTest()
            {
                var source = createMessage(25 + 1);
                var result = Program.filter(source);
                Assert.True(result.Properties["MessageType"] == "Alert");
            }

            [Fact]
            public void filterMoreThanThresholdCopyPropertyTest()
            {
                var source = createMessage(25 + 1);
                source.Properties.Add("customTestKey", "customTestValue");
                var result = Program.filter(source);
                Assert.True(result.Properties["customTestKey"] == "customTestValue");
            }

            private Message createMessage(int temperature)
            {
                var messageBody = createMessageBody(temperature);
                var messageString = JsonConvert.SerializeObject(messageBody);
                var messageBytes = Encoding.UTF8.GetBytes(messageString);
                return new Message(messageBytes);
            }

            private MessageBody createMessageBody(int temperature)
            {
                var messageBody = new MessageBody
                {
                    machine = new Machine
                    {
                        temperature = temperature,
                        pressure = 0
                    },
                    ambient = new Ambient
                    {
                        temperature = 0,
                        humidity = 0
                    },
                    timeCreated = string.Format("{0:O}", DateTime.Now)
                };

                return messageBody;
            }
        }
    }
    ```

6. Du kan ange följande kommandon för att köra enhetstester lokalt i integrerade terminalen. 
    ```cmd
    dotnet test
    ```

    ![Test jednotky](./media/how-to-ci-cd/unit-test.png)

7. Spara dessa projekt och checkar in i din Azure DevOps eller TFS-lagringsplats.
    

> [!NOTE]
> Mer information om hur du använder Azure-databaser finns i [dela din kod med Visual Studio och Azure-lagringsplatser](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).


## <a name="configure-continuous-integration"></a>Konfigurera kontinuerlig integrering
I det här avsnittet skapar du en build-pipeline som är konfigurerad för att köras automatiskt när du checkar in ändringar till exempel IoT Edge-lösningen och enhetstester som den innehåller kör automatiskt.

1. Logga in på din Azure DevOps-organisation (**https://**_ditt konto_**. visualstudio.com**) och öppna projektet där du har markerat i exempelappen.

    ![Checka in kod](./media/how-to-ci-cd/init-project.png)

1. Besök [Azure IoT Edge för Azure DevOps](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) på Azure Marketplace för DevOps. Klicka på **helt kostnadsfritt** och Följ guiden för att installera det här tillägget till din Azure DevOps-organisation eller ladda ned till din TFS.

    ![Installera tillägget](./media/how-to-ci-cd/install-extension.png)

1. I din Azure DevOps, öppnar den **skapa &amp; versionen** hub och i den **bygger** fliken **+ ny pipeline**. Eller, om du redan har skapandet av pipelines, välja den **+ ny** knappen. 

    ![Ny version](./media/how-to-ci-cd/add-new-build.png)

1. Om det händer väljer den **Azure DevOps Git** datakällans typ, välj sedan projektet, lagringsplatsen och grenen som var koden finns. Välj **fortsätta**.

    ![Välj Azure DevOps-git](./media/how-to-ci-cd/select-vsts-git.png)

1. I **Välj en mall** fönstret Välj **börja med en tom process**.

    ![Tom från början](./media/how-to-ci-cd/start-with-empty.png)

1. Klicka på **+** på höger sida av **fas 1** att lägga till en uppgift i fasen. Sök sedan och välj **.Net Core**, och klicka på **Lägg till** att lägga till den här uppgiften till fasen.

    ![DotNet-test](./media/how-to-ci-cd/add-dot-net-core.png)

1. Uppdatera den **visningsnamn** till **dotnet test**, och i den **kommandot** listrutan, väljer **testa**. Lägg till nedan sökvägen till den **sökvägen till projektet**.

    ```
    tests/FilterModuleTest/*.csproj
    ```

    ![Konfigurera dotnet-test](./media/how-to-ci-cd/dotnet-test.png)

1. Klicka på **+** på höger sida av **fas 1** att lägga till en uppgift i fasen. Sök sedan och välj **Azure IoT Edge**, och klicka på **Lägg till** knappen **två gånger** att lägga till dessa aktiviteter till fasen.

    ![IoT Edge](./media/how-to-ci-cd/add-azure-iot-edge.png)

1. I den första aktiviteten i Azure IoT Edge, uppdaterar den **visningsnamn** till **modulen bygga och Push**, och i den **åtgärd** listrutan, väljer **bygga och Push-**. I den **Module.json filen** textrutan lägga till på sökvägen till den. Välj sedan **behållare registertyp**, se till att du konfigurerar och välj samma registret i din kod. Den här uppgiften ska skapa och skicka alla moduler i lösningen och publicera till behållarregistret som du har angett. Om dina moduler ska skickas till olika register, du har flera **modulen bygga och Push** uppgifter.

    ```
    **/module.json
    ```

    ![Modulen versions- och Push](./media/how-to-ci-cd/module-build-push.png)

1. I den andra aktiviteten i Azure IoT Edge, uppdaterar den **visningsnamn** till **distribuera till IoT Edge-enhet**, och i den **åtgärd** listrutan, väljer **distribuera till IoT Edge enheten**. Välj din Azure-prenumeration och ange namnet på din IoT-hubb. Du kan ange ett ID för IoT Edge-distribution och distributionsprioritet. Du kan också välja att distribuera till en eller flera enheter. Om du distribuerar till flera enheter, måste du ange Målvillkor enheten. Om du vill använda taggar för enheten som villkoret måste du uppdatera enheterna motsvarande taggar före distributionen. 

    ![Distribuera till Edge](./media/how-to-ci-cd/deploy-to-edge.png)

1. Klickar du på den **processen** och se till att din **fronta agenta** är **finns Linux-förhandsvisningen**.

    ![Konfigurera](./media/how-to-ci-cd/configure-env.png)

1. Öppna den **utlösare** fliken och aktivera den **kontinuerlig integrering** utlösaren. Se till att grenen som innehåller koden ingår.

    ![Utlösare](./media/how-to-ci-cd/configure-trigger.png)

1. Spara ny build-pipeline och Köa en ny version. Klicka på den **spara och köa** knappen.

1. Välj länken till versionen i meddelandefält som visas. Eller gå till skapa pipeline för att se senaste köade build-jobb.

    ![Utveckla](./media/how-to-ci-cd/build-def.png)

1. När bygget har slutförts ser vi sammanfattningen för varje uppgift och resultat i live loggfilen. 
    
    ![Slutför](./media/how-to-ci-cd/complete.png)

1. Du kan gå tillbaka till VS Code och kontrollera enhetsutforskaren för IoT Hub. Edge-enhet med modulen ska börja köra (se till att du har lagt till autentiseringsuppgifter för registret i Edge-körningen).

    ![Edge som körs](./media/how-to-ci-cd/edge-running.png)

## <a name="continuous-deployment-to-iot-edge-devices"></a>Kontinuerlig distribution till IoT Edge-enheter

För att aktivera kontinuerlig distribution, i princip måste du ställa in CI-jobb med rätt IoT Edge-enheter, aktivera den **utlösare** för grenarna i projektet. I en klassisk DevOps-praxis innehåller ett projekt två huvudsakliga grenar. Huvudgrenen bör vara den säkra versionen av koden och utveckla grenen innehåller de senaste ändringarna i koden. Alla utvecklare i teamet bör Förgrena utveckla gren till hans eller sin egen funktion grenen när börjar uppdatera koden, vilket innebär att alla skrivningar händer om funktionen grenar av grenen utveckla. Och varje pushade incheckning bör testas via CI-system. När testats fullt ut koden lokalt, slås grenen funktionen samman till grenen utveckla via en pull-begäran. När koden på developer-gren testas via CI-system, kan den sammanfogas till mastergrenen via en pull-begäran.

Så när du distribuerar till IoT Edge-enheter, finns det tre huvudsakliga miljöer.
- Du kan använda simulerade IoT Edge-enhet på utvecklingsdatorn eller distribuera till en fysisk IoT Edge-enhet på funktionen gren.
- Utveckla på avdelningskontor, bör du distribuera till en fysisk IoT Edge-enhet.
- På huvudgrenen ska target IoT Edge-enheter för produktionsenheter.

## <a name="next-steps"></a>Nästa steg

* Förstå IoT Edge-distribution i [förstå IoT Edge-distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md)
* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md).
