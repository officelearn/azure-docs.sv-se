---
title: Skapa och distribuera anpassade moduler - Machine Learning på Azure IoT Edge | Microsoft Docs
description: Skapa och distribuera IoT Edge-moduler som bearbetar data från lövenheter via en maskininlärningsmodell och skicka sedan insikterna till IoT Hub.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 571a72d396c003bab363682559464715b180458a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081394"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Självstudier: Skapa och distribuera anpassade IoT Edge-moduler

> [!NOTE]
> Den här artikeln ingår i en serie med en självstudiekurs om hur du använder Azure Machine Learning på IoT Edge. Om du har hamnat på den här artikeln direkt, rekommenderar vi att du börja med den [först artikel](tutorial-machine-learning-edge-01-intro.md) i serien för bästa resultat.

I den här artikeln ska skapa vi tre IoT Edge-moduler som tar emot meddelanden från lövenheter, köra data via din machine learning-modell och sedan vidarebefordra insikter till IoT Hub.

IoT Edge hub underlättar modulen till modulen kommunikationen. Med hjälp av IoT Edge behåller hub som en asynkron meddelandekö moduler oberoende av varandra. Moduler behöver bara ange indata som de godkänner meddelanden och utdata som de skriva meddelanden.

Vi vill IoT Edge-enhet för att utföra fyra saker vi:

* Ta emot data från lövenheter
* Förutsäga RUL för den enhet som skickade data
* Skicka ett meddelande med endast motors återstående Livslängd för enheten till IoT Hub (den här funktionen kan ändras för att skicka data endast om RUL sjunker under vissa nivå)
* Spara löv enhetens data till en lokal fil på IoT Edge-enhet. Den här datafilen regelbundet laddas upp till IoT Hub via filöverföringen att förfina utbildning av machine learning-modell. Om du använder filuppladdningen i stället för konstant meddelande direktuppspelning är mer kostnadseffektivt.

Vi använder tre anpassade moduler för att utföra dessa uppgifter:

* **RUL-klassificeraren:** Modulen turboFanRulClassifier som vi skapade i [träna och distribuera en Azure Machine Learning-modell](tutorial-machine-learning-edge-04-train-model.md) är en standard machine learning-modul, som visar indata som heter ”amlInput” och utdata som kallas ”amlOutput”. ”AmlInput” förväntar att dess indata ska se ut exakt som de indata som vi har skickat till ACI-baserad webbtjänst. På samma sätt returnerar ”amlOutput” samma data som webbtjänsten.

* **Avro-skrivaren:** Den här modulen tar emot meddelanden på ”avroModuleInput”-indata och kvarstår meddelandet i Avro-format till disk för senare överföring till IoT Hub.

* **Router-modul:** Modulen router tar emot meddelanden från nedströms lövenheter och sedan formaterar och skickar meddelanden till klassificeraren. Modulen tar emot meddelanden från klassificeraren sedan och vidarebefordrar meddelandet till Avro-skrivarmodul. Slutligen skickar modulen bara RUL-förutsägelse till IoT Hub.

  * Indata:
    * **deviceInput**: tar emot meddelanden från lövenheter
    * **rulInput:** tar emot meddelanden från ”amlOutput”

  * Utdata:
    * **klassificera:** skickar meddelanden till ”amlInput”
    * **writeAvro:** skickar meddelanden ”avroModuleInput”
    * **toIotHub:** skickar meddelanden till $uppströms, som skickar meddelanden till anslutna IoT-hubben

Diagrammet nedan visar moduler, indata, utdata och IoT Edge Hub-vägarna för hela lösningen:

![Arkitekturdiagram för IoT Edge tre moduler](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

Stegen i den här artikeln är vanligtvis utförs av en utvecklare i molnet.

## <a name="create-a-new-iot-edge-solution"></a>Skapa en ny IoT Edge-lösning

Vid körningen av andra av våra två anteckningsböcker i Azure, vi skapat och publicerat en behållaravbildning som innehåller vårt RUL-modellen. Azure Machine Learning, som en del av skapa avbildningen, inbyggda delar att skapa avbildningen distribueras som en Azure IoT Edge-modul. I det här steget ska vi ska skapa en Azure IoT Edge-lösning med hjälp av modulen ”Azure Machine Learning” och peka modulen på den bild som vi publicerade med hjälp av Azure-anteckningsböcker.

1. Öppna en fjärrskrivbordssession till din utvecklingsdator.

2. Öppna mapp **C:\\källa\\IoTEdgeAndMlSample** i Visual Studio Code.

3. Högerklicka på panelen explorer (i det tomma utrymmet) och välj **nya IoT Edge-lösning**.

    ![Skapa ny lösning för IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. Godkänn standardnamnet för lösningen **EdgeSolution**.

5. Välj **Azure Machine Learning** som mall för modulen.

6. Namnge modulen **turbofanRulClassifier**.

7. Välj din machine learning-arbetsyta.

8. Välj den avbildning som du skapade när du kör Azure-anteckningsboken.

9. Titta på lösningen och Lägg märke till de filer som har skapats:

   * **deployment.template.json:** Den här filen innehåller definitionen av varje modul i lösningen. Det finns tre områden för att ta hänsyn till i den här filen:

     * **Autentiseringsuppgifter för registret:** Definierar uppsättningen med anpassad behållarregister som du använder i din lösning. Höger nu den bör innehålla registret från machine learning-arbetsyta som är där din Azure Machine Learning avbildning lagrades. Du kan ha valfritt antal behållarregister, men för enkelhetens skull vi använder en registrets för alla moduler

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io”
         }
       }
       ```

     * **Moduler:** Det här avsnittet innehåller en uppsättning användardefinierade moduler som ingår i den här lösningen. Du kommer att märka att det här avsnittet innehåller två moduler: tempSensor och turbofanRulClassifier. TempSensor har installerats av Visual Studio Code-mallen, men behövs inte för den här lösningen. Du kan ta bort definitionen för tempSensor modulen från modulavsnittet. Observera att turbofanRulClassifier modulen definitionen pekar på avbildningen i ditt behållarregister. När vi lägger till flera moduler i lösningen, visas de i det här avsnittet.

       ```json
       "modules": {
         "tempSensor": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
             "createOptions": {}
           }
         },
         "turbofanRulClassifier": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "<your registry>.azurecr.io/edgemlsample:1",
             "createOptions": {}
           }
         }
       }
       ```

     * **Vägar:** kommer vi att arbeta med vägar ökar en hel del i den här självstudien. Vägar definierar hur moduler kommunicerar med varandra. De två vägarna som definieras i mallen stämmer inte överens med Routning som vi behöver. Den första vägen skickar alla data från alla utdata för klassificerarna till IoT Hub (uppströms$). Den andra vägen avser tempSensor som vi precis har tagits bort. Ta bort de två standardvägar.

       ```json
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
             "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/\* INTO $upstream",
             "sensorToturbofanRulClassifier": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\\"/modules/turbofanRulClassifier/inputs/input1\\")"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       }
       ```

   * **Deployment.Debug.template.JSON:** den här filen är felsökningsversionen av deployment.template.json. Vi ska spegla alla ändringar från deployment.template.json i den här filen.

   * **.ENV:** den här filen är där du ska ange användarnamn och lösenord för åtkomst till ditt register.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. Högerklicka på filen deployment.template.json i Visual Studio Code-Utforskaren och välj **skapa lösning för IoT Edge**.

11. Observera att det här kommandot skapar en config-mapp med en deployment.amd64.json-fil. Den här filen är konkreta Distributionsmall för lösningen.

## <a name="add-router-module"></a>Lägg till Router-modul

Nu ska vi lägga till Router-modulen till vår lösning. Router-modulen hanterar flera ansvar för vår lösning:

* **Ta emot meddelanden från lövenheter:** när meddelandet kommer till IoT Edge-enhet från underordnade enheter modulen Router tar emot meddelandet och börjar samordna routning av meddelandet.
* **Skicka meddelanden till modulen RUL-klassificeraren:** när ett nytt meddelande tas emot från en underordnad enhet, Router-modulen omvandlar meddelandet till det format som förväntar sig att RUL-klassificerare. Routern skickar meddelandet till RUL-klassificerare för en RUL-förutsägelse. När klassificeraren har gjort en förutsägelse, skickar meddelandet till Router-modulen.
* **Skicka RUL-meddelanden till IoT Hub:** när routern som tar emot meddelanden från klassificeraren, den omvandlar meddelandet som ska innehålla endast grundläggande information, enhets-ID och RUL, och skickar meddelandet förkortat till IoT hub. En ytterligare förfining, som vi inte har gjort här, skulle skicka meddelanden till IoT Hub endast när RUL-förutsägelse faller under ett tröskelvärde (till exempel när RUL är mindre än 100 cykler). Filtrering på så sätt skulle minska volym av meddelanden och minska kostnaderna för IoT hub.
* **Skicka meddelande till Avro-skrivarmodul:** för att bevara alla data som skickas av underordnade enheten, Router-modulen skickar hela meddelandet togs emot från klassificeraren för Avro-skrivaren-modul som kvarstår och överföra data med hjälp av IoT Hub-fil ladda upp.

> [!NOTE]
> Beskrivning av modulen ansvarsområden kan göra bearbetningen verka sekventiella men flödet är meddelande/händelsebaserade. Det är därför vi behöver en orchestration-modul som våra Router-modulen.

### <a name="create-module-and-copy-files"></a>Skapa modulen och kopiera filer

1. Högerklicka på modulmappen i Visual Studio Code och välj **Lägg till IoT Edge-modul**.

2. Välj  **C# modulen**.

3. Namnge modulen **turbofanRouter**.

4. När du tillfrågas om din lagringsplats för Docker-avbildning, använder du registret från machine learning-arbetsytan (du hittar registret i noden registryCredentials din *deployment.template.json* fil). Det här värdet är den fullständiga adressen i registret som  **\<registret\>.azurecr.io/turbofanrouter**.

    > [!NOTE]
    > I den här artikeln använder vi Azure Container Registry som skapats av Azure Machine Learning-tjänsten arbetsyta som vi använde för att träna och distribuera vår klassificerare. Detta är enbart i informationssyfte. Vi kan ha skapat ett nytt behållarregister och publicerat vår moduler det.

5. Öppna ett nytt terminalfönster i Visual Studio Code (**visa** > **Terminal**) och kopiera filer från katalogen moduler.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. När du uppmanas att skriva över program.cs, trycker du på `y` och tryck sedan på `Enter`.

### <a name="build-router-module"></a>Skapa router-modul

1. I Visual Studio Code, Välj **Terminal** > **konfigurera standard skapa uppgift**.

2. Klicka på **skapa tasks.json fil från mall**.

3. Klicka på **.NET Core**.

4. Ersätt innehållet med när tasks.json öppnas:

    ```json
    {
      // See https://go.microsoft.com/fwlink/?LinkId=733558
      // for the documentation about the tasks.json format
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

5. Spara och Stäng tasks.json.

6. Köra build med `Ctrl + Shift + B` eller **Terminal** > **kör Skapa uppgift**.

### <a name="set-up-module-routes"></a>Konfigurera modulen vägar

Som nämnts ovan är IoT Edge-körningen använder vägar som konfigurerats i den *deployment.template.json* filen för att hantera kommunikationen mellan löst kopplade moduler. I det här avsnittet ska se vi hur du konfigurerar vägar i modulen turbofanRouter. Vi täcker inkommande vägar först och sedan flytta på utdata.

#### <a name="inputs"></a>Indata

1. I metoden Init() i Program.cs registrerar vi två återanrop för modulen:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. Det första återanropet lyssnar efter meddelanden som skickas till den **deviceInput** mottagare. Från diagrammet ovan ser vi att vi vill skicka meddelanden från valfri enhet för löv att denna indata. I den *deployment.template.json* Lägg till en väg som talar om för edge hub att dirigera alla meddelanden som tas emot av IoT Edge-enhet som inte har skickats av en IoT Edge-modul till indata som kallas ”deviceInput” i modulen turbofanRouter:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Därefter lägger till en väg för meddelanden från modulen rulClassifier till modulen turbofanRouter:

   ```json
   "classifierToRouter": "FROM /messages/modules/classifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Utdata

Lägg till fyra ytterligare vägar till parametern $edgeHub väg att hantera utdata från modulen Router.

1. Program.CS definierar metoden SendMessageToClassifier() som använder klient-modulen för att skicka ett meddelande till RUL-klassificerare med vägen:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/classifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub() använder klient-modulen för att skicka bara RUL-data för enheten till IoT Hub via vägen:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter() använder klient-modulen för att skicka meddelandet med RUL-data som lagts till avroFileWriter-modulen.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() skickar misslyckade meddelanden uppströms IoT Hub där de kan vidarebefordras till senare.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Med de vägar som tillsammans ”$edgeHub” nod bör se ut som följande JSON:

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

> [!NOTE]
> Att lägga till modulen turbofanRouter skapas följande ytterligare väg: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`. Ta bort den här vägen, lämnar bara vägar som visas i din deployment.template.json-fil.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Kopiera vägar till deployment.debug.template.json

Spegla de ändringar du gjort i deployment.template.json i deployment.debug.template.json om du vill synkronisera våra filer, som ett sista steg.

## <a name="add-avro-writer-module"></a>Lägg till Avro-skrivarmodul

Avro-skrivarmodul har två ansvarsområden i vår lösning för att lagra meddelanden och ladda upp filer.

* **Store meddelanden**: när Avro-skrivarmodul får ett meddelande visas den skriver meddelandet till det lokala filsystemet i Avro-format. Vi använder en bindning montering montera en katalog (i det här fallet /data/avrofiles) till en sökväg i modulens behållare. Den här montera gör att modulen att skriva till en lokal sökväg (/ avrofiles) och har de filerna som är tillgängliga direkt från IoT Edge-enhet.

* **Ladda upp filer**: Avro-skrivarmodul använder Azure IoT Hub filuppladdningsfunktionen ladda upp filer till ett Azure storage-konto. När en fil har laddats upp modulen tar bort filen från disken

### <a name="create-module-and-copy-files"></a>Skapa modulen och kopiera filer

1. I kommandopaletten, söker du efter och välj sedan **Python: Välj tolk**.

1. Välj vilken tolk som finns i C:\\Python37.

1. Öppna kommandopaletten igen och Sök efter och välj sedan **Terminal: Välj standardgränssnitt**.

1. Välj **kommandotolk**.

1. Öppna ett nytt terminal gränssnitt **Terminal** > **ny Terminal**.

1. Högerklicka på modulmappen i Visual Studio Code och välj **Lägg till IoT Edge-modul**.

1. Välj **Python-modul**.

1. Namnge modulen ”avroFileWriter”.

1. När du uppmanas din lagringsplats för Docker-avbildning, använder du samma registret som du använde när du lägger till Router-modulen.

1. Kopiera filer från exempelmodulen i lösningen.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Om du uppmanas att skriva över main.py, skriver `y` och tryck sedan på `Enter`.

1. Observera att filemanager.py och schema.py har lagts till lösningen och main.py har uppdaterats.

> [!NOTE]
> När du öppnar en Python-fil kan du uppmanas att installera pylint. Du behöver inte installera linter för att slutföra den här självstudien.

### <a name="bind-mount-for-data-files"></a>Binda montering för datafiler

Som vi nämnde i introduktionen, modulen skrivaren förlitar sig på förekomsten av bindning montera att skriva Avro-filernas till enhetens filsystem.

#### <a name="add-directory-to-device"></a>Lägg till katalog till enheten

1. Ansluta till din IoT Edge-enhet VM med hjälp av SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Skapa katalogen som ska innehålla de sparade löv meddelanden från enheten.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Uppdatera directory behörighet att ta bort skrivskyddet från behållaren.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. Verifiera katalogen har nu behörighet att skriva (w) för användare, grupp och ägare.

   ```bash
   ls -la /data
   ```

   ![Directory-behörigheter för avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Lägg till katalog i modulen

Om du vill lägga till katalogen modulens behållaren, ska vi ändra Dockerfiles som är associerade med modulen avroFileWriter. Det finns tre Dockerfiles som är associerade med modulen: Dockerfile.amd64 Dockerfile.amd64.debug och Dockerfile.arm32v7. Dessa filer bör hållas synkroniserade om vi vill felsöka eller distribuera till en arm32-enhet. Fokus ligger endast på Dockerfile.amd64 i den här artikeln.

1. På en utvecklingsdator måste du öppna den **Dockerfile.amd64** fil.

2. Ändra filen så att den ser ut som i följande exempel:

   ```dockerfile
   FROM ubuntu:xenial  

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   Den `mkdir` och `chown` kommandon instruerar Docker build processen att skapa en översta katalog med namnet /avrofiles i bild och för att göra moduleuser till ägare av den katalogen. Det är viktigt att dessa kommandon infogas när modulen användaren har lagts till avbildningen med den `useradd` kommandot och innan växlarna kontext till moduleuser (användaren moduleuser).

3. Ändra de motsvarande Dockerfile.amd64.debug och Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Uppdatera modulkonfigurationen av

Det sista steget för att skapa bindningen är att uppdatera deployment.template.json (och deployment.debug.template.json) filer med informationen som bindning.

1. Öppna deployment.template.json.

2. Ändra definitionen för avroFileWriter modulen genom att lägga till den `Binds` parameter som pekar behållare directory /avrofiles på den lokala katalogen på edge-enhet. Definition av modulen ska matcha det här exemplet:

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

3. Ändra de motsvarande deployment.debug.template.json.

### <a name="bind-mount-for-access-to-configyaml"></a>Binda montering för åtkomst till config.yaml

Vi måste lägga till en mer bindning för modulen skrivaren. Den här bind får modulen att läsa anslutningssträngen från filen /etc/iotedge/config.yaml på IoT Edge-enhet. Vi behöver anslutningssträngen för att skapa en IoTHubClient så att vi kan anropa överföringen\_blob\_async-metod för att ladda upp filer till IoT hub. Stegen för att lägga till den här bindning är liknande dem i föregående avsnitt.

#### <a name="update-directory-permission"></a>Uppdatera Katalogbehörighet

1. Anslut till din IoT Edge-enhet med hjälp av SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Lägg till läsbehörighet config.yaml-filen.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. Validera behörigheterna är korrekta.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Kontrollera att behörigheterna för config.yaml är **- r--r--r--** .

#### <a name="add-directory-to-module"></a>Lägg till katalog i modulen

1. På en utvecklingsdator måste du öppna den **Dockerfile.amd64** fil.

2. Lägg till ytterligare en uppsättning `mkdir` och `chown` kommandon till filen så som ser ut som:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

3. Ändra de motsvarande Dockerfile.amd64.debug och Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Uppdatera modulkonfigurationen av

1. Öppna den **deployment.template.json** fil.

2. Ändra definitionen för avroFileWriter modulen genom att lägga till en andra rad till den `Binds` parameter som pekar behållarkatalogen (/ app/iotconfig) på den lokala katalogen på enheten (/ etc/iotedge).

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

3. Ändra de motsvarande deployment.debug.template.json.

## <a name="install-dependencies"></a>Installera beroenden

Modulen skrivaren tar ett beroende på två Python-biblioteken, fastavro och PyYAML. Vi behöver installera beroenden på vår utvecklingsdator och instruera Docker skapandeprocessen att installera dem i våra moduls avbildning.

### <a name="pyyaml"></a>PyYAML

1. På en utvecklingsdator måste du öppna den **requirements.txt** filen och Lägg till pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Öppna den **Dockerfile.amd64** filen och Lägg till en `pip install` kommando för att uppgradera installationsverktyg.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

3. Ändra de motsvarande Dockerfile.amd64.debug. <!--may not be necessary. Add 'if needed'?-->

4. Installera pyyaml lokalt genom att öppna en terminal i Visual Studio Code och skriva

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. Lägg till fastavro efter pyyaml i requirements.txt.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. Installera fastavro till din utvecklingsdator med hjälp av Visual Studio Code terminal.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Reconfigure IoT Hub

Genom att introducera IoT Edge-enhet och moduler i systemet, har vi ändrat våra förväntningar om vilka data som skickas till hubben och i vilket syfte. Vi måste konfigurera om routning i hubben behöver bry dig om våra nya verkligheten.

> [!NOTE]
> Vi konfigurerar om hubben innan du distribuerar moduler eftersom några av de hub-inställningarna, särskilt filen överför måste vara korrekt konfigurerat avroFileWriter modulen ska kunna köras korrekt

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Ställ in flöde för RUL-meddelanden i IoT Hub

Med router och klassificerare på plats kan räknar vi med att ta emot vanliga meddelanden som innehåller endast enhets-ID och RUL-förutsägelser för enheten. Vi vill dirigera RUL-data till en egen lagringsplats där vi kan övervaka status för enheter, skapa rapporter och utlöses aviseringar efter behov. På samma gång vill vi att alla enhetsdata som fortfarande som skickas direkt via en löv-enhet som ännu inte har kopplats till vår IoT Edge-enhet för att fortsätta att dirigera till den aktuella lagringsplatsen.

#### <a name="create-a-rul-message-route"></a>Skapa en väg för RUL-meddelande

1. Gå till din IoT-hubb i Azure-portalen.

2. I det vänstra navigeringsfönstret väljer **meddelanderoutning**.

3. Välj **Lägg till**.

4. Namnge flödet **RulMessageRoute**.

5. Välj **Lägg till** bredvid den **Endpoint** väljare och välj **Blob-lagring**.

6. I den **lägga till en slutpunkt för lagring** formuläret, namnge slutpunkten **ruldata**.

7. Välj **Välj en behållare**.

8. Välj lagringskonto som används i den här självstudien, som heter som **iotedgeandml\<unikt suffix\>** .

9. Välj den **ruldata** behållare och klickar på **Välj**.

10. Klicka på **skapa** skapa storage-slutpunkt.

11. För den **routning fråga**, anger du följande fråga:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. Expandera den **Test** avsnittet och sedan den **meddelandetext** avsnittet. Ersätt meddelandet med det här exemplet på vår förväntade meddelanden:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. Välj **testa flödet**. Om testet lyckas ser du ”meddelandet matchar frågan”.

14. Klicka på **Spara**.

#### <a name="update-turbofandevicetostorage-route"></a>Uppdatera turbofanDeviceToStorage vägen

Vi vill inte skicka nya förutsägelsedata till vår gamla lagringsplatsen, så uppdatera vägen för att förhindra att den.

1. I IoT Hub **meddelanderoutning** väljer den **vägar** fliken.

2. Välj **turbofanDeviceDataToStorage**, eller det namn du gav till färdvägen starttillstånd data.

3. Uppdatera routning frågan till

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. Expandera den **Test** avsnittet och sedan den **meddelandetext** avsnittet. Ersätt meddelandet med det här exemplet på vår förväntade meddelanden:

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

5. Välj **testa flödet**. Om testet lyckas ser du ”meddelandet matchar frågan”.

6. Välj **Spara**.

### <a name="configure-file-upload"></a>Konfigurera filuppladdning

Konfigurera IoT Hub filuppladdningsfunktionen om du vill aktivera skrivarmodul fil att ladda upp filer till lagring.

1. Vänstra Navigatören i din IoT-hubb, Välj **filuppladdning**.

2. Välj **Azure-lagringsbehållare**.

3. Välj ditt lagringskonto i listan.

4. Välj den **uploadturbofanfiles** behållare och klickar på **Välj**.

5. Välj **Spara**. Portalen meddelar dig när filen är sparad.

> [!Note]
> Vi inte att aktivera uppladdning meddelande för den här självstudien, men se [ta emot ett filuppladdningsmeddelande](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) för information om hur du hanterar filen ladda upp meddelandet.

## <a name="build-publish-and-deploy-modules"></a>Skapa, publicera och distribuera moduler

Nu när vi har gjort ändringar i konfigurationen är vi redo att skapa avbildningar och publicera dem till vår Azure container registry. Skapandeprocessen använder deployment.template.json-filen för att avgöra vilka moduler som behöver skapas. Inställningarna för varje modul, inklusive version, finns i filen module.json i modulmappen. Skapandeprocessen körs för första gången en Docker-version på Dockerfiles matchar den aktuella konfigurationen i filen module.json för att skapa en avbildning. Sedan publicerar den avbildningen till registret från filen module.json med en tagg för version som matchar det i filen module.json. Slutligen genererar den ett manifest med configuration-specifika distribution (till exempel deployment.amd64.json), som vi ska distribuera till IoT Edge-enhet. IoT Edge-enheten läser information från distributionen manifest och baserat på instruktioner ska ladda ned moduler, konfigurera vägar och ange önskade egenskaper. Den här distributionsmetoden har två sidoeffekter som du bör känna till:

* **Distribution fördröjning:** eftersom IoT Edge-körningen måste identifiera ändringen till dess önskade egenskaper innan den börjar konfigurera om, det kan ta några lång tid när du har distribuerat dina moduler tills körningen hämtas de och börjar uppdatera IoT Edge enheten.

* **Modulen versioner bara:** om du publicerar en ny version av en modul behållare på ditt behållarregister med hjälp av samma version-taggar som den föregående modulen körningen inte ladda ned den nya versionen av modulen. Den gör en jämförelse av version taggen för den lokala avbildningen och önskad bild från distribution manifestet. Om dessa versioner matchar tar körningen ingen åtgärd. Det är därför viktigt att räkna upp modulens version varje gång som du vill distribuera nya ändringar. Öka versionen genom att ändra den **version** egenskapen under den **taggen** -egenskapen i module.json-filen för modulen som du ändrar. Sedan skapar och publicerar modulen.

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>Skapa och publicera

1. I Visual Studio Code på utvecklingen av din virtuella dator, öppna ett terminalfönster för Visual Studio Code och logga in på ditt behållarregister.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. Högerklicka på deployment.template.json i Visual Studio Code och välj **Build and Push IoT Edge-lösning**.

### <a name="view-modules-in-the-registry"></a>Visa moduler i registret

När bygget har slutförts kommer vi att kunna använda Azure portal för att granska våra publicerade moduler.

1. I Azure-portalen går du till din arbetsyta för Azure Machine Learning-tjänsten och klickar på hyperlänken för **registret**.

    ![Navigera till registernyckeln från machine learning-arbetsyta](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. Från registret på klientsidan Navigatören, väljer **databaser**.

3. Observera att båda modulerna du skapat, **avrofilewriter** och **turbofanrouter**, visas som databaser.

4. Välj **turbofanrouter** och Observera att du har publicerat en bild som taggats som 0.0.1-amd64.

   ![Visa första taggade versionen av turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Distribuera moduler på IoT Edge-enhet

Vi har skapats och konfigurerats modulerna i vår lösning, nu ska vi Distribuera modulerna till IoT Edge-enhet.

1. I Visual Studio Code, högerklicka på den **deployment.amd64.json** fil i config-mappen.

2. Välj **skapa distribution för enskild enhet**.

3. Välj din IoT Edge-enhet **aaTurboFanEdgeDevice**.

4. Uppdatera panelen Azure IoT Hub-enheter i Visual Studio Code-Utforskaren. Du bör se att tre nya moduler distribueras men ännu inte körs.

5. Uppdatera igen efter några minuter och du kommer att se de moduler som körs.

   ![Visa pågående moduler i Visual Studio Code](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Det kan ta flera minuter för vilka moduler som ska starta och uppnå en stadig körtillstånd. Under den tiden kan du se moduler starta och stoppa när de försöker upprätta en anslutning med IoT Edge hub-modul.

## <a name="diagnosing-failures"></a>Diagnostisera fel

I det här avsnittet ska dela vi några metoder för att förstå vad har gått fel med en modulen eller modulerna. Ett fel kan ofta först vara spotted från statusen i Visual Studio Code.

### <a name="identify-failed-modules"></a>Identifiera misslyckade moduler

* **Visual Studio Code:** Titta på panelen för Azure IoT Hub-enheter. Om de flesta moduler är körs men en stoppas, måste du undersöka stoppad modulen ytterligare. Om alla moduler som är i ett stoppat tillstånd för en längre tidsperiod kan bero det på fel samt.

* **Azure-portalen:** Genom att gå till din IoT-hubb i portalen och att hitta sidan med enhetsinformation (öka detaljnivån i din enhet under IoT Edge) kanske du upptäcker att en modul har rapporterat ett fel eller aldrig har rapporterat något till IoT hub.

### <a name="diagnosing-from-the-device"></a>Diagnostisera från enheten

Genom att logga in på IoT Edge-enhet får du åtkomst till en hel del information om status för dina moduler. Den huvudsakliga mekanism som vi använder är Docker-kommandon som låter oss undersöka behållare och bilder på enheten.

1. Lista alla behållare som körs. Vi förväntar oss att se en behållare för varje modul med ett namn som motsvarar modulen. Det här kommandot visar också en exakt kopia för behållaren, inklusive version så att du kan matcha med dina förväntningar. Du kan också visa bilder genom att byta ut ”bild” för ”behållare” i kommandot.

   ```bash
   sudo docker container ls
   ```

2. Hämta loggarna för en behållare. Detta kommando visar vad som har skrivits till StdErr och StdOut i behållaren. Det här kommandot fungerar för behållare som har startat och sedan bröts av någon anledning. Det är också användbart för att förstå vad händer med edgeAgent eller edgeHub behållare.

   ```bash
   sudo docker container logs <container name>
   ```

3. Granska en behållare. Det här kommandot ger en mängd information om avbildningen. Data kan filtreras beroende på vad du letar efter. Till exempel om du vill se om binds på avroFileWriter är korrekta kan du använda kommandot:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Anslut till en behållare som körs. Det här kommandot kan vara användbart om du vill undersöka behållaren när den körs:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har skapat vi en IoT Edge-lösning i Visual Studio Code med tre moduler, en klassificerare, en router och en fil-skrivaren /-överförare. Vi konfigurerar vägarna så att vilka moduler som ska kommunicera med varandra på edge-enhet, ändrade konfigurationen för edge-enhet och uppdateras Dockerfiles för att installera beroenden och Lägg till bindning monterar till de moduler behållare. Nu ska vi har uppdaterat konfigurationen av IoT-hubben att dirigera våra meddelanden baserat på typen och för att hantera filöverföringar. Med allt på plats, vi distribuerade moduler på IoT Edge-enheten och sett till modulerna som kördes på rätt sätt.

Mer information finns på följande sidor:

* [Lär dig hur du distribuerar moduler och upprätta vägar i IoT Edge](module-composition.md)
* [IoT Hub meddelanderoutning frågesyntax](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [IoT Hub meddelanderoutning: nu med routning på meddelandetext](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [Ladda upp filer med IoT Hub](../iot-hub/iot-hub-devguide-file-upload.md)
* [Ladda upp filer från din enhet till molnet med IoT Hub](../iot-hub/iot-hub-python-python-file-upload.md)

Fortsätt till nästa artikel för att börja skicka data och se din lösning i praktiken.

> [!div class="nextstepaction"]
> [Skicka data via en transparent gateway](tutorial-machine-learning-edge-07-send-data-to-hub.md)
