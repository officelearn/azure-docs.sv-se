---
title: 'Självstudiekurs: Distribuera Azure-funktioner som moduler - Azure IoT Edge'
description: I den här självstudien utvecklar du en Azure-funktion som en IoT Edge-modul och distribuerar den sedan till en gränsenhet.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: f909ca12ce080fc5d1241bcc649c041361e405a7
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421167"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Självstudie: Distribuera Azure-funktioner som IoT Edge-moduler

Du kan använda Azure Functions till att distribuera kod som implementerar din affärslogik direkt till dina Azure IoT Edge-enheter. I den här självstudien får du hjälp att skapa och distribuera en Azure-funktion som filtrerar sensordata för den simulerade IoT Edge-enheten. Du använder den simulerade IoT Edge-enheten som du skapade i snabbstarten Distribuera Azure IoT Edge på en simulerad enhet i [Windows](quickstart.md) eller [Linux](quickstart-linux.md). I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * använda Visual Studio Code till att skapa en Azure-funktion
> * använda VS Code och Docker till att skapa en Docker-avbildning och publicera den till ett containerregister
> * distribuera modulen från containerregistret till din IoT Edge-enhet
> * visa filtrerade data.

<center>

![Diagram - Självstudiearkitektur: steg- och distributionsfunktionsmodul](./media/tutorial-deploy-function/functions-architecture.png)
</center>

Den Azure-funktion du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Funktionen skickar enbart meddelanden uppströms till Azure IoT Hub om temperaturen överskrider ett angivet tröskelvärde.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Innan du påbörjar den här självstudien bör du ha gått igenom den tidigare självstudien för att ställa in din utvecklingsmiljö för Linux-containerutveckling: [Utveckla IoT Edge-moduler för Linux-enheter](tutorial-develop-for-linux.md). Genom att fylla i den självstudien bör du ha följande förutsättningar på plats:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En [Linux-enhet som kör Azure IoT Edge](quickstart-linux.md)
* Ett behållarregister, till exempel [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio-kod](https://code.visualstudio.com/) som konfigurerats med [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) konfigurerad för att köra Linux-behållare.

Om du vill utveckla en IoT Edge-modul med Azure Functions installerar du följande ytterligare förutsättningar på utvecklingsmaskinen:

* [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [The .NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Skapa ett funktionsprojekt

Azure IoT-verktyg för Visual Studio Code som du installerade i avsnittet med systemkrav och förutsättningar innehåller hanteringsfunktioner och några kodmallar. I det här avsnittet använder du Visual Studio Code för att skapa en IoT Edge-lösning som innehåller en Azure-funktion.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Skapa en C#-funktionslösningsmall som du kan anpassa med din egen kod.

1. Öppna Visual Studio Code på utvecklingsdatorn.

2. Öppna kommandopaletten VS-kod genom att välja **Visa** > **kommandopalett**.

3. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) på kommandopaletten. Skapa lösningen genom att följ anvisningarna på kommandopaletten.

   | Field | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen, till exempel **FunctionSolution**, eller acceptera standardnamnet. |
   | Välj modulmall | Välj **Azure Functions - C#**. |
   | Ange ett modulnamn | Ge modulen namnet **CSharpFunction**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen har fyllts i från föregående steg. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. Den slutlgiltiga strängen ser ut så här: \<registernamn\>.azurecr.io/CSharpFunction. |

   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerregistret och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten.

1. Öppna .env-filen i VS Code-utforskaren.
2. Uppdatera fälten med det **användarnamn** och **lösenord** som du kopierade från Azure Container-registret.
3. Spara filen.

### <a name="select-your-target-architecture"></a>Välj målarkitektur

För närvarande kan Visual Studio Code utveckla C-moduler för Linux AMD64 och Linux ARM32v7-enheter. Du måste välja vilken arkitektur du riktar dig till med varje lösning, eftersom behållaren är byggd och körs på olika sätt för varje arkitekturtyp. Standard är Linux AMD64.

1. Öppna kommandopaletten och sök efter **Azure IoT Edge: Ange standardmålplattform för edge-lösning**eller välj genvägsikonen i sidofältet längst ned i fönstret.

2. I kommandopaletten väljer du målarkitekturen i listan med alternativ. För den här guiden använder vi en Ubuntu virtuell maskin som IoT Edge-enhet, så kommer att behålla standard **amd64**.

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

Nu ska vi lägga till ytterligare kod så att modulen bearbetar meddelandena vid kanten innan du vidarebefordrar dem till IoT Hub.

1. Öppna moduler > **csharpFunction** > **CSharpFunction.cs i**Visual Studio-kod . **modules**

1. Ersätt innehållet i filen **CSharpFunction.cs** med följande kod. Den här koden tar emot telemetri om omgivande temperatur och datortemperatur och vidarebefordrar enbart meddelandet till IoT Hub om datortemperaturen överstiger ett angivet tröskelvärde.

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
                       // Send the message to the output as the temperature value is greater than the threshold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
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

1. Spara filen.

## <a name="build-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

I föregående avsnitt skapade du en IoT Edge-lösning och ändrade **CSharpFunction** för att filtrera bort meddelanden med rapporterade maskintemperaturer under det acceptabla tröskelvärdet. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

I det här avsnittet anger du autentiseringsuppgifterna för behållarregistret för andra gången (den första fanns i **.env-filen** i IoT Edge-lösningen) genom att logga in lokalt från utvecklingsmaskinen så att Visual Studio Code kan skicka avbildningar till registret.

1. Öppna den integrerade VS-koden-terminalen genom att välja **Visa** > **terminal**.

2. Logga in i ditt containerregister genom att ange följande kommando i den integrerade terminalen. Använd det användarnamn och den inloggningsserver som du kopierade från Azure-containerregistret när du kopierade den.

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    När du uppmanas att ange lösenordet klistrar du in lösenordet (det visas inte i terminalfönstret) för behållarregistret och trycker på **Retur**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

3. Högerklicka på filen deployment.template.json och välj **Build and Push IoT Edge solution** (Skapa och skicka IoT Edge-lösning) i VS Code-utforskaren.

När du talar om för Visual Studio Code att skapa din lösning, tar den först informationen i distributionsmallen och genererar en deployment.json-fil i en ny mapp med namnet **config**. Sedan körs två kommandon i den `docker build` `docker push`integrerade terminalen: och . Build-kommandot skapar din kod och innehåller funktioner. Sedan skickar push-kommandot koden till behållarregistret som du angav när du initierade lösningen.

## <a name="view-your-container-image"></a>Visa din containeravbildning

Visual Studio Code matar ut ett meddelande om lyckad överföring när containeravbildningen har push-överförts till containerregistret. Om du själv vill kontrollera att åtgärden lyckades kan du visa avbildningen i registret.

1. Bläddra till ditt Azure containerregister i Azure Portal.
2. Välj **Lagringsplatser**.
3. Du bör se lagringsplatsen **csharpfunction** i listan. Välj den här lagringsplatsen för att se mer information.
4. I avsnittet **Taggar** bör du se taggen **0.0.1-amd64**. Den här taggen anger version och plattform för avbildningen du skapade. Dessa värden anges i filen module.json i mappen CSharpFunction.

## <a name="deploy-and-run-the-solution"></a>Distribuera och kör lösningen

Du kan använda Azure Portal till att distribuera din funktionsmodul till en IoT Edge-enhet, precis som du gjorde i snabbstarterna. Du kan också distribuera och övervaka moduler från Visual Studio Code. I följande avsnitt använder du Azure IoT-verktygen för VS Code som angavs i förhandskraven. Installera tillägget nu om du inte redan har gjort det.

1. I VS Code-utforskaren expanderar du avsnittet **Azure IoT Hub-enheter**.

2. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Create Deployment for Single Device** (Skapa distribution för enskild enhet).

3. Bläddra till lösningsmappen som innehåller **CSharpFunction**. Öppna mappen config, välj filen **deployment.json** och välj sedan på **Välj distributionsmanifest för Edge**.

4. Uppdatera avsnittet **Azure IoT Hub-enheter**. Du bör se den nya **CSharpFunction** körs tillsammans med **Modulen SimuleradTemperatureSensor** och **$edgeAgent** och **$edgeHub**. Det kan ta en stund för de nya modulerna att visas. IoT Edge-enheten måste hämta sin nya distributionsinformation från IoT Hub, starta de nya containrarna och rapportera statusen tillbaka till IoT Hub.

   ![Visa distribuerade moduler i VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-the-generated-data"></a>Visa genererade data

Du kan se alla meddelanden som kommer fram till din IoT-hubb genom att köra **Azure IoT Hub: Börja övervaka inbyggd händelseslutpunkt i** kommandopaletten.

Du kan också filtrera vyn för att visa alla meddelanden som kommer till IoT-hubben från en viss enhet. Högerklicka på enheten i avsnittet **Azure IoT Hub Devices** och välj Starta övervakning inbyggd **händelseslutpunkt**.

Om du vill sluta övervaka meddelanden kör du kommandot **Azure IoT Hub: Stop Monitoring Built-in Event Endpoint** i kommandopaletten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en Azure-funktionsmodul med kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att skapa egna moduler kan du läsa mer om hur du [utvecklar med Azure IoT Edge för Visual Studio Code](how-to-vs-code-develop-module.md).

Fortsätt med någon av följande självstudier om du vill lära dig mer om hur Azure IoT Edge kan hjälpa dig att omvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Hitta medelvärden med hjälp av ett flytande fönster i Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
