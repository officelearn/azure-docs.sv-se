---
title: 'Självstudie: Distribuera en Azure-funktion till en enhet – Azure IoT Edge | Microsoft Docs'
description: I den här självstudien utvecklar du en Azure-funktion som en IoT Edge-modul och distribuerar den sedan till en gränsenhet.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 1fba2c4e5191d4c827035362a8eb6876fcbb67cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611904"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Självstudie: Distribuera Azure Functions som IoT Edge-moduler

Du kan använda Azure Functions till att distribuera kod som implementerar din affärslogik direkt till dina Azure IoT Edge-enheter. I den här självstudien får du hjälp att skapa och distribuera en Azure-funktion som filtrerar sensordata för den simulerade IoT Edge-enheten. Du använder den simulerade IoT Edge-enheten som du skapade i snabbstarten Distribuera Azure IoT Edge på en simulerad enhet i [Windows](quickstart.md) eller [Linux](quickstart-linux.md). I den här guiden får du lära dig att:     

> [!div class="checklist"]
> * använda Visual Studio Code till att skapa en Azure-funktion
> * använda VS Code och Docker till att skapa en Docker-avbildning och publicera den till ett containerregister
> * distribuera modulen från containerregistret till din IoT Edge-enhet
> * visa filtrerade data.

<center>

![Diagram - självstudie arkitektur mellanlagra och distribuera funktionsmodul](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>Azure-funktionsmoduler i Azure IoT Edge är tillgängliga som en [offentlig förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Den Azure-funktion du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Funktionen skickar enbart meddelanden uppströms till Azure IoT Hub om temperaturen överskrider ett angivet tröskelvärde. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

En Azure IoT Edge-enhet:

* Du kan konfigurera utvecklingsdatorn eller en virtuell dator som gränsenhet genom att följa stegen i snabbstarten för [Linux-](quickstart-linux.md) eller [Windows-enheter](quickstart.md).

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure. 

Utvecklingsresurser:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Azure IoT-verktyg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools). 
* [The .NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Skapa ett containerregister

I den här självstudien använder du Azure IoT-verktyg för Visual Studio Code för att skapa en modul och en **containeravbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.  

Du kan använda valfritt Docker-kompatibelt register för att lagra dina containeravbildningar. Två populära Docker-registertjänster är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). I den här kursen använder vi Azure Container Registry. 

1. I [Azure Portal](https://portal.azure.com) väljer du **Skapa en resurs** > **Container** > **Containerregister**.

    ![Skapa ett containerregister med Azure-portalen](./media/tutorial-deploy-function/create-container-registry.png)

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

7. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du kan använda dessa värden senare i självstudien för att ge åtkomst till containerregistret. 

## <a name="create-a-function-project"></a>Skapa ett funktionsprojekt

Azure IoT-verktyg för Visual Studio Code som du installerade i avsnittet med systemkrav och förutsättningar innehåller hanteringsfunktioner och några kodmallar. I det här avsnittet använder du Visual Studio Code för att skapa en IoT Edge-lösning som innehåller en Azure-funktion. 

1. Öppna Visual Studio Code på utvecklingsdatorn.

2. Öppna kommandopaletten för VS Code genom att välja **Visa** > **Kommandopalett**.

3. Skriv och kör kommandot **Azure IoT Edge på kommandopaletten: New IoT Edge solution** (Ny IoT Edge-lösning). Skapa lösningen genom att följ anvisningarna på kommandopaletten.

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen, till exempel **FunctionSolution**, eller acceptera standardnamnet. |
   | Välj modulmall | Välj **Azure Functions - C#**. |
   | Ange ett modulnamn | Ge modulen namnet **CSharpFunction**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen har fyllts i från föregående steg. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. Den slutlgiltiga strängen ser ut så här: \<registernamn\>.azurecr.io/CSharpFunction. |

   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-deploy-function/repository.png)

4. VS Code-fönstret läser in IoT Edge-lösningens arbetsyta: en \.vscode-mapp, modulmapp, en mallfil för distributionsmanifestet och en \.env-fil. I VS Code-utforskaren öppnar du **moduler** > **CSharpFunction** > **CSharpFunction.cs**.

5. Ersätt innehållet i filen **CSharpFunction.cs** med följande kod. Den här koden tar emot telemetri om omgivande temperatur och datortemperatur och vidarebefordrar enbart meddelandet till IoT Hub om datortemperaturen överstiger ett angivet tröskelvärde.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       var filteredMessage = new Message(messageBytes);
                       // Copy the properties of the original message into the new Message object.
                       foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                       {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                       // Add a new property to the message to indicate it is an alert.
                       filteredMessage.Properties.Add("MessageType", "Alert");
                       // Send the message.       
                       await output.AddAsync(filteredMessage);
                       logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                   }
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
   }
   ```

6. Spara filen.

## <a name="build-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod i **CSharpFunction** som filtrerar ut meddelanden om att temperaturen för den rapporterade datorn ligger under det godkända tröskelvärdet. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

I det här avsnittet anger du autentiseringsuppgifterna för ditt containerregister två gånger. Den första gången är för att logga in lokalt från utvecklingsdatorn så att Visual Studio Code kan överföra avbildningar till ditt register. Den andra gången gör du det i **.env**-filen för IoT Edge-lösningen, vilket ger IoT-Edge enheten behörighet att hämta avbildningar från ditt register. 

1. Öppna den VS Code-integrerade terminalen genom att välja **Visa** > **Terminal**. 

2. Logga in i ditt containerregister genom att ange följande kommando i den integrerade terminalen. Använd det användarnamn och den inloggningsserver som du kopierade från Azure-containerregistret när du kopierade den.
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    När du uppmanas att ange lösenordet klistra du in lösenordet för containerregistret och trycker på **Retur**.

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

Du kan använda Azure Portal till att distribuera din funktionsmodul till en IoT Edge-enhet, precis som du gjorde i snabbstarterna. Du kan också distribuera och övervaka moduler från Visual Studio Code. I följande avsnitt använder du Azure IoT-verktygen för VS Code som angavs i förhandskraven. Installera tillägget nu om du inte redan har gjort det. 

1. Öppna kommandopaletten för VS Code genom att välja **Visa** > **Kommandopalett**.

2. Sök efter och kör kommandot  **i Azure: Logga in**. Följ anvisningarna för att logga in på ditt Azure-konto. 

3. Leta upp kommandot **Azure IoT Hub i kommandopaletten och kör det: Välj IoT Hub**. 

4. Välj den prenumeration som innehåller din IoT-hubb och välj sedan den IoT-hubb du vill komma åt.

5. I VS Code-utforskaren expanderar du avsnittet **Azure IoT Hub-enheter**. 

6. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Create Deployment for Single Device** (Skapa distribution för enskild enhet). 

7. Bläddra till lösningsmappen som innehåller **CSharpFunction**. Öppna mappen config, välj filen **deployment.json** och välj sedan på **Välj distributionsmanifest för Edge**.

8. Uppdatera avsnittet **Azure IoT Hub-enheter**. Du borde se den nya **CSharpFunction** köras tillsammans med **TempSensor**-modulen och **$edgeAgent** och **$edgeHub**. Det kan ta en stund för de nya modulerna att visas. IoT Edge-enheten måste hämta sin nya distributionsinformation från IoT Hub, starta de nya containrarna och rapportera statusen tillbaka till IoT Hub. 

   ![Visa distribuerade moduler i VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Visa genererade data

Du kan se alla meddelanden som kommer till din IoT-hubb genom att köra **Azure IoT Hub: Börja övervaka D2C-meddelande** i kommandopaletten.

Du kan också filtrera vyn för att visa alla meddelanden som kommer till IoT-hubben från en viss enhet. Högerklicka på enheten i avsnittet för **Azure IoT Hub-enheter** och välj **Start Monitoring D2C Messages** (Starta övervakning av D2C-meddelande).

Om du vill stoppa övervakningen av meddelanden kör du kommandot **Azure IoT Hub: Sluta övervaka D2C-meddelande** i kommandopaletten. 


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

