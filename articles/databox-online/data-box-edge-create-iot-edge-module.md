---
title: C# IoT Edge-modul för Azure Data Box Edge | Microsoft-dokument
description: Lär dig hur du utvecklar en C# IoT Edge-modul som kan distribueras på din Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/06/2019
ms.author: alkohli
ms.openlocfilehash: 3aa1190fb713c2fbdedcb1ce84a65d4263693827
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942557"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge"></a>Utveckla en C# IoT Edge-modul för att flytta filer på Data Box Edge

I den här artikeln beskrivs hur du skapar en IoT Edge-modul för distribution med din Data Box Edge-enhet. Azure Data Box Edge är en lagringslösning som gör det möjligt att bearbeta data och skicka dem via nätverk till Azure.

Du kan använda Azure IoT Edge-moduler med din Data Box Edge för att omvandla data när de flyttas till Azure. Modulen som används i den här artikeln implementerar logiken för att kopiera en fil från en lokal resurs till en molnresurs på din Data Box Edge-enhet.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa ett behållarregister för att lagra och hantera modulerna (Docker-avbildningar).
> * Skapa en IoT Edge-modul som du kan distribuera på databoxens edge-enhet.


## <a name="about-the-iot-edge-module"></a>Om IoT Edge-modulen

Data Box Edge-enheten kan distribuera och köra IoT Edge-moduler. Edge-moduler är i huvudsak Docker-behållare som utför en viss uppgift, till exempel förtär ett meddelande från en enhet, transformera ett meddelande eller skicka ett meddelande till en IoT Hub. I den här artikeln ska du skapa en modul som kopierar filer från en lokal resurs till en molnresurs på din Data Box Edge-enhet.

1. Filer skrivs till den lokala resursen på din Data Box Edge-enhet.
2. Filhändelsegeneratorn skapar en filhändelse för varje fil som skrivs till den lokala resursen. Filhändelserna genereras också när en fil ändras. Filhändelserna skickas sedan till IoT Edge Hub (i IoT Edge-körning).
3. Den anpassade modulen IoT Edge bearbetar filhändelsen för att skapa ett filhändelseobjekt som också innehåller en relativ sökväg för filen. Modulen genererar en absolut sökväg med hjälp av den relativa filsökvägen och kopierar filen från den lokala resursen till molnresursen. Modulen tar sedan bort filen från den lokala resursen.

![Så här fungerar Azure IoT Edge-modulen på Data Box Edge](./media/data-box-edge-create-iot-edge-module/how-module-works-1.png)

När filen finns i molnresursen överförs den automatiskt till ditt Azure Storage-konto.

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att du har:

- En Data Box Edge-enhet som körs.

    - Enheten har också en associerad IoT Hub-resurs.
    - Enheten har Edge-beräkningsrollen konfigurerad.
    Mer information finns i [Konfigurera beräkning](data-box-edge-deploy-configure-compute.md#configure-compute) för databoxens edge.

- Följande utvecklingsresurser:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
    - [Azure IoT Edge-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Du kan behöva skapa ett konto för att ladda ner och installera programvaran.

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Ett Azure-containerregister är ett privat Docker-register i Azure där du kan lagra och hantera dina privata Docker-containeravbildningar. De två populära Docker-registertjänster som är tillgängliga i molnet är Azure Container Registry och Docker Hub. I den här artikeln används behållarregistret.

1. Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
2. Välj **Skapa en resurs > behållare > containerregister**. Klicka på **Skapa**.
3. Ge:

   1. Ett unikt **registernamn** i Azure som innehåller 5 till 50 alfanumeriska tecken.
   2. Välj en **prenumeration**.
   3. Skapa nytt eller välj en befintlig **resursgrupp**.
   4. Välj en **plats**. Vi rekommenderar att den här platsen är samma som som är associerad med databoxkantsresursen.
   5. Växla till **Administratörsanvändare** för att **Aktivera**.
   6. Ställ in SKU till **Basic**.

      ![Skapa containerregister](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Välj **Skapa**.
5. När du har skapat ditt containerregister går du till det och väljer **Åtkomstnycklar**.

    ![Hämta åtkomstnycklar](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du använder dessa värden senare för att publicera Docker-avbildningen i registret och lägga till registerautentiseringsuppgifterna i Azure IoT Edge-körningen.


## <a name="create-an-iot-edge-module-project"></a>Skapa ett projekt för IoT Edge-modulen

I följande steg skapas ett IoT Edge-modulprojekt baserat på .NET Core 2.1 SDK. Projektet använder Visual Studio Code och Azure IoT Edge-tillägget.

### <a name="create-a-new-solution"></a>Skapa en ny lösning

Skapa en C#-lösningsmall som du kan anpassa med din egen kod.

1. I Visual Studio-kod väljer du **Visa > kommandopalett** för att öppna kommandopaletten VS-kod.
2. Ange och kör kommandot **Azure: Logga in** i kommandopaletten och följ anvisningarna för att logga in med ditt Azure-konto. Om du redan är inloggad kan du hoppa över det här steget.
3. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) på kommandopaletten. Ange följande information i kommandopaletten för att skapa din lösning:

    1. Välj den mapp där du vill skapa lösningen.
    2. Ange ett namn för din lösning eller välj standardnamnet **EdgeSolution**.
    
        ![Skapa ny lösning 1](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Välj **C# Module** som modulmall.
    4. Ersätt standardmodulnamnet med det namn du vill tilldela, i det här fallet är det **FileCopyModule**.
    
        ![Skapa ny lösning 2](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Ange behållarregistret som du skapade i föregående avsnitt som avbildningsdatabas för den första modulen. Ersätt **localhost:5000** med det serverinloggningsvärde som du kopierade.

        Den sista strängen ser ut som `<Login server name>/<Module name>`. I det här exemplet `mycontreg2.azurecr.io/filecopymodule`är strängen: .

        ![Skapa ny lösning 3](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Gå till **> öppna mappen**.

    ![Skapa ny lösning 4](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Bläddra och peka på **edgesolution-mappen** som du skapade tidigare. Vs-kodfönstret läser in din IoT Edge-lösningsarbetsyta med sina fem komponenter på den översta nivån. Du redigerar inte **.vscode-mappen,** **.gitignore-filen,** **.env-filen** och **deployment.template.json** i den här artikeln.
    
    Den enda komponent som du ändrar är modulens mapp. Den här mappen har C#-koden för din modul och Docker-filer för att skapa din modul som en behållaravbildning.

    ![Skapa ny lösning 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

1. Öppna **moduler > FileCopyModule > Program.cs i**VS-kodutforskaren .
2. Lägg till följande med hjälp av satser för typer som används senare högst upp i **namnområdet FileCopyModule.** **Microsoft.Azure.Devices.Client.Transport.Mqtt** är ett protokoll för att skicka meddelanden till IoT Edge Hub.

    ```
    namespace FileCopyModule
    {
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using Newtonsoft.Json;
    ```
3. Lägg till variabeln **InputFolderPath** och **OutputFolderPath** i klassen Program.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Omedelbart efter föregående steg lägger du till klassen **FileEvent** för att definiera meddelandetexten.

    ```
    /// <summary>
    /// The FileEvent class defines the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. I **metoden Init**skapar och konfigurerar koden ett **ModuleClient-objekt.** Med det här objektet kan modulen ansluta till den lokala Azure IoT Edge-körningen med MQTT-protokollet för att skicka och ta emot meddelanden. Anslutningssträngen som används i Init-metoden skickas till modulen av IoT Edge-körningen. Koden registrerar en FileCopy-motringning för att ta emot meddelanden från en IoT Edge-hubb via **endpointen input1.** Ersätt **Init-metoden** med följande kod.

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

6. Ta bort koden för **PipeMessage-metoden** och sätt i koden för **FileCopy**.

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
8. Du kan också [hämta ett befintligt kodexempel](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable) för det här projektet. Du kan sedan validera filen som du sparade mot **program.cs** filen i det här exemplet.

## <a name="build-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod i FileCopyModule för att kopiera filer från lokal resurs till molnresursen. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

1. Öppna en ny integrerad terminal > i VSCode i VSCode.
2. Logga in på Docker genom att ange följande kommando i den integrerade terminalen.

    `docker login <ACR login server> -u <ACR username>`

    Använd inloggningsservern och användarnamnet som du kopierade från behållarregistret.

    ![Bygg och tryck IoT Edge-lösning](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Ange lösenordet när du uppmanas att ange lösenordet. Du kan också hämta värdena för inloggningsserver, användarnamn och lösenord från **åtkomstnycklarna** i behållarregistret i Azure-portalen.
 
3. När autentiseringsuppgifterna har angetts kan du skicka modulavbildningen till ditt Azure-behållarregister. Högerklicka på **module.json-filen** i VS-kodutforskaren och välj **Bygg och push IoT Edge-lösning**.

    ![Bygg och tryck IoT Edge-lösning](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    När du talar om för Visual Studio Code att bygga din lösning körs två kommandon i den integrerade terminalen: docker build och docker push. Dessa två kommandon skapar din kod, bäddar in CSharpModule.dll i en container och skickar sedan koden till det containerregister som du angav när du initierade lösningen.

    Du uppmanas att välja modulplattformen. Välj *amd64* som motsvarar Linux.

    ![Välj plattform](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Endast Linux-modulerna stöds.

    Följande varning kan visas som du kan ignorera:

    *Program.cs(77,44): varning CS1998: Denna async metod saknar "vänta" operatörer och kommer att köras synkront. Överväg att använda operatorn "vänta" för att invänta icke-blockerande API-anrop eller vänta på Task.Run(...)' för att utföra CPU-bundet arbete på en bakgrundstråd.*

4. Den fullständiga adressen med tagg för containeravbildningen finns i den integrerade VS Code-terminalen. Bildadressen är byggd av information som finns i module.json-filen med formatet `<repository>:<version>-<platform>`. För den här artikeln `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`bör det se ut som .

## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar och kör den här modulen på Data Box Edge finns i stegen i [Lägg till en modul](data-box-edge-deploy-configure-compute.md#add-a-module).
