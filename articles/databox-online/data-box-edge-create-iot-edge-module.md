---
title: C# IoT Edge-modul för Azure Data Box Edge | Microsoft Docs
description: Lär dig mer om att utveckla en C# IoT Edge-modul som kan distribueras på din Data Box-Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/19/2019
ms.author: alkohli
ms.openlocfilehash: 522dddde4994bb019e6547fcd18465b201f048d8
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401734"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge"></a>Utveckla en C# IoT Edge-modul för flytt av filer på Data Box Edge

Den här artikeln vägleder dig genom hur du skapar en IoT Edge-modul för distribution med din Data Box Edge-enhet. Azure Data Box Edge är en lagringslösning som gör det möjligt att bearbeta data och skicka dem via nätverk till Azure.

Du kan använda Azure IoT Edge-moduler med din Data Box-Edge för att omvandla data som flyttas till Azure. Modulen som används i den här artikeln implementerar logik för att kopiera en fil från en lokal resurs till en cloud-resurs på din Data Box Edge-enhet.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa ett behållarregister för att lagra och hantera dina moduler (Docker-avbildningar).
> * Skapa en IoT Edge-modul ska distribueras i din Data Box Edge-enhet.


## <a name="about-the-iot-edge-module"></a>Om IoT Edge-modul

Din Data Box Edge-enhet kan distribuera och köra IoT Edge-moduler. Edge-moduler är i stort sett Docker-behållare som utför en viss uppgift, till exempel mata in ett meddelande från en enhet, omvandla ett meddelande eller skicka ett meddelande till en IoT-hubb. I den här artikeln skapar du en modul som kopierar filer från en lokal resurs till en cloud-resurs på din Data Box Edge-enhet.

1. Filer skrivs till den lokala resursen på din Data Box Edge-enhet.
2. Filen händelse generatorn skapar en fil händelse för varje fil som skrivs till den lokala resursen. Filhändelser skapas också när en fil ändras. Filhändelser skickas sedan till IoT Edge Hub (i IoT Edge-körningen).
3. Anpassad IoT Edge-modul bearbetar händelsen filen om du vill skapa en fil-händelseobjekt som också innehåller en relativ sökväg för filen. Modulen genererar en absolut sökväg med hjälp av den relativa filsökvägen och kopierar filen från den lokala resursen till molnet-resursen. Modulen tar sedan bort filen från den lokala resursen.

![Hur Azure IoT Edge-modul fungerar på Data Box Edge](./media/data-box-edge-create-iot-edge-module/how-module-works.png)

När filen är i molnet resursen kan den automatiskt hämtar laddas upp till Azure Storage-kontot.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrollera att du har:

- En Data Box Edge-enhet som kör.

    - Enheten har även en associerad IoT Hub-resurs.
    - Enheten har Edge compute konfigurerad roll.
    Mer information går du till [konfigurera beräkna](data-box-edge-deploy-configure-compute.md#configure-compute) för din Data Box-Edge.

- I följande Utvecklingsresurser:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
    - [Azure IoT Edge-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Du kan behöva skapa ett konto för att ladda ned och installera programvaran.

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Ett Azure-containerregister är ett privat Docker-register i Azure där du kan lagra och hantera dina privata Docker-containeravbildningar. Två populära Docker registertjänster som är tillgängliga i molnet är Azure Container Registry och Docker Hub. Den här artikeln använder Container Registry.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Välj **skapa en resurs > behållare > Container Registry**. Klicka på **Skapa**.
3. Ange:

   1. Ett unikt **registernamn** inom Azure som innehåller 5 till 50 alfanumeriska tecken.
   2. Välj en **prenumeration**.
   3. Skapa en ny eller välj en befintlig **resursgrupp**.
   4. Välj en **Plats**. Vi rekommenderar att den här platsen är samma som som är associerad med Data Box Edge-resursen.
   5. Växla till **Administratörsanvändare** för att **Aktivera**.
   6. Ange SKU: N **grundläggande**.

      ![Skapa containerregister](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Välj **Skapa**.
5. När du har skapat ditt containerregister går du till det och väljer **Åtkomstnycklar**.

    ![Hämta åtkomstnycklar](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du använder dessa värden senare att publicera Docker-avbildningen till registret och att lägga till autentiseringsuppgifter för registret i Azure IoT Edge-körningen.


## <a name="create-an-iot-edge-module-project"></a>Skapa ett projekt för IoT Edge-modulen

Följande steg skapar en IoT Edge-modulen projekt som baseras på .NET Core 2.1 SDK. Projektet använder Visual Studio Code och Azure IoT Edge-tillägget.

### <a name="create-a-new-solution"></a>Skapa en ny lösning

Skapa en C#-lösningsmall som du kan anpassa med din egen kod.

1. I Visual Studio Code, Välj **Visa > Kommandopaletten** att öppna kommandopaletten VS Code.
2. I kommandopaletten skriver och kör du kommandot **Azure: Logga in** och följer anvisningarna för att logga in på ditt Azure-konto. Om du redan är inloggad kan du hoppa över det här steget.
3. Skriv och kör kommandot **Azure IoT Edge på kommandopaletten: New IoT Edge solution** (Ny IoT Edge-lösning). Ange följande information i kommandopaletten för att skapa din lösning:

    1. Välj den mapp där du vill skapa lösningen.
    2. Ange ett namn för din lösning eller välj standardnamnet **EdgeSolution**.
    
        ![Skapa ny lösning 1](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Välj **C# Module** som mall för modulen.
    4. Ersätt namnet på standard-modulen med namnet som du vill tilldela, i det här fallet är det **FileCopyModule**.
    
        ![Skapa ny lösning 2](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Ange behållarregistret som du skapade i föregående avsnitt som avbildningslagringsplatsen för din första modul. Ersätt **localhost:5000** med det serverinloggningsvärde som du kopierade.

        Den sista strängen ut `<Login server name>/<Module name>`. I det här exemplet strängen är: `mycontreg2.azurecr.io/filecopymodule`.

        ![Skapa ny lösning 3](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Gå till **fil > Öppna mappen**.

    ![Skapa ny lösning 4](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Bläddra och peka på den **EdgeSolution** mapp som du skapade tidigare. VS Code-fönstret läser in din IoT Edge lösningens arbetsyta med dess fem komponenter på toppnivå. Du inte redigera den **.vscode** mappen **.gitignore** filen **.env** -fil och **deployment.template.json** i den här artikeln.
    
    Den enda komponenten som du har ändrat är modulmappen. Den här mappen innehåller C#-kod för modulen och Docker-filer för att skapa din modul som en behållaravbildning.

    ![Skapa ny lösning 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

1. Öppna i VS Code-Utforskaren **moduler > FileCopyModule > Program.cs**.
2. Överst på den **FileCopyModule namnområde**, Lägg till följande using-satser för typer som används senare. **Microsoft.Azure.Devices.Client.Transport.Mqtt** är ett protokoll för att skicka meddelanden till IoT Edge Hub.

    ```
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;
    using Newtonsoft.Json;
    ```
3. Lägg till den **InputFolderPath** och **OutputFolderPath** variabel i programklassen.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Lägg till den **MessageBody** klass i programklassen. Dessa klasser definierar det förväntade schemat för brödtexten i inkommande meddelanden.

    ```
    /// <summary>
    /// The MessageBody class defines the expected schema for the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. I metoden **Init** skapar och konfigurerar koden ett **ModuleClient**-objekt. Det här objektet kan modulen ska kunna ansluta till lokala Azure IoT Edge-körningen med hjälp av MQTT-protokollet för att skicka och ta emot meddelanden. Anslutningssträngen som används i Init-metoden levereras till modul av IoT Edge-körningen. Koden registrerar ett FileCopy återanrop för att ta emot meddelanden från en IoT Edge hub via den **indata1** slutpunkt.

    ```
    /// <summary>
    /// Initializes the ModuleClient and sets up the callback to receive
    /// messages containing file event information
    /// </summary>
    static async Task Init()
    {
        MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
        ITransportSettings[] settings = { mqttSetting };

        // Open a connection to the IoT Edge runtime
        ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
        await ioTHubModuleClient.OpenAsync();
        Console.WriteLine("IoT Hub module client initialized.");

        // Register callback to be called when a message is received by the module
        await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FileCopy, ioTHubModuleClient);
    }
    ```

6. Infoga kod för **FileCopy**.

    ```
        /// <summary>
        /// This method is called whenever the module is sent a message from the IoT Edge Hub. 
        /// This method deserializes the file event, extracts the corresponding relative file path, and creates the absolute input file path using the relative file path and the InputFolderPath.
        /// This method also forms the absolute output file path using the relative file path and the OutputFolderPath. It then copies the input file to output file and deletes the input file after the copy is complete.
        /// </summary>
        static async Task<MessageResponse> FileCopy(Message message, object userContext)
        {
            int counterValue = Interlocked.Increment(ref counter);

            try
            {
                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                if (!string.IsNullOrEmpty(messageString))
                {
                    var fileEvent = JsonConvert.DeserializeObject<FileEvent>(messageString);

                    string relativeFileName = fileEvent.ShareRelativeFilePath.Replace("\\", "/");
                    string inputFilePath = InputFolderPath + relativeFileName;
                    string outputFilePath = OutputFolderPath + relativeFileName;

                    if (File.Exists(inputFilePath))                
                    {
                        Console.WriteLine($"Moving input file: {inputFilePath} to output file: {outputFilePath}");
                        var outputDir = Path.GetDirectoryName(outputFilePath);
                        if (!Directory.Exists(outputDir))
                        {
                            Directory.CreateDirectory(outputDir);
                        }

                        File.Copy(inputFilePath, outputFilePath, true);
                        Console.WriteLine($"Copied input file: {inputFilePath} to output file: {outputFilePath}");
                        File.Delete(inputFilePath);
                        Console.WriteLine($"Deleted input file: {inputFilePath}");
                    } 
                    else
                    {
                        Console.WriteLine($"Skipping this event as input file doesn't exist: {inputFilePath}");   
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Caught exception: {0}", ex.Message);
                Console.WriteLine(ex.StackTrace);
            }

            Console.WriteLine($"Processed event.");
            return MessageResponse.Completed;
        }
    ```

7. Spara filen.

## <a name="build-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

I det föregående avsnittet du skapade en IoT Edge-lösning och lagt till kod till FileCopyModule att kopiera filer från lokala resurser till molnet-resursen. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

1. I VSCode, går du till terminalen > Ny Terminal för att öppna en ny integrerade Visual Studio Code-terminalen.
2. Logga in på Docker genom att ange följande kommando i den integrerade terminalen.

    `docker login <ACR login server> -u <ACR username>`

    Använd inloggningsserver och ange det användarnamn som du kopierade från ditt behållarregister. 

    ![Skapa och skicka IoT Edge-lösning](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. När du uppmanas att ange lösenord, ange lösenordet. Du kan också hämta värdena för inloggningsserver, användarnamn och lösenord från den **åtkomstnycklar** i ditt behållarregister i Azure-portalen.
 
3. När autentiseringsuppgifterna ska överföras, kan du skicka din modul-avbildning till Azure container registry. I VS Code-Utforskaren högerklickar du på den **module.json** och väljer **Build and Push IoT Edge-lösningen**.

    ![Skapa och skicka IoT Edge-lösning](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    När du berätta för Visual Studio Code för att skapa din lösning, körs den två kommandon i den integrerade terminalen: docker versions- och docker push. Dessa två kommandon skapar din kod, bäddar in CSharpModule.dll i en container och skickar sedan koden till det containerregister som du angav när du initierade lösningen.

    Du uppmanas att välja den modul-plattformen. Välj *amd64* för Linux.

    ![Välj plattform](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Endast Linux-moduler stöds.

    Du kan se följande varning som du kan ignorera:

    *Program.cs(77,44): warning CS1998: Den här async-metod saknar 'await' operatörer och körs synkront. Överväg att använda operatorn 'await' i väntan på icke-blockerande API-anrop eller 'await Task.Run(...)' för CPU-bundna arbete i en bakgrundstråd.*

4. Den fullständiga adressen med tagg för containeravbildningen finns i den integrerade VS Code-terminalen. Bild-adressen är byggd från information som finns i filen module.json med formatet `<repository>:<version>-<platform>`. Den här artikeln är det bör se ut `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`.

## <a name="next-steps"></a>Nästa steg

För att distribuera och köra den här modulen på Data Box Edge, hittar du i [lägger till en modul](data-box-edge-deploy-configure-compute.md#add-a-module).
