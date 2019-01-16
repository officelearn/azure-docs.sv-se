---
title: Självstudie om att skapa anpassad Node.js-modul – Azure IoT Edge | Microsoft Docs
description: Den här självstudien visar hur du skapar en IoT Edge-modul med Node.js-kod och distribuerar den till en gränsenhet
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 62ea3e3ee13ee52462e1c93ac34e98ae179d251c
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54053933"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-to-your-simulated-device"></a>Självstudie: Utveckla och distribuera en Node.js IoT Edge-modul till din simulerade enhet

Du kan använda IoT Edge-moduler till att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. Du använder den simulerade IoT Edge-enhet som du skapade i snabbstarterna. I den här guiden får du lära dig att:    

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
* Om du kör IoT Edge på Windows har IoT Edge version 1.0.5 inte stöd för Node.js-moduler. Mer information finns i [viktig information om 1.0.5](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5). Stegvisa instruktioner för hur du installerar en specifik version finns i avsnittet om att [uppdatera IoT Edge-säkerhetsdaemon och -körning](how-to-update-iot-edge.md).

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure. 

Utvecklingsresurser:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) för Visual Studio Code. 
* [Docker CE](https://docs.docker.com/engine/installation/). 
* [Node.js och npm](https://nodejs.org). npm-paketet distribueras med Node.js, vilket innebär att npm installeras automatiskt på din dator när du laddar ned Node.js.

## <a name="create-a-container-registry"></a>Skapa ett containerregister

I den här självstudien använder du Azure IoT-verktyg för Visual Studio Code för att skapa en modul och skapa en **containeravbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.  

Du kan använda valfritt Docker-kompatibelt register för att lagra dina containeravbildningar. Två populära Docker-registertjänster är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). I den här kursen använder vi Azure Container Registry. 

Om du inte redan har ett containerregister följer du dessa steg för att skapa ett nytt i Azure:

1. I [Azure Portal](https://portal.azure.com) väljer du **Skapa en resurs** > **Container** > **Containerregister**.

2. Skapa containerregistret genom att ange följande värden:

   | Fält | Värde | 
   | ----- | ----- |
   | Registernamn | Ange ett unikt namn. |
   | Prenumeration | Välj en prenumeration i listrutan. |
   | Resursgrupp | Vi rekommenderar att du använder samma resursgrupp för alla testresurser som du skapar i snabbstarterna och självstudierna om IoT Edge. Till exempel **IoTEdgeResources**. |
   | Plats | Välj en plats i närheten av dig. |
   | Administratörsanvändare | Ändra värdet till **Aktivera**. |
   | SKU | Välj **Grundläggande**. | **Terminal**

5. Välj **Skapa**.

6. När du har skapat containerregistret går du till det och väljer **Åtkomstnycklar**. 

7. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du kan använda dessa värden senare i självstudien för att ge åtkomst till containerregistret. 

## <a name="create-an-iot-edge-module-project"></a>Skapa ett projekt för IoT Edge-modulen
Följande steg visar hur du skapar en IoT Edge Node.js-modul med Visual Studio Code och Azure IoT-verktyg.

### <a name="create-a-new-solution"></a>Skapa en ny lösning

Använd **npm** för att skapa en Node.js-lösningsmall som du kan utgå ifrån. 

1. I Visual Studio Code väljer du **Visa** > **Integrerad terminal** för att öppna den integrerade Visual Studio Code-terminalen.

2. Installera **yeoman** och generatorn för Node.js Azure IoT Edge-modulen genom att köra följande kommando i den integrerade terminalen: 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Välj **Visa** > **Kommandopalett** för att öppna kommandopaletten i VS Code. 

3. I kommandopaletten skriver du och kör kommandot **Azure: Logga in** och följer anvisningarna för att logga in på ditt Azure-konto. Om du redan har loggat in kan du hoppa över det här steget.

4. I kommandopaletten skriver och kör du kommandot **Azure IoT Edge: Ny IoT Edge-lösning**. Skapa lösningen genom att följ anvisningarna på kommandopaletten.

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **Node.js-modulen**. |
   | Ange ett modulnamn | Ge modulen namnet **NodeModule**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen har fyllts i från föregående steg. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. Den slutliga strängen ser ut så här: \<registernamn\>.azurecr.io/nodemodule. |
 
   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-node-module/repository.png)

VS Code läser in arbetsytan för IoT Edge-lösningen. Lösningens arbetsyta innehåller fem komponenter på den högsta nivån. Mappen **modules** innehåller Node.js-koden för din modul samt Dockerfiles som används för att skapa modulen som en containeravbildning. Filen **\.env** lagrar dina autentiseringsuppgifter för containerregistret. Filen **deployment.template.json** innehåller informationen som IoT Edge-körningen använder för att distribuera moduler på en enhet. Och filen **deployment.debug.template.json** innehåller felsökningsversionen av modulerna. Du kommer inte att redigera mappen **\.vscode** eller filen **\.gitignore** i den här självstudiekursen. 

Om du inte angav ett containerregister när du skapade lösningen, men accepterade standardvärdet localhost:5000, har du ingen \.env-fil. 

<!--
   ![Node.js solution workspace](./media/tutorial-node-module/workspace.png)
-->

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
    var temperatureThreshold = 25;
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

9. Spara filen app.js.

10. I VS Code-utforskaren öppnar du filen **deployment.template.json** i arbetsytan för IoT Edge-lösningen. Den här filen talar om för IoT Edge-agenten vilka moduler som ska distribueras, i detta fall **tempSensor** och **NodeModule**, och talar om för IoT Edge-hubben hur meddelanden ska dirigeras mellan dem. Visual Studio Code-tillägget fyller automatiskt i merparten av den information som du behöver i distributionsmallen, men kontrollerar att allt är korrekt för din lösning: 

   1. Standardplattformen för din IoT Edge är inställd på **amd64** i VS Code-statusfältet, vilket innebär att **NodeModule** är inställd på Linux amd64-versionen för avbildningen. Ändra standardplattformen i statusfältet från **amd64** till **arm32v7** eller **windows-amd64** om det är arkitekturen för din IoT Edge-enhet. 

      ![Uppdatera modulavbildningsplattformen](./media/tutorial-node-module/image-platform.png)

   2. Kontrollera att mallen har rätt modulnamn, inte standardnamnet **SampleModule** som du ändrade när du skapade IoT Edge-lösningen.

   3. Avsnittet **registryCredentials** lagrar dina autentiseringsuppgifter för Docker-registret så att IoT Edge-agenten kan hämta modulavbildningen. Själva användarnamns- och lösenordsparen lagras i .env-filen, som ignoreras av Git. Lägg till dina autentiseringsuppgifter i .env-filen om du inte redan har gjort det.  

   4. Mer information om distributionsmanifest finns i [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md) (Lär dig hur du distribuerar moduler och upprättar vägar i IoT Edge).

11. Lägg till NodeModule-modultvillingen till distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet `moduleContent` efter `$edgeHub`-modultvillingen: 

   ```json
       "NodeModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Lägga till modultvilling till distributionsmall](./media/tutorial-node-module/module-twin.png)

12. Spara filen deployment.template.json.


## <a name="build-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod till NodeModule som filtrerar ut meddelanden om att temperaturen för den rapporterade datorn ligger under det godkända tröskelvärdet. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister. 

1. Logga in på Docker genom att ange följande kommando i den integrerade terminalen för Visual Studio Code så att du kan push-överföra modulavbildningen till ACR: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Använd användarnamnet, lösenordet och inloggningsservern som du kopierade från Azure Container Registry i det första avsnittet. Eller hämta dem igen från avsnittet **Åtkomstnycklar** i ditt register i Azure Portal.

2. I VS Code-utforskaren högerklickar du på filen **deployment.template.json** och väljer **Build and Push IoT Edge solution** (Skapa och skicka IoT Edge-lösning). 

När du ger Visual Studio Code kommando att skapa din lösning hämtar den först information i distributionsmallen och genererar en `deployment.json`-fil i en ny **config**-mapp. Sedan körs två kommandon i en integrerad terminal: `docker build` och `docker push`. Dessa två kommandon skapar koden, lägger din Node.js-kod i behållare och push-överför till det containerregister som du angav när du initierade lösningen. 

Den fullständiga adressen med tagg för containeravbildningen i `docker build`-kommandot som körs i den integrerade VS Code-terminalen. Avbildningsadressen skapas utifrån information i filen `module.json` med formatet **\<lagringsplats\>:\<version\>-\<plattform\>**. För den här självstudien ska den se ut så här: **registryname.azurecr.io/nodemodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Distribuera och kör lösningen

I stegen i snabbstartsartikeln som du följde för att konfigurera IoT Edge-enheten distribuerade du en modul med hjälp av Azure Portal. Du kan även distribuera moduler med hjälp av Azure IoT Hub Toolkit-tillägget (tidigare Azure IoT Toolkit-tillägget) för Visual Studio Code. Du har redan ett distributionsmanifest som är förberett för ditt scenario, filen **deployment.json**. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

1. I kommandopaletten i VS Code kör du **Azure IoT Hub: Välj IoT-hubb**. 

2. Välj den prenumeration och IoT-hubb som innehåller den IoT Edge-enhet som du vill konfigurera. 

3. I VS Code-utforskaren expanderar du avsnittet **Azure IoT Hub-enheter**. 

4. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Create Deployment for Single Device** (Skapa distribution för en enskild enhet). 

   ![Skapa distribution för en enskild enhet](./media/tutorial-node-module/create-deployment.png)

5. Välj filen **deployment.json** i mappen **config** och klicka sedan på **Select Edge Deployment Manifest** (Välj distributionsmanifest för Edge). Använd inte filen deployment.template.json. 

6. Klicka på uppdateringsknappen. Du bör se den nya **NodeModule** köras tillsammans med **TempSensor**-modulen och **$edgeAgent** och **$edgeHub**. 


## <a name="view-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas. 

Du kan visa statusen för din IoT Edge-enhet i avsnittet om **Azure IoT Hub-enheter** i Visual Studio Code-utforskaren. Expandera enhetsinformationen så ser du en lista med moduler som distribueras och körs. 

Med hjälp av kommandot `iotedge list` på själva IoT Edge-enheten kan du se statusen för dina distributionsmoduler. Du bör se fyra moduler: de modulerna för IoT Edge-körning, tempSensor och den anpassade modul du skapade i den här självstudien. Det kan ta några minuter för alla moduler att starta, så kör kommandot igen om du till en början inte ser alla. 

Du kan visa de meddelanden som genereras av alla moduler med hjälp av kommandot `iotedge logs <module name>`. 

Du kan visa meddelanden när de anländer till IoT-hubben med hjälp av Visual Studio Code. 

1. Om du vill övervaka data som inkommer till IoT Hub klickar du på **...** och väljer **Starta övervakning av D2C-meddelanden**.
2. Om du vill övervaka D2C-meddelandet för en specifik enhet högerklickar du på enheten i listan och väljer **Starta övervakning av D2C-meddelanden**.
3. Om du vill stoppa övervakningen av data kör du kommandot **Azure IoT Hub: Sluta övervaka D2C-meddelande** i kommandopaletten. 
4. Om du vill visa eller uppdatera modultvillingen högerklickar du på modulen i listan och väljer **Redigera modultvilling**. Om du vill uppdatera modultvillingen sparar du tvilling-JSON-filen och högerklickar på redigeringsområdet och väljer **Uppdatera modultvilling**.
5. Om du vill visa Docker-loggar kan du installera [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) för VS Code och se moduler som körs lokalt i Docker-utforskaren. I kontextmenyn klickar du på **Visa loggar** för att visa i den integrerade terminalen. 

## <a name="clean-up-resources"></a>Rensa resurser 

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet. 

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet. Du kan fortsätta med någon av följande självstudier om du vill lära dig mer om hur Azure IoT Edge kan förvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Distribuera Azure-funktionen som en modul](tutorial-deploy-function.md)
> [Distribuera Azure Stream Analytics som en modul](tutorial-deploy-stream-analytics.md)

