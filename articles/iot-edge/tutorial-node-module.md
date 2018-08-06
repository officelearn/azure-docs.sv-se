---
title: Självstudie för Azure IoT Edge Node.js | Microsoft Docs
description: Den här självstudien visar hur du skapar en IoT Edge-modul med Node.js-kod och distribuerar den till en gränsenhet
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 9ab441bdd30e7598dacfec8dd74702aef0299e1b
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413498"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-to-your-simulated-device"></a>Självstudie: Utveckla och distribuera en Node.js IoT Edge-modul till din simulerade enhet

Du kan använda IoT Edge-moduler till att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. Du kommer att använda den simulerade IoT Edge-enheten som du skapade i självstudien Distribuera Azure IoT Edge på en simulerad enhet i [Windows][lnk-tutorial1-win] eller [Linux][lnk-tutorial1-lin]. I den här guiden får du lära dig att:    

> [!div class="checklist"]
> * Använda Visual Studio Code för att skapa en IoT Edge Node.js-modul
> * Använda Visual Studio Code och Docker för att skapa en Docker-avbildning och publicera den till ditt register 
> * Distribuera modulen till din IoT Edge-enhet
> * Visa genererade data


IoT Edge-modulen som du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys är användbar för att minska mängden data som kommuniceras till och lagras i molnet. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

En Azure IoT Edge-enhet:

* Du kan använda utvecklingsdatorn eller en virtuell dator som en gränsenhet genom att följa stegen i snabbstarten för [Linux-](quickstart-linux.md) eller [Windows-enheter](quickstart.md).
* Azure Machine Learning-modulen stöder inte ARM-processorer.

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på standardnivå i Azure. 

Utvecklingsresurser:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge-tillägg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) för Visual Studio Code. 
* [Docker CE](https://docs.docker.com/engine/installation/). 
* [Node.js och npm](https://nodejs.org). npm-paketet distribueras med Node.js, vilket innebär att npm installeras automatiskt på din dator när du laddar ned Node.js.

## <a name="create-a-container-registry"></a>Skapa ett containerregister
I den här självstudien använder du Azure IoT Edge-tillägget för VS Code för att skapa en modul och skapa en **containeravbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.  

Du kan använda valfritt Docker-kompatibelt register för den här självstudien. Två populära Docker-registertjänster som finns tillgängliga i molnet är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). I den här kursen använder vi Azure Container Registry. 

1. I [Azure Portal](https://portal.azure.com) väljer du **Skapa en resurs** > **Behållare** > **Azure Container Registry**.
2. Namnge ditt register, välj en prenumeration, välj en resursgrupp och ange SKU:n till **Basic**. 
3. Välj **Skapa**.
4. När du har skapat ditt containerregister, navigerar du till det och väljer **Åtkomstnycklar**. 
5. Växla till **Administratörsanvändare** för att **Aktivera**.
6. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du kommer att använda de här värdena senare i självstudien. 

## <a name="create-an-iot-edge-module-project"></a>Skapa ett projekt för IoT Edge-modulen
Följande steg visar hur du skapar en IoT Edge Node.js-modul med Visual Studio Code och Azure IoT Edge-tillägget.

### <a name="create-a-new-solution"></a>Skapa en ny lösning

Använd **npm** för att skapa en Node.js-lösningsmall som du kan utgå ifrån. 

1. I Visual Studio Code väljer du **Visa** > **Integrerad terminal** för att öppna den integrerade Visual Studio Code-terminalen.

2. Installera **yeoman** och generatorn för Node.js Azure IoT Edge-modulen genom att köra följande kommando i den integrerade terminalen: 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Öppna kommandopaletten i VS Code genom att välja **Visa** > **Kommandopalett**. 

3. Ange och kör kommandot **Azure: Logga in** i kommandopaletten och följ anvisningarna för att logga in med ditt Azure-konto. Om du redan har loggat in kan du hoppa över det här steget.

4. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) i kommandopaletten. Ange följande information i kommandopaletten för att skapa din lösning: 

   1. Välj den mapp där du vill skapa lösningen. 
   2. Ange ett namn för din lösning eller välj standardnamnet **EdgeSolution**.
   3. Välj **Node.js-modulen** som mall för modulen. 
   4. Ge modulen namnet **NodeModule**. 
   5. Ange det Azure Container Registry som du skapade i föregående avsnitt som avbildningslagringsplatsen för din första modul. Ersätt **localhost:5000** med serverinloggningsvärdet som du kopierade. Den slutliga strängen ser ut så här: **\<registernamn\>.azurecr.io/nodemodule**.
 
VS Code läser in arbetsytan för IoT Edge-lösningen. Det finns en **.vscode**-mapp, en **modules**-mapp, en **.env**-fil och en mallfil för distributionsmanifestet

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerlagringsplatsen och delar dem med IoT Edge-körningsmiljön. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten. 

1. Öppna **.env**-filen i VS Code-utforskaren. 
2. Uppdatera fälten med det **användarnamn** och **lösenord** som du kopierade från Azure Container-registret. 
3. Spara filen. 

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

Exempelkod ingår i alla mallar. Koden simulerar sensordata från **tempSensor**-modulen och skickar dem till IoT Hub. I det här avsnittet lägger du till kod för att göra så att NodeModule analyserar meddelandena innan de skickas. 

1. Öppna **modules** > **NodeModule** > **app.js** i VS Code-utforskaren.

5. Lägg till en tröskelvariabel för temperatur under nodmoduler. Tröskelvärdet för temperatur är det värde som den uppmätta temperaturen måste överstiga för att data ska skickas till IoT Hub.

    ```javascript
    var temperatureThreshold = 30;
    ```

6. Ersätt hela funktionen `PipeMessage` med funktionen `FilterMessage`.
    
    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

7. Ersätt funktionsnamnet `pipeMessage` med `filterMessage` i funktionen `client.on()`.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

8. Kopiera följande kodfragment i motringningen för funktionen `client.open()` efter `client.on()` i instruktionen `else`. Funktionen anropas när önskade egenskaper uppdateras.

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

9. Spara filen.

## <a name="build-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod till NodeModule som filtrerar ut meddelanden om att temperaturen för den rapporterade datorn ligger under det godkända tröskelvärdet. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister. 

1. Logga in på Docker genom att ange följande kommando i den integrerade terminalen för Visual Studio Code så att du kan push-överföra modulavbildningen till ACR: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Använd användarnamnet, lösenordet och inloggningsservern som du kopierade från Azure Container Registry i det första avsnittet. Eller hämta dem igen från avsnittet **Åtkomstnycklar** i ditt register i Azure Portal.

2. I VS Code-utforskaren öppnar du filen **deployment.template.json** i arbetsytan för IoT Edge-lösningen. 

   Den här filen instruerar `$edgeAgent` om att distribuera två moduler: **tempSensor** som simulerar enhetsdata och **NodeModule**. `NodeModule.image`-värdet är inställt på en Linux amd64-version av avbildningen. Läs mer om distributionsmanifest i avsnittet om att [förstå hur IoT Edge-moduler kan användas, konfigureras och återanvändas](module-composition.md).

   Den här filen innehåller även autentiseringsuppgifter för registret. Ditt användarnamn och lösenord är ifyllda med platshållare i mallfilen. När du skapar distributionsmanifestet uppdateras fälten med de värden du lade till i **.env**. 

4. Lägg till NodeModule-modultvillingen till distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet `moduleContent` efter `$edgeHub`-modultvillingen: 
    ```json
        "NodeModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```
5. Spara filen.
6. Högerklicka på filen **deployment.template.json** och välj **Skapa IoT Edge-lösning** i VS Code-utforskaren. 

När du ger Visual Studio Code kommando att skapa din lösning hämtar den först information i distributionsmallen och genererar en `deployment.json`-fil i en ny **config**-mapp. Sedan körs två kommandon i en integrerad terminal: `docker build` och `docker push`. Dessa två kommandon skapar koden, lägger din Node.js-kod i behållare och push-överför till det containerregister som du angav när du initierade lösningen. 

Den fullständiga adressen med tagg för containeravbildningen i `docker build`-kommandot som körs i den integrerade VS Code-terminalen. Avbildningsadressen skapas utifrån information i filen `module.json` med formatet **\<lagringsplats\>:\<version\>-\<plattform\>**. För den här självstudien ska den se ut så här: **registryname.azurecr.io/nodemodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Distribuera och kör lösningen

Du kan använda Azure Portal för att distribuera din Node.ms-modul till en IoT Edge-enhet, som du gjorde i snabbstarten, men du kan också distribuera och övervaka moduler från Visual Studio Code. I följande avsnitt använder du Azure IoT Edge-filnamnstillägget för VS Code som listades under förutsättningarna. Installera det nu om du inte redan har gjort det. 

1. Öppna kommandopaletten för VS Code genom att välja **Visa** > **Kommandopalett**.

2. Sök efter och kör kommandot **Azure: Logga in**. Följ anvisningarna för att logga in på ditt Azure-konto. 

3. Leta upp kommandot **Azure IoT Hub: Välj IoT** i kommandopaletten och kör det. 

4. Välj den prenumeration som innehåller din IoT Hub och välj sedan den IoT Hub du vill komma åt.

5. I VS Code-utforskaren expanderar du avsnittet **Azure IoT Hub-enheter**. 

6. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Skapa distribution för IoT Edge-enhet**. 

7. Navigera till lösningsmappen som innehåller NodeModule. Öppna mappen **config** och välj filen **deployment.json**. Klicka på **Välj distributionsmanifest för Edge**.

8. Uppdatera avsnittet **Azure IoT Hub-enheter**. Du bör se den nya **NodeModule** köras tillsammans med **TempSensor**-modulen och **$edgeAgent** och **$edgeHub**. 


## <a name="view-generated-data"></a>Visa genererade data

1. Om du vill övervaka data som inkommer till IoT Hub klickar du på **...** och väljer **Starta övervakning av D2C-meddelanden**.
2. Om du vill övervaka D2C-meddelandet för en specifik enhet högerklickar du på enheten i listan och väljer **Starta övervakning av D2C-meddelanden**.
3. Om du vill stoppa dataövervakningen kör du kommandot **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: Sluta övervaka D2C-meddelande) i kommandopaletten. 
4. Om du vill visa eller uppdatera modultvillingen högerklickar du på modulen i listan och väljer **Redigera modultvilling**. Om du vill uppdatera modultvillingen sparar du tvilling-JSON-filen och högerklickar på redigeringsområdet och väljer **Uppdatera modultvilling**.
5. Om du vill visa Docker-loggar kan du installera [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) för VS Code och se moduler som körs lokalt i Docker-utforskaren. I kontextmenyn klickar du på **Visa loggar** för att visa i den integrerade terminalen. 

## <a name="clean-up-resources"></a>Rensa resurser 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Om du fortsätter till nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du redan har skapat och återanvända dem.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som skapats i den här artikeln om du vill undvika kostnader. 

> [!IMPORTANT]
> Det går inte att ångra borttagning av Azure-resurser och resursgrupper. När resursgruppen och alla resurser som ingår i den tas bort är de borta permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat IoT Hub:en inuti en befintlig resursgrupp som innehåller resurser som du vill behålla, ta bara bort själva IoT Hub-resursen i stället för att ta bort resursgruppen.
>

Om du endast vill ta bort körningen av IoT Hub kan du använda följande kommando med namnet på hubben och resursgruppens namn:

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


Ta bort hela resursgruppen med namnet:

1. Logga in på [Azure Portal](https://portal.azure.com) och klicka på **Resursgrupper**.

2. I textrutan **Filtrera efter namn ...** , skriver du namnet på resursgruppen som innehåller din IoT Hub. 

3. Till höger av din resursgrupp i resultatlistan klickar du på **...** och därefter **Ta bort resursgrupp**.

4. Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på din resursgrupp igen för att bekräfta och klicka sedan på **Ta bort**. Efter en liten stund tas resursgruppen och resurser som finns i den bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet. Du kan fortsätta med någon av följande självstudier om du vill lära dig mer om hur Azure IoT Edge kan förvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Distribuera Azure-funktionen som en modul](tutorial-deploy-function.md)
> [Distribuera Azure Stream Analytics som en modul](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
