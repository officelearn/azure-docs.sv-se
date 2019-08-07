---
title: C#IoT Edge modul för Azure Data Box Edge | Microsoft Docs
description: Lär dig hur du utvecklar C# en IoT Edge-modul som kan distribueras på din data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 734ad263356ab9f91c7cb92ab174a14e0c5dd867
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775178"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge"></a>Utveckla en C# IoT Edge-modul för att flytta filer på data Box Edge

Den här artikeln beskriver hur du skapar en IoT Edge-modul för distribution med din Data Box Edge-enhet. Azure Data Box Edge är en lagringslösning som gör det möjligt att bearbeta data och skicka dem via nätverk till Azure.

Du kan använda Azure IoT Edge moduler med Data Box Edge för att transformera data när de flyttas till Azure. Modulen som används i den här artikeln implementerar logiken för att kopiera en fil från en lokal resurs till en moln resurs på din Data Box Edge-enhet.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa ett behållar register för att lagra och hantera dina moduler (Docker-avbildningar).
> * Skapa en IoT Edge-modul som ska distribueras på din Data Box Edge-enhet.


## <a name="about-the-iot-edge-module"></a>Om modulen IoT Edge

Din Data Box Edge-enhet kan distribuera och köra IoT Edge moduler. Edge-moduler är i princip Docker-behållare som utför en speciell uppgift, till exempel mata in ett meddelande från en enhet, transformera ett meddelande eller skicka ett meddelande till ett IoT Hub. I den här artikeln ska du skapa en modul som kopierar filer från en lokal resurs till en moln resurs på din Data Box Edge-enhet.

1. Filerna skrivs till den lokala resursen på din Data Box Edge-enhet.
2. Fil händelse generatorn skapar en fil händelse för varje fil som skrivs till den lokala resursen. Fil händelserna skapas också när en fil ändras. Fil händelserna skickas sedan till IoT Edge Hub (i IoT Edge Runtime).
3. Den anpassade modulen IoT Edge bearbetar fil händelsen för att skapa ett fil händelse objekt som också innehåller en relativ sökväg till filen. Modulen genererar en absolut sökväg med den relativa fil Sök vägen och kopierar filen från den lokala resursen till moln resursen. Modulen tar sedan bort filen från den lokala resursen.

![Så här fungerar Azure IoT Edge-modulen på Data Box Edge](./media/data-box-edge-create-iot-edge-module/how-module-works-1.png)

När filen finns i moln resursen överförs den automatiskt till ditt Azure Storage-konto.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrollera att du har:

- En Data Box Edge enhet som kör.

    - Enheten har också en tillhör ande IoT Hub-resurs.
    - Enhetens beräknings roll har kon figurer ATS.
    Mer information finns i [Konfigurera Compute](data-box-edge-deploy-configure-compute.md#configure-compute) för din data Box Edge.

- Följande utvecklings resurser:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
    - [Azure IoT Edge-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Du kan behöva skapa ett konto för att ladda ned och installera program varan.

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Ett Azure-containerregister är ett privat Docker-register i Azure där du kan lagra och hantera dina privata Docker-containeravbildningar. De två populära Docker-register tjänsterna som är tillgängliga i molnet är Azure Container Registry och Docker Hub. Den här artikeln använder Container Registry.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Välj **skapa en resurs > behållare > container Registry**. Klicka på **Skapa**.
3. Använda

   1. Ett unikt **register namn** i Azure som innehåller 5 till 50 alfanumeriska tecken.
   2. Välj en **prenumeration**.
   3. Skapa en ny eller Välj en befintlig **resurs grupp**.
   4. Välj en **Plats**. Vi rekommenderar att den här platsen är samma som den som är kopplad till den Data Box Edge resursen.
   5. Växla till **Administratörsanvändare** för att **Aktivera**.
   6. Ange SKU: n till **Basic**.

      ![Skapa containerregister](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Välj **Skapa**.
5. När du har skapat ditt containerregister går du till det och väljer **Åtkomstnycklar**.

    ![Hämta åtkomst nycklar](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du kan använda dessa värden senare för att publicera Docker-avbildningen i registret och lägga till registervärdena i Azure IoT Edge Runtime.


## <a name="create-an-iot-edge-module-project"></a>Skapa ett projekt för IoT Edge-modulen

Följande steg skapar ett IoT Edge module-projekt baserat på .NET Core 2,1 SDK. Projektet använder Visual Studio Code och Azure IoT Edge-tillägget.

### <a name="create-a-new-solution"></a>Skapa en ny lösning

Skapa en C#-lösningsmall som du kan anpassa med din egen kod.

1. I Visual Studio Code väljer du **visa > kommando palett** för att öppna kommando paletten vs Code.
2. I kommandopaletten skriver och kör du kommandot **Azure: Logga in** och följer anvisningarna för att logga in på ditt Azure-konto. Om du redan är inloggad kan du hoppa över det här steget.
3. Skriv och kör kommandot **Azure IoT Edge på kommandopaletten: New IoT Edge solution** (Ny IoT Edge-lösning). Ange följande information i kommandopaletten för att skapa din lösning:

    1. Välj den mapp där du vill skapa lösningen.
    2. Ange ett namn för din lösning eller välj standardnamnet **EdgeSolution**.
    
        ![Skapa ny lösning 1](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Välj **C# Module** som mall för modulen.
    4. Ersätt namnet på standardmodulen med det namn som du vill tilldela, i det här fallet är det **FileCopyModule**.
    
        ![Skapa ny lösning 2](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Ange det behållar register som du skapade i föregående avsnitt som avbildnings lagrings plats för din första modul. Ersätt **localhost:5000** med det serverinloggningsvärde som du kopierade.

        Den slutliga strängen ser ut `<Login server name>/<Module name>`som. I det här exemplet är strängen: `mycontreg2.azurecr.io/filecopymodule`.

        ![Skapa ny lösning 3](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Gå till **filen > öppna mappen**.

    ![Skapa ny lösning 4](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Bläddra och peka på **EdgeSolution** -mappen som du skapade tidigare. VS Code-fönstret läser in din IoT Edge lösnings arbets yta med dess fem toppnivå komponenter. Du kan inte redigera **. VSCode** -mappen, **. gitignore** -filen, **. miljö** filen och filen **Deployment. template. JSON** i den här artikeln.
    
    Den enda komponent som du ändrar är mappen moduler. Den här mappen innehåller C# koden för din modul och Docker-filer för att bygga modulen som en behållar avbildning.

    ![Skapa ny lösning 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

1. I VS Code-Utforskaren öppnar du **moduler > FileCopyModule > program.cs**.
2. Längst upp i FileCopyModule- **namnområdet**lägger du till följande using-instruktioner för typer som används senare. **Microsoft. Azure. devices. client. transport. MQTT** är ett protokoll för att skicka meddelanden till IoT Edge Hub.

    ```
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;
    using Newtonsoft.Json;
    ```
3. Lägg till variabeln **InputFolderPath** och **OutputFolderPath** i program klassen.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Lägg till **FileEvent** -klassen för att definiera meddelande texten.

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

5. I metoden **Init** skapar och konfigurerar koden ett **ModuleClient**-objekt. Med det här objektet kan modulen ansluta till den lokala Azure IoT Edge runtime med hjälp av MQTT-protokollet för att skicka och ta emot meddelanden. Anslutnings strängen som används i init-metoden anges till modulen av IoT Edge Runtime. Koden registrerar ett FileCopy-återanrop för att ta emot meddelanden från en IoT Edge hubb via **INPUT1** -slutpunkten.

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

6. Infoga koden för **FileCopy**.

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

I föregående avsnitt skapade du en IoT Edge-lösning och lagt till kod i FileCopyModule för att kopiera filer från lokal resurs till moln resursen. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

1. I VSCode går du till Terminal > New Terminal för att öppna en ny Visual Studio Code-integrerad Terminal.
2. Logga in på Docker genom att ange följande kommando i den integrerade terminalen.

    `docker login <ACR login server> -u <ACR username>`

    Använd den inloggnings Server och det användar namn som du kopierade från behållar registret. 

    ![Lösning för att bygga och push IoT Edge](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Ange lösen ordet när du uppmanas att ange lösen ord. Du kan också hämta värdena för inloggnings Server, användar namn och lösen ord från **åtkomst nycklarna** i behållar registret i Azure Portal.
 
3. När du har angett autentiseringsuppgifterna kan du skicka modulens avbildning till Azure Container Registry. I VS Code-Utforskaren högerklickar du på filen **module. JSON** och väljer **Build och push IoT Edge-lösning**.

    ![Lösning för att bygga och push IoT Edge](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    När du anger Visual Studio Code för att bygga lösningen, körs två kommandon i den integrerade terminalen: Docker-build och Docker push. Dessa två kommandon skapar din kod, bäddar in CSharpModule.dll i en container och skickar sedan koden till det containerregister som du angav när du initierade lösningen.

    Du uppmanas att välja modul-plattformen. Välj *amd64* som motsvarar Linux.

    ![Välj plattform](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Endast Linux-moduler stöds.

    Du kan se följande varning att du kan ignorera:

    *Program. CS (77, 44): varning CS1998: Den här asynkrona metoden saknar "await"-operatorer och kommer att köras synkront. Överväg att använda operatorn "await" för att vänta på icke-blockerande API-anrop, eller "vänta aktivitet. Run (...)" för att göra CPU-kopplat arbete på en bakgrunds tråd.*

4. Den fullständiga adressen med tagg för containeravbildningen finns i den integrerade VS Code-terminalen. Bild adressen skapas utifrån information som finns i filen module. JSON med formatet `<repository>:<version>-<platform>`. I den här artikeln bör den se ut `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`.

## <a name="next-steps"></a>Nästa steg

Om du vill distribuera och köra modulen på Data Box Edge, se stegen i [lägga till en modul](data-box-edge-deploy-configure-compute.md#add-a-module).
