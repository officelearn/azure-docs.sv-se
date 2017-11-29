---
title: Distribuera Azure-funktionen med Azure IoT-Edge | Microsoft Docs
description: Distribuera Azure-funktion som en modul till en insticksenhet
services: iot-edge
keywords: 
author: JimacoMS2
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: cba901e8929d3626dc06e4600437b6d364e9bc44
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Distribuera Azure-funktion som en gräns för IoT-modul - förhandsgranskning
Du kan använda Azure Functions för att distribuera kod som implementerar affärslogiken direkt till IoT Edge-enheter. Den här självstudiekursen vägleder dig genom att skapa och distribuera en Azure-funktion som filtrerar sensordata på simulerade IoT gränsenheten som du skapade i distribuera Azure IoT kanten på en simulerad enhet på [Windows] [ lnk-tutorial1-win]eller [Linux] [ lnk-tutorial1-lin] självstudier. I den här guiden får du lära dig hur man:     

> [!div class="checklist"]
> * Använd Visual Studio-koden för att skapa en Azure-funktion
> * Använd VS-kod och Docker att skapa en Docker-avbildning och publicera i registret 
> * Distribuera modulen till din IoT-Edge-enhet
> * Visa genererade data


Azure-funktion som du skapar i den här självstudiekursen filtrerar temperatur data som genereras av enheten och endast skickar meddelanden uppströms till Azure IoT Hub när temperaturen är högre än angivet tröskelvärde. 

## <a name="prerequisites"></a>Krav

* Azure IoT gränsenheten som du skapade i Snabbstart eller tidigare kursen.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# för Visual Studio Code (drivs av OmniSharp) tillägg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). (Du kan installera tillägget panelen tillägg i Visual Studio Code.)
* [Azure IoT kant-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). (Du kan installera tillägget panelen tillägg i Visual Studio Code.)
* [Docker](https://docs.docker.com/engine/installation/). Community Edition (CE) för din plattform är tillräcklig för den här kursen. 
* [.NET core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 

## <a name="set-up-a-docker-registry"></a>Ställ in en Docker-registret
I kursen får du använder Azure IoT kant-tillägget för VS-kod att skapa en Azure-funktion och skapa en [Docker bild](https://docs.docker.com/glossary/?term=image) med den. Sedan du push Docker avbildningen till en [Docker databasen](https://docs.docker.com/glossary/?term=repository) hos en [Docker registret](https://docs.docker.com/glossary/?term=registry). Slutligen kan du distribuera Docker avbildningen paketeras som en [dockerbehållare](https://docs.docker.com/glossary/?term=container) från registret på enheten IoT kant.  

Du kan använda alla Docker-kompatibel registret för den här självstudiekursen. Två populära Docker registret tjänster som är tillgängliga i molnet är **Azure Container registret** och **Docker-hubb**:

- [Azure Container registret](https://docs.microsoft.com/en-us/azure/container-registry/) är tillgänglig med en [betald prenumeration](https://azure.microsoft.com/en-us/pricing/details/container-registry/). I den här självstudien den **grundläggande** prenumerationen är tillräckliga. 

- [Docker-hubb](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) erbjuder en kostnadsfri privata databas om du registrerar dig för en (kostnadsfritt) Docker-ID. 
    1. Om du vill registrera dig för en Docker-ID, följ instruktionerna i [registrera dig för en Docker-ID](https://docs.docker.com/docker-id/#register-for-a-docker-id) på Docker-webbplatsen. 

    2. Om du vill skapa en privat Docker-databas, följ instruktionerna i [en ny databas skapas på Docker hubben](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub) på Docker-webbplatsen.

I den här kursen i tillämpliga fall finns kommandon för både Azure-behållare registret och Docker-hubb.

## <a name="create-a-function-project"></a>Skapa ett projekt med funktionen
Följande steg visar hur du skapar en IoT-Edge-funktionen med hjälp av Visual Studio Code och Azure IoT kant-tillägget.
1. Öppna VS-kod.
2. Använd den **visa | Integrerad Terminal** menyn för att öppna VS koden integrerad terminal.
3. Ange följande kommando för att installera (eller uppdatera) i terminalen integrerad i **AzureIoTEdgeFunction** mallen i dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. Ange följande kommando för att skapa ett projekt för ny modul i integrerad terminalen:

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction
    ```

    >[!NOTE]
    > Det här kommandot skapar projektmappen, **FilterFunction**, i den aktuella arbetsmappen. Om du vill skapa den på en annan plats, ändra kataloger innan kommandot körs.

3. Använd den **filen | Öppna mappen** menykommandot, bläddra till den **FilterFunction** mappen och klicka på **Välj mapp** öppna projektet i VS-kod.
4. I VS kod explorer klickar du på den **EdgeHubTrigger Csharp** mappen, klicka på den **run.csx** att öppna den.
5. Lägg till följande sats efter den `#r "Microsoft.Azure.Devices.Client"` instruktionen:

    ```csharp
    #r "Newtonsoft.Json"
    ```

5. Lägg till följande using-instruktioner efter den befintliga `using` instruktioner:

    ```csharp
    using Newtonsoft.Json;
    ```

1. Lägg till följande klasser. Dessa klasser definierar det förväntade schemat för brödtexten för inkommande meddelanden.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
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

1. Ersätt innehållet i den **kör** metoden med följande kod. Den kan du filtrera meddelanden baserat på värdet temperatur i själva meddelandet och temperatur tröskelvärdet.

    ```csharp
    const int temperatureThreshold = 25;

    byte[] messageBytes = messageReceived.GetBytes();
    var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

    if (!string.IsNullOrEmpty(messageString))
    {
        // Get the body of the message and deserialize it
        var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);
        
        if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
        {
            // We will send the message to the output as the temperature value is greater than the threashold
            var filteredMessage = new Message(messageBytes);
            // We need to copy the properties of the original message into the new Message object
            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
            {
                filteredMessage.Properties.Add(prop.Key, prop.Value);
            }
            // We are adding a new property to the message to indicate it is an alert
            filteredMessage.Properties.Add("MessageType", "Alert");
            // Send the message        
            await output.AddAsync(filteredMessage);
            log.Info("Received and transferred a message with temperature above the threshold");
        }
    }
    ```

11. Spara filen.

## <a name="publish-a-docker-image"></a>Publicera en Docker-bild

1. Skapa Docker-bild.
    1. Klicka i VS kod explorer den **Docker** mapp för att öppna den. Välj sedan antingen mappen för din plattform för behållaren **linux x64** eller **windows nano**. 
    2. Högerklicka på den **Dockerfile** fil och klicka på **skapa IoT kant modulen Docker bild**. 
    3. I den **Välj mappen** rutan, navigera till projektmappen **FilterFunction**, och klicka på **Välj mapp som EXE_DIR**. 
    4. Ange avbildningens namn i popup-textrutan längst upp i fönstret VS-kod. Till exempel `<docker registry address>/filterfunction:latest`; där *docker registret adress* Docker-ID om du använder Docker-hubb eller liknar `<your registry name>.azurecr.io`, om du använder Azure-behållare registret.
 
4. Logga in på Docker. Ange följande kommando i integrerad terminal: 

    - Docker-hubb (ange dina autentiseringsuppgifter när du uppmanas):
        
        ```csh/sh
        docker login
        ```

    - Azure-behållaren registret:
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        Användarnamn, lösenord och logga in server som ska användas i det här kommandot finns [Azure-portalen] (https://portal.azure.com). Från **alla resurser**, klickar du på panelen för din Azure-behållaren registret för att öppna dess egenskaper och klicka sedan på **åtkomstnycklar**. Kopiera värdena i den **användarnamn**, **lösenord**, och **inloggningsserver** fält. Inloggningen server sould vara i formatet: `<your registry name>.azurecr.io`.

3. Skicka bilden till Docker-databasen. Använd den **visa | Kommandot paletten... | Edge: Push IoT kant modulen Docker bild** menyn kommando och ange avbildningens namn i textrutan popup visas överst i fönstret VS-kod. Använd samma namn för bilden som du använde i steg 1.c.

## <a name="add-registry-credentials-to-your-edge-device"></a>Lägg till registret autentiseringsuppgifter i Edge-enhet
Lägg till autentiseringsuppgifterna för registret Edge körningsmiljön på datorn där du kör Edge-enhet. Detta ger runtime-åtkomst till pull-behållaren. 

- För Windows, kör du följande kommando:
    
    ```cmd/sh
    iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

- Kör följande kommando för Linux:
    
    ```cmd/sh
    sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

## <a name="run-the-solution"></a>Köra lösningen

1. I den **Azure-portalen**, navigera till din IoT-hubb.
2. Gå till **IoT kant (förhandsgranskning)** och välj IoT-Edge-enhet.
1. Välj **ange moduler**. 
2. Lägg till den **tempSensor** modul. Detta krävs om du inte har använt den **tempSensor** modul till din IoT-Edge-enhet.
    1. Välj **lägga till IoT kant modul**.
    2. I den **namn** anger `tempSensor`.
    3. I den **avbildningen URI** anger `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Lämna de andra inställningarna oförändrad och klicka på **spara**.
1. Lägg till den **filterfunction** modul.
    1. Välj **Lägg till IoT kant modul** igen.
    2. I den **namn** anger `filterfunction`.
    3. I den **bild** , ange din adress bild, till exempel `<docker registry address>/filterfunction:latest`.
    74. Klicka på **Spara**.
2. Klicka på **Nästa**.
3. I den **ange vägar** steg, kopiera JSON nedan i textrutan. Moduler publicera alla meddelanden i Edge-körningsmiljön. Deklarativ regler i körningsmiljön definiera där dessa meddelanden. I den här kursen behöver du två vägar. Första vägen transporter meddelanden från temperatursensorn till filtermodul via slutpunkten ”input1”, vilket är den slutpunkt du konfigurerade med den **FilterMessages** hanterare. Andra vägen transporter meddelanden från modulen filter till IoT-hubb. I den här vägen `upstream` är särskilda mål som talar om kant-hubb för att skicka meddelanden till IoT-hubb. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Klicka på **Nästa**.
5. I den **granska mallen** , klicka på **skicka**. 
6. Gå tillbaka till sidan IoT kant enheten information och klickar på **uppdatera**. Du bör se den nya **filterfunction** modulen körs tillsammans med den **tempSensor** modulen och **IoT kant runtime**. 

## <a name="view-generated-data"></a>Visa genererade data

Så här övervakar enhet till moln-meddelanden som skickas från IoT-Edge-enhet till din IoT-hubb:
1. Konfigurera Azure IoT Toolkit-tillägget med anslutningssträngen för din IoT-hubb: 
    1. Använd den **visa | Explorer** menyn för att öppna Utforskaren VS-kod. 
    3. I Utforskaren klickar du på **IOT HUB-enheter** och klicka sedan på **...** . Klicka på **ange anslutningssträngen för IoT-hubb** och ange anslutningssträngen för IoT-hubb som din IoT insticksenhet ansluter till i popup-fönstret. 

        För att hitta anslutningssträngen panelen för din IoT-hubb i Azure-portalen och klicka sedan på **principer för delad åtkomst**. I **principer för delad åtkomst**, klicka på den **iothubowner** princip och kopiera IoT-hubb anslutningen sträng i den **iothubowner** fönster.   

1. Du övervakar data som inkommer till IoT-hubben i **visa | Kommandot paletten... | IoT: Börja övervaka D2C meddelandet** kommando. 
2. Om du vill stoppa övervakningen av data, Använd den **visa | Kommandot paletten... | IoT: Stoppa övervakningen D2C meddelandet** kommando. 

## <a name="next-steps"></a>Nästa steg

I kursen får skapat du en Azure-funktion som innehåller koden för att filtrera rådata som genereras av enheten IoT kant. Om du vill behålla utforska Azure IoT kant, lär du dig hur du använder en IoT-enhet som en gateway. 

> [!div class="nextstepaction"]
> [Skapa en IoT-Edge-gateway](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
