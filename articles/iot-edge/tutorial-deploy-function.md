---
title: Distribuera Azure-funktioner med Azure IoT Edge | Microsoft Docs
description: Distribuera en Azure-funktion som en modul till en kantenhet.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3f3ba0ccb1cb8961344b605e7ec386b6d6692262
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006885"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules-preview"></a>Självstudie: Distribuera Azure-funktioner som IoT Edge-moduler (förhandsversion)

Du kan använda Azure Functions till att distribuera kod som implementerar din affärslogik direkt till dina Azure IoT Edge-enheter. I den här självstudien får du hjälp att skapa och distribuera en Azure-funktion som filtrerar sensordata för den simulerade IoT Edge-enheten. Du använder den simulerade IoT Edge-enheten som du skapade i snabbstarten Distribuera Azure IoT Edge på en simulerad enhet i [Windows][lnk-tutorial1-win] eller [Linux][lnk-tutorial1-lin]. I den här guiden får du lära dig att:     

> [!div class="checklist"]
> * använda Visual Studio Code till att skapa en Azure-funktion
> * använda VS Code och Docker till att skapa en Docker-avbildning och publicera den till ett containerregister
> * distribuera modulen från containerregistret till din IoT Edge-enhet
> * visa filtrerade data.

>[!NOTE]
>Azure-funktionsmoduler i Azure IoT Edge är i offentlig [förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Den Azure-funktion du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Funktionen skickar enbart meddelanden uppströms till Azure IoT Hub om temperaturen överskrider ett angivet tröskelvärde. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill testa funktionsmodulen du skapar i den här självstudien behöver du en IoT Edge-enhet. Du kan använda enheten som du konfigurerade i snabbstarten för [Linux](quickstart-linux.md) eller [Windows](quickstart.md).

Du måste ha följande på utvecklingsdatorn: 
* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Azure IoT Edge-tillägg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) för Visual Studio Code. 
* [The .NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) på utvecklingsdatorn. 

## <a name="create-a-container-registry"></a>Skapa ett containerregister
I den här självstudien använder du Azure IoT Edge-tillägget för VS Code för att skapa en modul och skapa en **containeravbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.  

Du kan använda valfritt Docker-kompatibelt register för den här självstudien. Två populära Docker-registertjänster som är tillgängliga i molnet är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). I den här kursen använder vi Azure Container Registry. 

1. I [Azure Portal](https://portal.azure.com) väljer du **Skapa en resurs** > **Container** > **Containerregister**.

    ![Skapa ett containerregister](./media/tutorial-deploy-function/create-container-registry.png)

2. Ange ett namn för registret och välj en prenumeration.
3. För resursgruppen rekommenderar vi att du använder det resursgruppsnamn som innehåller din IoT-hubb. Genom att samla alla resurser i samma grupp kan du hantera dem tillsammans. När du till exempel tar bort den resursgrupp som används till testningen tas alla testresurser i gruppen också bort. 
4. Sätt SKU:n till **Basic** och ändra **Administratörsanvändare** till **Aktivera**. 
5. Välj **Skapa**.
6. När du har skapat containerregistret går du till det och väljer **Åtkomstnycklar**. 
7. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du kommer att använda de här värdena senare i självstudien. 

## <a name="create-a-function-project"></a>Skapa ett funktionsprojekt
Följande steg skapar en IoT Edge-funktion med Visual Studio Code och Azure IoT Edge-tillägget.

1. Öppna Visual Studio Code.
2. Välj **Visa** > **Integrerad terminal** för att öppna den integrerade terminalen i VS Code. 
2. Öppna kommandopaletten för VS Code genom att välja **Visa** > **Kommandopalett**.
3. Skriv och kör kommandot **Azure: Logga in** i kommandopaletten. Följ anvisningarna för att logga in på ditt Azure-konto. Om du redan är inloggad kan du hoppa över det här steget.
3. Skriv och kör kommandot **Azure IoT Edge: Ny IoT Edge-lösning** i kommandopaletten. Ange följande information i kommandopaletten för att skapa din lösning: 

   1. Välj den mapp där du vill skapa lösningen. 
   2. Ange ett namn för din lösning eller välj standardnamnet **EdgeSolution**.
   3. Välj **Azure Functions – C#** som mall för modulen. 
   4. Ge modulen namnet **CSharpFunction**. 
   5. Ange det Azure-containerregister du skapade i föregående avsnitt som avbildningslagringsplats för den första modulen. Ersätt **localhost:5000** med det serverinloggningsvärde som du kopierade. Den slutliga strängen ser ut så här: \<registernamn\>.azurecr.io/csharpfunction.

4. VS Code-fönstret läser in IoT Edge-lösningens arbetsyta: en \.vscode-mapp, modulmapp, en mallfil för distributionsmanifestet och en \.env-fil. I VS Code-utforskaren öppnar du **moduler** > **CSharpFunction** > **EdgeHubTrigger-Csharp** > **run.csx**.

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

1. Logga in på Docker genom att ange följande kommando i den integrerade Visual Studio Code-terminalen: Sedan kan du skicka modulavbildningen till ditt Azure-containerregister: 
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Använd det användarnamn och den inloggningsserver som du kopierade från Azure-containerregistret när du kopierade den. Du uppmanas att ange lösenordet. Klistra in lösenordet och tryck på **Retur**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. I VS Code-utforskaren öppnar du filen deployment.template.json i arbetsytan för IoT Edge-lösningen. Den här filen talar om för IoT Edge-körningen vilka moduler som ska distribueras till en enhet. Läs mer om distributionsmanifest i avsnittet om att [förstå hur IoT Edge-moduler kan användas, konfigureras och återanvändas](module-composition.md).

3. Leta upp avsnittet **registryCredentials** i distributionsmanifestet. Uppdatera **användarnamn**, **lösenord** och **adress** med autentiseringsuppgifterna från containerregistret. Det här avsnittet ger IoT Edge-körningen på din enhet behörighet att hämta containeravbildningen som lagras i ditt privata register. Själva användarnamns- och lösenordsparen lagras i .env-filen, som ignoreras av Git.

5. Spara filen.

6. Högerklicka på filen deployment.template.json och välj **Skapa IoT Edge-lösning** i VS Code-utforskaren. 

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

6. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Skapa distribution för IoT Edge-enhet**. 

7. Bläddra till lösningsmappen som innehåller **CSharpFunction**. Öppna mappen config, välj filen deployment.json och välj sedan på **Välj distributionsmanifest för Edge**.

8. Uppdatera avsnittet **Azure IoT Hub-enheter**. Du borde se den nya **CSharpFunction** köras tillsammans med **TempSensor**-modulen och **$edgeAgent** och **$edgeHub**. 

   ![Visa distribuerade moduler i VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Visa genererade data

Du kan se alla meddelanden som kommer till din IoT-hubb genom att köra **Azure IoT Hub: Starta övervakning av D2C-meddelande** i kommandopaletten.

Du kan också filtrera vyn för att visa alla meddelanden som kommer till IoT-hubben från en viss enhet. Högerklicka på enheten i avsnittet för **Azure IoT Hub-enheter** och välj **Start Monitoring D2C Messages** (Starta övervakning av D2C-meddelande).

Om du vill stoppa meddelandeövervakningen kör du kommandot **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: Sluta övervaka D2C-meddelande) i kommandopaletten. 


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)]

Ta bort körningsmiljön för IoT Edge-tjänsten som baseras på din IoT-enhetsplattform (Linux eller Windows).

#### <a name="windows"></a>Windows

Ta bort IoT Edge-körningen.

```Powershell
stop-service iotedge -NoWait
sleep 5
sc.exe delete iotedge
```

Ta bort de containrar som har skapats på enheten. 

```Powershell
docker rm -f $(docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

#### <a name="linux"></a>Linux

Ta bort IoT Edge-körningen.

```bash
sudo apt-get remove --purge iotedge
```

Ta bort de containrar som har skapats på enheten. 

```bash
sudo docker rm -f $(sudo docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

Ta bort körmiljön för containern.

```bash
sudo apt-get remove --purge moby
```



## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en Azure-funktionsmodul med kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att skapa egna moduler kan du läsa mer om hur du [utvecklar Azure-funktioner med Azure IoT Edge för Visual Studio Code](how-to-develop-csharp-function.md). 

Fortsätt med någon av följande självstudier om du vill lära dig mer om hur Azure IoT Edge kan hjälpa dig att omvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Hitta medelvärden med hjälp av ett flytande fönster i Azure Stream Analytics](tutorial-deploy-stream-analytics.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
