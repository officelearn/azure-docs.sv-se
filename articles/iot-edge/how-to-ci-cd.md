---
title: Azure IoT kant kontinuerlig integrering och kontinuerlig distribution | Microsoft Docs
description: Översikt över kontinuerlig integration och kontinuerlig distribution för Azure IoT kant
author: shizn
manager: ''
ms.author: xshi
ms.date: 04/30/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a8b58eae9aa08d8f6539370fa6e78a7a4813c18f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631028"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge---preview"></a>Kontinuerlig integrering och kontinuerlig distribution till Azure IoT kant - förhandsgranskning
Den här kursen visar hur du kan använda kontinuerlig integration och funktioner för kontinuerlig distribution för Visual Studio Team Services VSTS () och Microsoft Team Foundation Server (TFS) för att skapa, testa och distribuera program till snabbare och effektivare din Azure IoT kant. 

I den här självstudien får du lära dig hur man:
> [!div class="checklist"]
> * Skapa och i exemplet kant för IoT-lösning som innehåller enhet tester.
> * Installera tillägg för Azure IoT kant för din VSTS.
> * Konfigurera kontinuerlig integration (KO) för att skapa lösningen och köra testerna enhet.
> * Konfigurera kontinuerlig distribution (CD) för att distribuera lösningen och visa svar.

Det tar 30 minuter att slutföra den här självstudiekursen.

![CD-skivan och CI](./media/how-to-ci-cd/cd.png)

## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Skapa en lösning för Azure IoT kanten av exemplet med hjälp av Visual Studio Code

I det här avsnittet skapar du ett prov IoT kant lösning som innehåller kontroller som du kan köra som en del av skapar. Innan du följer riktlinjerna i det här avsnittet bör du slutföra stegen i [utveckla en kant för IoT-lösning med flera moduler i Visual Studio Code](tutorial-multiple-modules-in-vscode.md).

1. Skriv i VS kod kommandot paletten och kör kommandot **kant: kant för ny IoT-lösningen**. Välj arbetsytemappen, ange lösningens namn (standardnamnet är **EdgeSolution**), och skapa en C#-modul (**FilterModule**) som den första användarmodulen i den här lösningen. Du måste också ange Docker avbildningslagringsplatsen för din första modulen. Standard avbildningslagringsplatsen baseras på en lokal Docker-registret (`localhost:5000/filtermodule`). Du måste ändra registret för Azure-behållaren (`<your container registry address>/filtermodule`) eller Docker-hubb för ytterligare kontinuerlig integration.

    ![Konfigurera ACR](./media/how-to-ci-cd/acr.png)

2. Fönstret VS kod laddar IoT kant lösning arbetsytan. Du kan alternativt skriver och kör **kant: lägga till kant för IoT-modulen** att lägga till flera moduler. Det finns en `modules` mapp, en `.vscode` mapp och en manifestet mallfil distribution i rotmappen. Alla användare modulen koder kommer att undermappar under `modules`. Den `deployment.template.json` är manifestet Distributionsmall. Vissa parametrar i den här filen kommer att tolkas från den `module.json`, som finns i varje modul-mapp.

3. Ditt exempel kant för IoT-lösningen är nu klar. Standard C# modulen fungerar som en modul för pipe-meddelande. I den `deployment.template.json`, visas den här lösningen innehåller två moduler. Meddelandet kommer att skapas från den `tempSensor` modulen, och ska skickas direkt `FilterModule`, sedan skickas till din IoT-hubb. Ersätt hela **Program.cs** filen med nedan innehåll. Mer information om det här kodstycket kan referera till [skapar en IoT kant C#-modulen projekt](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module#create-an-iot-edge-module-project).

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
                // The Edge runtime gives us the connection string we need -- it is injected as an environment variable
                string connectionString = Environment.GetEnvironmentVariable("EdgeHubConnectionString");

                // Cert verification is not yet fully functional when using Windows OS for the container
                bool bypassCertVerification = RuntimeInformation.IsOSPlatform(OSPlatform.Windows);
                if (!bypassCertVerification) InstallCert();
                Init(connectionString, bypassCertVerification).Wait();

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
            /// Add certificate in local cert store for use by client for secure connection to IoT Edge runtime
            /// </summary>
            static void InstallCert()
            {
                string certPath = Environment.GetEnvironmentVariable("EdgeModuleCACertificateFile");
                if (string.IsNullOrWhiteSpace(certPath))
                {
                    // We cannot proceed further without a proper cert file
                    Console.WriteLine($"Missing path to certificate collection file: {certPath}");
                    throw new InvalidOperationException("Missing path to certificate file.");
                }
                else if (!File.Exists(certPath))
                {
                    // We cannot proceed further without a proper cert file
                    Console.WriteLine($"Missing path to certificate collection file: {certPath}");
                    throw new InvalidOperationException("Missing certificate file.");
                }
                X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
                store.Open(OpenFlags.ReadWrite);
                store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
                Console.WriteLine("Added Cert: " + certPath);
                store.Close();
            }
            /// <summary>
            /// Initializes the DeviceClient and sets up the callback to receive
            /// messages containing temperature information
            /// </summary>
            static async Task Init(string connectionString, bool bypassCertVerification = false)
            {
                Console.WriteLine("Connection String {0}", connectionString);

                MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
                // During dev you might want to bypass the cert verification. It is highly recommended to verify certs systematically in production
                if (bypassCertVerification)
                {
                    mqttSetting.RemoteCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) => true;
                }
                ITransportSettings[] settings = { mqttSetting };

                // Open a connection to the Edge runtime
                DeviceClient ioTHubModuleClient = DeviceClient.CreateFromConnectionString(connectionString, settings);
                await ioTHubModuleClient.OpenAsync();
                Console.WriteLine("IoT Hub module client initialized.");

                // Register callback to be called when a message is received by the module
                // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

                // Read TemperatureThreshold from Module Twin Desired Properties
                var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
                var moduleTwinCollection = moduleTwin.Properties.Desired;
                try {
                    temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
                } catch(ArgumentOutOfRangeException) {
                    Console.WriteLine("Proerty TemperatureThreshold not exist");
                }

                // Attach callback for Twin desired properties updates
                await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

                // Register callback to be called when a message is received by the module
                await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
            }

            static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
            {
                try
                {
                    Console.WriteLine("Desired property change:");
                    Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

                    if (desiredProperties["TemperatureThreshold"] != null)
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

            static async Task<MessageResponse> FilterMessages(Message message, object userContext)
            {
                try
                {
                    DeviceClient deviceClient = (DeviceClient)userContext;

                    var filteredMessage = filter(message);
                    if (filteredMessage != null)
                    {
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
        }
    }
    ```

4. Skapa ett .net Core enhet test projektet. Skapa en ny mapp i Utforskaren VS kod **tests\FilterModuleTest** på arbetsytan. I VS koden integrerad terminal (**Ctrl + '**), kör följande kommandon för att skapa ett xunit test-projekt och Lägg till referens till den **FilterModule** projekt.

    ```cmd
    cd tests\FilterModuleTest
    dotnet new xunit
    dotnet add reference ../../modules/FilterModule/FilterModule.csproj
    ```

    ![Mappstruktur](./media/how-to-ci-cd/add-test-project.png)

5. I den **FilterModuleTest** mapp, uppdatera namnet på **UnitTest1.cs** till **FilterModuleTest.cs**. Markera och öppna **FilterModuleTest.cs**, Ersätt hela koden med under kodavsnitt som innehåller enheten testerna mot FilterModule projektet.

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

6. Du kan ange följande kommandon för att köra kontroller lokalt i integrerad terminal. 
    ```cmd
    dotnet test
    ```

    ![Enhetstest](./media/how-to-ci-cd/unit-test.png)

7. Spara dessa projekt och sedan checkar in i databasen VSTS eller TFS.
    

> [!NOTE]
> Mer information om hur du använder VSTS code databaser, se [dela din kod med Visual Studio och VSTS Git](https://docs.microsoft.com/vsts/git/share-your-code-in-git-vs?view=vsts).


## <a name="configure-continuous-integration"></a>Konfigurera kontinuerlig integration
I det här avsnittet skapar du en build-definition som är konfigurerad för att köras automatiskt när du checkar in ändringar till exempel IoT Edge-lösningen och kör den automatiskt enhet testerna som den innehåller.

1. Logga in på ditt konto i VSTS (**https://**_ditt konto_**. visualstudio.com**) och öppna projektet där du har markerat i sample-appen.

    ![Checka in kod](./media/how-to-ci-cd/init-project.png)

1. Besök [Azure IoT kant VSTS](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) på VSTS Marketplace. Klicka på **hämta den kostnadsfria** och Följ guiden för att installera det här tillägget till ditt VSTS eller ladda ned till din TFS.

    ![Installera tillägget](./media/how-to-ci-cd/install-extension.png)

1. Öppna i din VSTS den **skapa &amp; versionen** hubb och i den **bygger** , Välj **+ ny definition**. Eller, om du redan har build definitioner, väljer du den **+ ny** knappen. 

    ![Ny version](./media/how-to-ci-cd/add-new-build.png)

1. Välj den **VSTS Git** typ av datakälla; Välj projekt, databasen och avdelningskontor där koden finns. Välj **fortsätta**.

    ![Välj VSTS git](./media/how-to-ci-cd/select-vsts-git.png)

1. I **Välj en mall** fönstret Välj **börja med en tom process**.

    ![Tom från början](./media/how-to-ci-cd/start-with-empty.png)

1. Klicka på **+** på höger sida av **fas 1** lägga till en aktivitet till fasen. Sök sedan och välj **.Net Core**, och klicka på **Lägg till** att lägga till den här aktiviteten i fasen.

    ![DotNet-test](./media/how-to-ci-cd/add-dot-net-core.png)

1. Uppdatering av **visningsnamn** till **dotnet test**, och i den **kommandot** listrutan, Välj **testa**. Lägga till under sökvägen till den **sökvägen till projektet**.

    ```
    tests/FilterModuleTest/*.csproj
    ```

    ![Konfigurera dotnet-test](./media/how-to-ci-cd/dotnet-test.png)

1. Klicka på **+** på höger sida av **fas 1** lägga till en aktivitet till fasen. Sök sedan och välj **Azure IoT kant**, och klicka på **Lägg till** knappen **två gånger** att lägga till fasen dessa aktiviteter.

    ![IoT Edge](./media/how-to-ci-cd/add-azure-iot-edge.png)

1. I den första aktiviteten i Azure IoT kant uppdatera den **visningsnamn** till **modulen bygga och Push**, och i den **åtgärd** listrutan, Välj **skapa och skicka**. I den **Module.json filen** textruta lägga till under sökvägen till den. Välj **registret behållartypen**, måste du konfigurera och välj samma registret i koden. Den här uppgiften skapar och push alla moduler i lösningen och publicera i registret för behållaren som du angav. 

    ```
    **/module.json
    ```

    ![Modulen bygg- och Push](./media/how-to-ci-cd/module-build-push.png)

1. I den andra uppgiften i Azure IoT kant uppdatera den **visningsnamn** till **till IoT-enhet**, och i den **åtgärd** listrutan, Välj **distribuera IoT kant enheten**. Välj din Azure-prenumeration och ange din IoT-hubb-namn. Du kan ange en gräns för IoT distributions-ID och prioritet för distribution. Du kan också välja att distribuera till en eller flera enheter. Om du distribuerar till flera enheter, måste du ange målvillkoren för enheten. Om du vill använda enheten taggar som villkor, måste du uppdatera enheterna motsvarande taggar före distributionen. 

    ![Distribuera kant](./media/how-to-ci-cd/deploy-to-edge.png)

1. Klickar du på den **processen** och kontrollera att din **Agent kön** är **finns Linux Preview**.

    ![Konfigurera](./media/how-to-ci-cd/configure-env.png)

1. Öppna den **utlösare** fliken och aktivera den **kontinuerlig integration** utlösare. Se till att grenen som innehåller koden ingår.

    ![Utlösare](./media/how-to-ci-cd/configure-trigger.png)

1. Spara den nya build-definitionen och kö en ny version. Klicka på den **Spara & kö** knappen.

1. Välj länken för att bygga i meddelandefältet som visas. Eller skapa definition för att se senaste köade build-jobbet.

    ![Utveckla](./media/how-to-ci-cd/build-def.png)

1. När versionen har slutförts kan du se sammanfattning för varje aktivitet och resultat i live loggfilen. 
    
    ![Slutför](./media/how-to-ci-cd/complete.png)

1. Du kan gå tillbaka till VS-kod och kontrollera IoT-hubb enheten explorer. Gränsenheten med modulen ska börja köra (Kontrollera att du har lagt till registret autentiseringsuppgifter Edge runtime).

    ![Edge körs](./media/how-to-ci-cd/edge-running.png)

## <a name="continuous-deployment-to-iot-edge-devices"></a>Kontinuerlig distribution till enheter som IoT

Om du vill aktivera kontinuerlig distribution i princip du måste ange CI-jobb med rätt kant för IoT-enheter, aktivera den **utlösare** för grenarna i projektet. Ett projekt innehåller två huvudsakliga grenar i en klassiska DevOps-metod. Mastergrenen bör vara stabil version av koden och utveckla grenen innehåller de senaste ändringarna i koden. Alla utvecklare i teamet bör duplicera utveckla gren till hans eller sitt eget funktionen gren när startar uppdaterar kod, vilket betyder att alla genomför sker på funktionen filialer av grenen utveckla. Och varje intryckt genomförande bör testas via CI-system. När testats fullt ut koden lokalt, slås grenen funktionen samman utveckla grenen via en pull-begäran. När koden på utvecklare gren testas via CI-system, kan de kopplas till mastergrenen via en pull-begäran.

Så, när du distribuerar till enheter som IoT, det finns tre huvudsakliga miljöer.
- Du kan använda simulerade IoT insticksenhet på utvecklingsdatorn eller distribuera till en fysisk enhet som IoT kanten på funktionen gren.
- Utveckla på avdelningskontor, du ska distribuera till en fysisk enhet som IoT kant.
- På mastergrenen ska IoT kant målenheterna enheter för produktion.

## <a name="next-steps"></a>Nästa steg

Den här kursen visar hur du kan använda kontinuerlig integrering och funktioner för kontinuerlig distribution av VSTS eller TFS. 

* Förstå IoT kant distributionen i [förstå IoT kant distributioner för enstaka enheter eller i skala](module-deployment-monitoring.md)
* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [distribuera och övervaka IoT kant moduler i skala] [how-till-distribuera-monitor.md].









