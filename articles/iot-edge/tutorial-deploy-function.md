---
title: Distribuera Azure-funktion med Azure IoT Edge | Microsoft Docs
description: Distribuera Azure-funktion som en modul till en edge-enhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 0445817f9ff403156025e38a1e14a3892a9a292b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031200"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules---preview"></a>Självstudie: Distribuera Azure Functions som IoT Edge-moduler – förhandsversion

Du kan använda Azure Functions för att distribuera kod som implementerar din affärslogik direkt till dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en Azure-funktion som filtrerar sensordata på simulerade IoT Edge-enheter som du skapade i självstudierna Distribuera Azure IoT Edge på en simulerad enhet i [Windows][lnk-tutorial1-win] eller [Linux][lnk-tutorial1-lin]. I den här guiden får du lära dig hur man:     

> [!div class="checklist"]
> * Använd Visual Studio Code för att skapa en Azure-funktion
> * Använd VS Code och Docker för att skapa en Docker-avbildning och publicera den till ett containerregister 
> * Distribuera modulen från containerregistret till din IoT Edge-enhet
> * Visa filtrerade data

>[!NOTE]
>Azure Function-moduler på Azure IoT Edge är i offentlig [förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Azure-funktionen som du skapar i den här självstudien filtrerar temperaturdata som genereras av din enhet och skickar enbart meddelanden uppströms till Azure IoT Hub när temperaturen är högre än det angivna tröskelvärdet. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill testa Functions-moduler som du skapar i den här självstudien behöver du en IoT Edge-enhet. Du kan använda enheten som du konfigurerade i snabbstarten för [Linux](quickstart-linux.md) eller [Windows](quickstart.md).

Utvecklingsdatorn behöver ha följande förutsättningar: 
* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# för Visual Studio Code-tillägget (tillhandahålls av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) för Visual Studio Code.
* [Azure IoT Edge-tillägg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) för Visual Studio Code. 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) på utvecklingsdatorn. 

## <a name="create-a-container-registry"></a>Skapa ett behållarregister
I den här självstudien använder du Azure IoT Edge-tillägget för VS Code för att skapa en modul och skapa en **behållaravbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.  

Du kan använda valfritt Docker-kompatibelt register för den här självstudien. Två populära Docker-registertjänster som finns tillgängliga i molnet är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). I den här kursen använder vi Azure Container Registry. 

1. I [Azure Portal](https://portal.azure.com) väljer du **Skapa en resurs** > **Container** > **Containerregister**.

    ![create container registry](./media/tutorial-deploy-function/create-container-registry.png)

2. Namnge registret och välj en prenumeration.
3. För resursgruppen rekommenderar vi att du använder det resursgruppsnamn som innehåller din IoT Hub. Genom att lägga alla resurser i en grupp, kan du hantera dem tillsammans. Till exempel tas alla testresurser som ingår i gruppen bort om resursgruppen för testning tas bort. 
4. Sätt SKU:n till **Basic** och ändra **Administratörsanvändare** till **Aktivera**. 
5. Klicka på **Skapa**.
6. När du har skapat ditt behållarregister, navigerar du till det och väljer **Åtkomstnycklar**. 
7. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du kommer att använda de här värdena senare i självstudien. 

## <a name="create-a-function-project"></a>Skapa ett funktionsprojekt
Följande steg visar hur du skapar en IoT Edge-funktion med Visual Studio Code och Azure IoT Edge-tillägget.

1. Öppna Visual Studio Code.
2. Välj **Visa** > **Integrerad terminal** för att öppna den VS Code-integrerade terminalen. 
2. Öppna kommandopaletten för VS Code genom att välja **Visa** > **Kommandopalett**.
3. Ange och kör kommandot **Azure: Logga in** i kommandopaletten och följ anvisningarna för att logga in med ditt Azure-konto. Om du redan har loggat in kan du hoppa över det här steget.
3. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) i kommandopaletten. Ange följande information i kommandopaletten för att skapa din lösning: 

   1. Välj den mapp som du vill skapa lösningen i. 
   2. Ange ett namn för din lösning eller välj standardnamnet **EdgeSolution**.
   3. Välj **Azure Functions – C#** som mall för modulen. 
   4. Ge modulen namnet **CSharpFunction**. 
   5. Ange det Azure Container Registry som du skapade i föregående avsnitt som avbildningslagringsplats för din första modul. Ersätt **localhost:5000** med serverinloggningsvärdet som du kopierade. Den slutliga strängen ser ut så här: **\<registernamn\>.azurecr.io/csharpfunction**.

4. VS Code läser in arbetsytan för IoT Edge-lösningen. Det finns en **.vscode**-mapp, en **modules**-mapp, en mallfil för distributionsmanifestet och en **.env**-fil. Öppna **modules** > **CSharpFunction** > **EdgeHubTrigger-Csharp** > **run.csx**.

5. Ersätt innehållet i filen med följande kod:

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
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
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

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod till CSharpFunction som filtrerar ut meddelanden om att temperaturen för den rapporterade datorn ligger under det godkända tröskelvärdet. Nu behöver du bygga lösningen som en containeravbildning och push-överföra den till ditt containerregister.

1. Logga in på Docker genom att ange följande kommando i den integrerade terminalen för Visual Studio Code, så att du kan push-överföra modulavbildningen till ACR: 
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Använd det användarnamn och den inloggningsserver som du kopierade från Azure Container Registry när du kopierade den. Du kommer uppmanas att ange lösenordet. Klistra in lösenordet och tryck på **Retur**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. Öppna filen **deployment.template.json** i arbetsytan för IoT Edge-lösningen i VS Code-utforskaren. Den här filen talar om för IoT Edge-körningen vilka moduler som ska distribueras till en enhet. Läs mer om distributionsmanifest i avsnittet om att [förstå hur IoT Edge-moduler kan användas, konfigureras och återanvändas](module-composition.md).

3. Leta upp avsnittet **registryCredentials** i distributionsmanifestet. Uppdatera **användarnamn**, **lösenord** och **adress** med autentiseringsuppgifterna från containerregistret. Det här avsnittet ger IoT Edge-körningen på din enhet behörighet att hämta containeravbildningen som lagras i ditt privata register. De faktiska paren för användarnamn och lösenord lagras i env-filen, som git ignorerar.

5. Spara filen.

6. Högerklicka på filen **deployment.template.json** och välj **Skapa IoT Edge-lösning** i VS Code-utforskaren. 

När du ger Visual Studio Code kommando att skapa din lösning hämtar den först information i mallen för distribution och genererar en `deployment.json`-fil i en ny **config**-mapp. Sedan körs två kommandon i en integrerad terminal: `docker build` och `docker push`. Dessa två kommandon skapar koden, lägger funktionerna i behållare och push-överför den till containerregistret som du angav när du initierade lösningen. 

## <a name="view-your-container-image"></a>Visa din containeravbildning

Visual Studio Code matar ut ett meddelande om lyckad överföring när containeravbildningen har push-överförts till containerregistret. Om du själv vill kontrollera att åtgärden lyckades kan du visa avbildningen i registret. 

1. Navigera till ditt Azure containerregister i Azure Portal. 
2. Välj **Lagringsplatser**.
3. Du bör se **csharpfunction** som listas i dina lagringsplatser. Välj lagringsplats för att se mer information.
4. I avsnittet **Taggar** bör du se **0.0.1-amd64**. Den här taggen visar version och plattform för avbildningen du skapade. Dessa värden anges i filen **module.json** i mappen CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Distribuera och kör lösningen

Du kan använda Azure Portal för att distribuera dina Functions-moduler till en IoT Edge-enhet, som du gjorde i snabbstarten, men du kan också distribuera och övervaka moduler från Visual Studio Code. I följande avsnitt använder du Azure IoT Edge-filnamnstillägget för VS Code som listades under förutsättningarna. Installera det nu om du inte redan har gjort det. 

1. Öppna kommandopaletten för VS Code genom att välja **Visa** > **Kommandopalett**.

2. Sök efter och kör kommandot **Azure: Logga in**. Följ anvisningarna för att logga in på ditt Azure-konto. 

3. Leta upp kommandot **Azure IoT Hub: Välj IoT** i kommandopaletten och kör det. 

4. Välj den prenumeration som innehåller din IoT Hub och välj sedan den IoT Hub du vill komma åt.

5. I VS Code-utforskaren expanderar du avsnittet **Azure IoT Hub-enheter**. 

6. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Skapa distribution för IoT Edge-enhet**. 

7. Navigera till lösningsmappen som innehåller CSharpFunction. Öppna mappen **config** och välj filen **deployment.json**. Klicka på **Välj distributionsmanifest för Edge**.

8. Uppdatera avsnittet **Azure IoT Hub-enheter**. Du borde se den nya **CSharpFunction** köras tillsammans med **TempSensor**-modulen och **$edgeAgent** och **$edgeHub**. 

   ![Visa distribuerade moduler i VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Visa genererade data

Du kan se alla meddelanden som kommer in till din IoT Hub genom att köra **Azure IoT Hub: Start Monitoring D2C Message** (Azure IoT Hub: Starta övervakning av D2C-meddelande) i kommandopaletten.

Du kan också filtrera för att visa alla meddelanden som kommer in till IoT Hub från en specifik enhet. Högerklicka på enheten i avsnittet för **Azure IoT Hub-enheter** och välj **Start Monitoring D2C Messages** (Starta övervakning av D2C-meddelande).

Om du vill stoppa meddelandeövervakningen kör du kommandot **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: Sluta övervaka D2C-meddelande) i kommandopaletten. 


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)]

Ta bort IoT Edge-servicekörning baserat på din IoT-enhetsplattform (Linux eller Windows).

#### <a name="windows"></a>Windows

Ta bort IoT Edge-körningen.

```Powershell
stop-service iotedge -NoWait
sleep 5
sc.exe delete iotedge
```

Ta bort de behållare som har skapats på enheten. 

```Powershell
docker rm -f $(docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

#### <a name="linux"></a>Linux

Ta bort IoT Edge-körningen.

```bash
sudo apt-get remove --purge iotedge
```

Ta bort de behållare som har skapats på enheten. 

```bash
sudo docker rm -f $(sudo docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

Ta bort körmiljön för behållaren.

```bash
sudo apt-get remove --purge moby
```



## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en Azure Functions-modul som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att skapa egna moduler kan du läsa mer om hur du [utvecklar Azure Functions med Azure IoT Edge för Visual Studio Code](how-to-develop-csharp-function.md). 

Fortsätt med någon av följande självstudier om du vill veta mer hur Azure IoT Edge kan hjälpa dig att omvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Hitta medelvärden med ett flytande fönster i Azure Stream Analytics](tutorial-deploy-stream-analytics.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
