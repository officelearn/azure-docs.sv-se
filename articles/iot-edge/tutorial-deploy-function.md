---
title: Distribuera Azure-funktioner med Azure IoT Edge | Microsoft Docs
description: I den här självstudien distribuerar du en Azure-funktion som en modul till en gränsenhet.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 66dcb3347741c4826fc62cc258c67b85f3939ff7
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47422770"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules-preview"></a>Självstudie: Distribuera Azure-funktioner som IoT Edge-moduler (förhandsversion)

Du kan använda Azure Functions till att distribuera kod som implementerar din affärslogik direkt till dina Azure IoT Edge-enheter. I den här självstudien får du hjälp att skapa och distribuera en Azure-funktion som filtrerar sensordata för den simulerade IoT Edge-enheten. Du använder den simulerade IoT Edge-enheten som du skapade i snabbstarten Distribuera Azure IoT Edge på en simulerad enhet i [Windows][lnk-tutorial1-win] eller [Linux][lnk-tutorial1-lin]. I den här guiden får du lära dig att:     

> [!div class="checklist"]
> * använda Visual Studio Code till att skapa en Azure-funktion
> * använda VS Code och Docker till att skapa en Docker-avbildning och publicera den till ett containerregister
> * distribuera modulen från containerregistret till din IoT Edge-enhet
> * visa filtrerade data.

<center>
![Arkitekturdiagram för självstudien](./media/tutorial-deploy-function/FunctionsTutDiagram.png)
</center>

>[!NOTE]
>Azure-funktionsmoduler i Azure IoT Edge är tillgängliga som en [offentlig förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Den Azure-funktion du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Funktionen skickar enbart meddelanden uppströms till Azure IoT Hub om temperaturen överskrider ett angivet tröskelvärde. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

En Azure IoT Edge-enhet:

* Du kan använda utvecklingsdatorn eller en virtuell dator som en gränsenhet genom att följa stegen i snabbstarten för [Linux-](quickstart-linux.md) eller [Windows-enheter](quickstart.md).

Molnresurser:

* En [IoT-hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå i Azure. 

Utvecklingsresurser:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Azure IoT Edge-tillägg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) för Visual Studio Code. 
* [The .NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Skapa ett containerregister

I den här självstudien använder du Azure IoT Edge-tillägget för VS Code för att skapa en modul och skapa en **containeravbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.  

Du kan använda valfritt Docker-kompatibelt register för den här självstudien. Två populära Docker-registertjänster som är tillgängliga i molnet är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). I den här kursen använder vi Azure Container Registry. 

1. I [Azure Portal](https://portal.azure.com) väljer du **Skapa en resurs** > **Container** > **Containerregister**.

    ![Skapa ett containerregister](./media/tutorial-deploy-function/create-container-registry.png)

2. Skapa containerregistret genom att ange följande värden:

   | Fält | Värde | 
   | ----- | ----- |
   | Registernamn | Ange ett unikt namn. |
   | Prenumeration | Välj en prenumeration i listrutan. |
   | Resursgrupp | Vi rekommenderar att du använder samma resursgrupp för alla testresurser som du skapar i snabbstarterna och självstudierna om IoT Edge. Till exempel **IoTEdgeResources**. |
   | Plats | Välj en plats i närheten av dig. |
   | Administratörsanvändare | Ändra värdet till **Aktivera**. |
   | SKU | Välj **Grundläggande**. | 

5. Välj **Skapa**.

6. När du har skapat containerregistret går du till det och väljer **Åtkomstnycklar**. 

7. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du kommer att använda de här värdena senare i självstudien. 

## <a name="create-a-function-project"></a>Skapa ett funktionsprojekt

Azure IoT Edge-tillägget för Visual Studio Code som du installerade i avsnittet med systemkrav och förutsättningar innehåller hanteringsfunktioner och några kodmallar. I det här avsnittet använder du Visual Studio Code för att skapa en IoT Edge-lösning som innehåller en Azure-funktion. 

1. Öppna Visual Studio Code på utvecklingsdatorn.

2. Öppna kommandopaletten för VS Code genom att välja **Visa** > **Kommandopalett**.

3. Skriv och kör kommandot **Azure: Logga in** i kommandopaletten. Följ anvisningarna för att logga in på ditt Azure-konto.

4. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) på kommandopaletten. Skapa lösningen genom att följ anvisningarna på kommandopaletten.

   1. Välj den mapp där du vill skapa lösningen. 
   2. Ange ett namn för din lösning eller välj standardnamnet **EdgeSolution**.
   3. Välj **Azure Functions – C#** som mall för modulen. 
   4. Ge modulen namnet **CSharpFunction**. 
   5. Ange det Azure-containerregister du skapade i föregående avsnitt som avbildningslagringsplats för den första modulen. Ersätt **localhost:5000** med det serverinloggningsvärde som du kopierade. Den slutliga strängen ser ut så här: \<registernamn\>.azurecr.io/csharpfunction.

   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-deploy-function/repository.png)

4. VS Code-fönstret läser in IoT Edge-lösningens arbetsyta: en \.vscode-mapp, modulmapp, en mallfil för distributionsmanifestet och en \.env-fil. I VS Code-utforskaren öppnar du **moduler** > **CSharpFunction** > **EdgeHubTrigger-Csharp** > **run.csx**.

5. Ersätt innehållet i filen **run.csx** med följande kod:

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold.
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object.
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
                // Add a new property to the message to indicate it is an alert.
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message.       
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages.
    class MessageBody
    {
        public Machine machine {get; set;}
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

6. Spara filen.

## <a name="build-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod i **CSharpFunction** som filtrerar ut meddelanden om att temperaturen för den rapporterade datorn ligger under det godkända tröskelvärdet. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

I det här avsnittet anger du autentiseringsuppgifterna för ditt containerregister två gånger. Den första gången är för att logga in lokalt från utvecklingsdatorn så att Visual Studio Code kan överföra avbildningar till ditt register. Den andra gången gör du det i **.env**-filen för IoT Edge-lösningen, vilket ger IoT-Edge enheten behörighet att hämta avbildningar från ditt register. 

1. Öppna den VS Code-integrerade terminalen genom att välja **Visa** > **Terminal**. 

1. Logga in i ditt containerregister genom att ange följande kommando i den integrerade terminalen. Sedan kan du skicka modulavbildningen till ditt Azure-containerregister: 
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Använd det användarnamn och den inloggningsserver som du kopierade från Azure-containerregistret när du kopierade den. När du uppmanas att ange lösenordet klistra du in lösenordet för containerregistret och trycker på **Retur**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. I VS Code-utforskaren öppnar du filen **deployment.template.json** i arbetsytan för IoT Edge-lösningen. Den här filen talar om för IoT Edge-körningen vilka moduler som ska distribueras till en enhet. Observera att din funktionsmodul, **CSharpFunction**, visas tillsammans med modulen **tempSensor** som tillhandahåller testdata. Läs mer om distributionsmanifest i avsnittet om att [förstå hur IoT Edge-moduler kan användas, konfigureras och återanvändas](module-composition.md).

   ![Visa din modul i distributionsmanifestet](./media/tutorial-deploy-function/deployment-template.png)

3. Öppna **.env**-filen på arbetsytan för IoT Edge-lösningen. Den här git-ignored-filen lagrar dina autentiseringsuppgifter för containerregistret så att du inte behöver placera dem i mallen för distributionsmanifestet. Ange **username** och **password** för containerregistret. 

5. Spara filen.

6. I VS Code-utforskaren högerklickar du på filen deployment.template.json och väljer **Build and Push IoT Edge solution** (Skapa och skicka IoT Edge-lösning). 

När du instruerar Visual Studio Code att skapa din lösning hämtar den först information från distributionsmallen och genererar en .json-distributionsfil i en ny mapp med namnet **config**. Sedan körs två kommandon i en integrerad terminal: `docker build` och `docker push`. Dessa två kommandon skapar koden, lägger funktionerna i container och push-överför sedan koden till containerregistret du angav när du initierade lösningen. 

## <a name="view-your-container-image"></a>Visa din containeravbildning

Visual Studio Code matar ut ett meddelande om lyckad överföring när containeravbildningen har push-överförts till containerregistret. Om du själv vill kontrollera att åtgärden lyckades kan du visa avbildningen i registret. 

1. Bläddra till ditt Azure containerregister i Azure Portal. 
2. Välj **Lagringsplatser**.
3. Du bör se lagringsplatsen **csharpfunction** i listan. Välj den här lagringsplatsen för att se mer information.
4. I avsnittet **Taggar** bör du se taggen **0.0.1-amd64**. Den här taggen anger version och plattform för avbildningen du skapade. Dessa värden anges i filen module.json i mappen CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Distribuera och kör lösningen

Du kan använda Azure Portal till att distribuera din funktionsmodul till en IoT Edge-enhet, precis som du gjorde i snabbstarterna. Du kan också distribuera och övervaka moduler från Visual Studio Code. I följande avsnitt använder du Azure IoT Edge-filnamnstillägget för VS Code som listades under förutsättningarna. Installera tillägget nu om du inte redan har gjort det. 

1. Öppna kommandopaletten för VS Code genom att välja **Visa** > **Kommandopalett**.

2. Sök efter och kör kommandot **Azure: Logga in**. Följ anvisningarna för att logga in på ditt Azure-konto. 

3. Leta upp kommandot **Azure IoT Hub: Välj IoT** i kommandopaletten och kör det. 

4. Välj den prenumeration som innehåller din IoT-hubb och välj sedan den IoT-hubb du vill komma åt.

5. I VS Code-utforskaren expanderar du avsnittet **Azure IoT Hub-enheter**. 

6. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Create Deployment for Single Device** (Skapa distribution för enskild enhet). 

7. Bläddra till lösningsmappen som innehåller **CSharpFunction**. Öppna mappen config, välj filen deployment.json och välj sedan på **Välj distributionsmanifest för Edge**.

8. Uppdatera avsnittet **Azure IoT Hub-enheter**. Du borde se den nya **CSharpFunction** köras tillsammans med **TempSensor**-modulen och **$edgeAgent** och **$edgeHub**. 

   ![Visa distribuerade moduler i VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Visa genererade data

Du kan se alla meddelanden som kommer till din IoT-hubb genom att köra **Azure IoT Hub: Starta övervakning av D2C-meddelande** i kommandopaletten.

Du kan också filtrera vyn för att visa alla meddelanden som kommer till IoT-hubben från en viss enhet. Högerklicka på enheten i avsnittet för **Azure IoT Hub-enheter** och välj **Start Monitoring D2C Messages** (Starta övervakning av D2C-meddelande).

Om du vill stoppa meddelandeövervakningen kör du kommandot **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: Sluta övervaka D2C-meddelande) i kommandopaletten. 


## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet. 

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en Azure-funktionsmodul med kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att skapa egna moduler kan du läsa mer om hur du [utvecklar Azure-funktioner med Azure IoT Edge för Visual Studio Code](how-to-develop-csharp-function.md). 

Fortsätt med någon av följande självstudier om du vill lära dig mer om hur Azure IoT Edge kan hjälpa dig att omvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Hitta medelvärden med hjälp av ett flytande fönster i Azure Stream Analytics](tutorial-deploy-stream-analytics.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
