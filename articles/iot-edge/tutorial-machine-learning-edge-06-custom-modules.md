---
title: 'Självstudiekurs: Skapa och distribuera anpassade moduler – Machine Learning på Azure IoT Edge'
description: Den här självstudien visar hur du skapar och distribuerar IoT Edge-moduler som bearbetar data från lövenheter via en maskininlärningsmodell och sedan skickar insikterna till IoT Hub.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3cba7781ac80ae567b2bfd54c4131429ed94b90f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75772371"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Självstudiekurs: Skapa och distribuera anpassade IoT Edge-moduler

> [!NOTE]
> Den här artikeln är en del av en serie för en självstudiekurs om hur du använder Azure Machine Learning på IoT Edge. Om du har kommit till den här artikeln direkt, uppmuntrar vi dig att börja med den [första artikeln](tutorial-machine-learning-edge-01-intro.md) i serien för bästa resultat.

I den här artikeln skapar vi tre IoT Edge-moduler som får meddelanden från lövenheter, kör data via din maskininlärningsmodell och sedan vidarebefordrar insikter till IoT Hub.

IoT Edge-hubben underlättar modul till modulkommunikation. Om du använder IoT Edge-hubben som meddelandemäklare håller modulerna oberoende av varandra. Moduler behöver bara ange de indata som de accepterar meddelanden och utdata som de skriver meddelanden till.

Vi vill att IoT Edge-enheten ska åstadkomma fyra saker för oss:

* Ta emot data från lövenheterna
* Förutse återstående livslängd (RUL) för enheten som skickade data
* Skicka ett meddelande med endast RUL för enheten till IoT Hub (den här funktionen kan ändras för att bara skicka data om RUL sjunker under någon nivå)
* Spara lövenhetsdata i en lokal fil på IoT Edge-enheten. Den här datafilen överförs regelbundet till IoT Hub via filöverföring för att förfina utbildningen av maskininlärningsmodellen. Det är mer kostnadseffektivt att använda filuppladdning i stället för konstant meddelandeströmning.

För att utföra dessa uppgifter använder vi tre anpassade moduler:

* **RUL-klassificerare:** TurboFanRulClassifier-modulen som vi skapade i [Train och distribuera en Azure Machine Learning-modell](tutorial-machine-learning-edge-04-train-model.md) är en standardmodul för maskininlärning, som exponerar en indata som kallas "amlInput" och en utdata som kallas "amlOutput". Den "amlInput" förväntar sig att dess ingång ska se exakt ut som den ingång som vi skickade till ACI-baserade webbtjänsten. På samma sätt returnerar "amlOutput" samma data som webbtjänsten.

* **Avro författare:** Den här modulen tar emot meddelanden på indata "avroModuleInput" och beständiga meddelandet i Avro-format till disk för senare överföring till IoT Hub.

* **Router Modul:** Routermodulen tar emot meddelanden från underordnade bladenheter, formaterar och skickar meddelandena till klassificeraren. Modulen tar sedan emot meddelandena från klassificeraren och vidarebefordrar meddelandet till Avro-skribentmodulen. Slutligen skickar modulen bara RUL-förutsägelsen till IoT Hub.

  * Ingångar:
    * **deviceInput**: tar emot meddelanden från lövenheter
    * **rulInput:** tar emot meddelanden från "amlOutput"

  * Utgångar:
    * **klassificera:** skickar meddelanden till "amlInput"
    * **writeAvro:** skickar meddelanden till "avroModuleInput"
    * **toIotHub:** skickar meddelanden till $upstream, som skickar meddelandena till den anslutna IoT Hub

Diagrammet nedan visar moduler, ingångar, utgångar och IoT Edge Hub-vägarna för den fullständiga lösningen:

![Arkitekturdiagram för IoT Edge tre moduler](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

Stegen i den här artikeln utförs vanligtvis av en molnutvecklare.

## <a name="create-a-new-iot-edge-solution"></a>Skapa en ny IoT Edge-lösning

Under körningen av den andra av våra två Azure-anteckningsböcker skapade och publicerade vi en behållaravbildning som innehåller vår RUL-modell. Azure Machine Learning, som en del av processen för att skapa avbildningen, paketerade den modellen så att avbildningen kan distribueras som en Azure IoT Edge-modul. I det här steget kommer vi att skapa en Azure IoT Edge-lösning med modulen "Azure Machine Learning" och peka modulen till den avbildning vi publicerade med Azure Notebooks.

1. Öppna en fjärrskrivbordssession på utvecklingsdatorn.

2. Öppna mapp **\\C: källa\\IoTEdgeAndMlSample** i Visual Studio-kod.

3. Högerklicka på utforskarpanelen (i det tomma utrymmet) och välj **Ny IoT Edge-lösning**.

    ![Skapa ny IoT Edge-lösning](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. Acceptera standardlösningsnamnet **EdgeSolution**.

5. Välj **Azure Machine Learning** som modulmall.

6. Namnge modulen **turbofanRulClassifier**.

7. Välj arbetsyta för maskininlärning.

8. Välj den avbildning som du skapade när du körde Den Azure Notebook.

9. Titta på lösningen och lägg märke till de filer som har skapats:

   * **deployment.template.json:** Den här filen innehåller definitionen av var och en av modulerna i lösningen. Det finns tre avsnitt att uppmärksamma i den här filen:

     * **Registerautentiseringsuppgifter:** Definierar uppsättningen anpassade behållarregister som du använder i lösningen. Just nu bör det innehålla registret från din maskininlärningsarbetsyta, vilket är där din Azure Machine Learning-avbildning lagrades. Du kan ha valfritt antal containerregister, men för enkelhetens skull kommer vi att använda detta register för alla moduler

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Moduler:** Det här avsnittet innehåller en uppsättning användardefinierade moduler som följer med den här lösningen. Du kommer att märka att det här avsnittet för närvarande innehåller två moduler: SimulatedTemperatureSensor och turbofanRulClassifier. SimulatedTemperatureSensor installerades av Visual Studio-kodmallen, men vi behöver den inte för den här lösningen. Du kan ta bort modulen SimuleradTemperatureSensor-moduldefinition från modulen. Observera att turbofanRulClassifier-modulens definition pekar på avbildningen i behållarregistret. När vi lägger till fler moduler till lösningen kommer de att dyka upp i det här avsnittet.

       ```json
       "modules": {
         "SimulatedTemperatureSensor": {
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

     * **Rutter:** vi kommer att arbeta med rutter en hel del i den här guiden. Vägar definierar hur moduler kommunicerar med varandra. De två vägar som definieras av mallen matchar inte med den routning vi behöver. Den första vägen skickar alla data från alla utdata från klassificeraren till IoT Hub ($upstream). Den andra vägen är för SimulatedTemperatureSensor, som vi just tagit bort. Ta bort de två standardvägarna.

       ```json
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
             "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/\* INTO $upstream",
             "sensorToturbofanRulClassifier": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\\"/modules/turbofanRulClassifier/inputs/input1\\")"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       }
       ```

   * **deployment.debug.template.json:** den här filen är felsökningsversionen av deployment.template.json. Vi bör spegla alla ändringar från deployment.template.json i den här filen.

   * **.env:** den här filen är där du bör ange användarnamn och lösenord för att komma åt registret.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. Högerklicka på filen deployment.template.json i Utforskaren för Visual Studio-kod och välj **Build IoT Edge Solution**.

11. Observera att det här kommandot skapar en konfigurationsmapp med en deployment.amd64.json-fil. Den här filen är den konkreta distributionsmallen för lösningen.

## <a name="add-router-module"></a>Lägg till routermodul

Därefter lägger vi routermodulen till vår lösning. Routermodulen hanterar flera ansvarsområden för vår lösning:

* **Ta emot meddelanden från lövenheter:** när meddelanden kommer till IoT Edge-enheten från nedströmsenheter tar routermodulen emot meddelandet och börjar dirigera routningen av meddelandet.
* **Skicka meddelanden till RUL-klassifierarmodulen:** När ett nytt meddelande tas emot från en nedströmsenhet omvandlar routermodulen meddelandet till det format som RUL Classifier förväntar sig. Routern skickar meddelandet till RUL-klassificeraren för en RUL-förutsägelse. När klassificeraren har gjort en förutsägelse skickas meddelandet tillbaka till routermodulen.
* **Skicka RUL-meddelanden till IoT Hub:** när routern tar emot meddelanden från klassificeraren omvandlar den meddelandet så att det bara innehåller viktig information, enhets-ID och RUL och skickar det förkortade meddelandet till IoT-hubben. En ytterligare förfining, som vi inte har gjort här, skulle skicka meddelanden till IoT Hub endast när RUL förutsägelse faller under ett tröskelvärde (till exempel när RUL är färre än 100 cykler). Filtrering på detta sätt skulle minska volymen av meddelanden och minska kostnaderna för IoT-hubben.
* **Skicka meddelande till Avro Writer-modulen:** för att bevara alla data som skickas av nedströmsenheten skickar routermodulen hela meddelandet som tas emot från klassificeraren till Avro Writer-modulen, som kommer att finnas kvar och ladda upp data med hjälp av IoT Hub-filöverföring.

> [!NOTE]
> Beskrivningen av modulens ansvar kan göra att bearbetningen verkar sekventiell, men flödet är meddelande/händelsebaserat. Det är därför vi behöver en orkestrering modul som vår Router modul.

### <a name="create-module-and-copy-files"></a>Skapa modul- och kopieringsfiler

1. Högerklicka på modulens mapp i Visual Studio Code och välj **Lägg till IoT Edge Module**.

2. Välj **C#-modul**.

3. Namnge modulen **turbofanRouter**.

4. När du uppmanas att ange Docker Image Repository använder du registret från maskininlärningsarbetsytan (du hittar registret i registeredCredentials-noden i *filen deployment.template.json).* Det här värdet är den fullständigt kvalificerade adressen till registret, till ** \<exempel registret\>.azurecr.io/turbofanrouter**.

    > [!NOTE]
    > I den här artikeln använder vi Azure Container Registry som skapats av Azure Machine Learning-arbetsytan, som vi använde för att träna och distribuera vår klassificerare. Detta är enbart för enkelhetens skull. Vi kunde ha skapat ett nytt containerregister och publicerat våra moduler där.

5. Öppna ett nytt terminalfönster i Visual Studio Code (**View** > **Terminal**) och kopiera filer från modulerna katalogen.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. När du uppmanas att skriva över `y` program.cs trycker `Enter`du på och trycker sedan på .

### <a name="build-router-module"></a>Skapa routermodul

1. I Visual Studio-kod väljer du **Terminal** > **Configure Standard build-uppgift**.

2. Klicka på **Skapa tasks.json-fil från mallen**.

3. Klicka på **.NET Core**.

4. När tasks.json öppnas ersätter du innehållet med:

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

5. Spara och stäng tasks.json.

6. Kör bygg `Ctrl + Shift + B` med eller **Terminal** > **Run Build Task**.

### <a name="set-up-module-routes"></a>Ställ in modulvägar

Som nämnts ovan använder IoT Edge-körningen vägar som konfigurerats i *filen deployment.template.json* för att hantera kommunikation mellan löst kopplade moduler. I det här avsnittet borrar vi in i hur du ställer in rutterna för turbofanRouter-modulen. Vi kommer att täcka inmatningsvägarna först och sedan flytta på utgångarna.

#### <a name="inputs"></a>Indata

1. I init() metoden för Program.cs registrerar vi två motringningar för modulen:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. Den första motringningen lyssnar efter meddelanden som skickas till **deviceInput-diskhon.** Från diagrammet ovan ser vi att vi vill dirigera meddelanden från valfri lövenhet till den här indatan. I filen *deployment.template.json* lägger du till en väg som talar om för kanthubben att dirigera alla meddelanden som tas emot av IoT Edge-enheten som inte skickades av en IoT Edge-modul i indata som kallas "deviceInput" på turbofanRouter-modulen:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Lägg sedan till en rutt för meddelanden från rulClassifier-modulen i turbofanRouter-modulen:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Utdata

Lägg till ytterligare fyra vägar till parametern $edgeHub för att hantera utdata från routermodulen.

1. Program.cs definierar metoden SendMessageToClassifier(), som använder modulklienten för att skicka ett meddelande till RUL-klassificeraren med hjälp av vägen:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub() använder modulklienten för att bara skicka RUL-data för enheten till IoT Hub via rutten:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter() använder modulklienten för att skicka meddelandet med RUL-data som lagts till i avroFileWriter-modulen.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() skickar misslyckade meddelanden uppströms IoT Hub där de kan dirigeras till senare.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Med alla rutter tillsammans din "$edgeHub" nod ska se ut så här JSON:

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
> Genom att lägga till turbofanRouter-modulen skapades följande ytterligare rutt: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`. Ta bort den här vägen och lämna endast de vägar som anges ovan i filen deployment.template.json.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Kopiera vägar till deployment.debug.template.json

Som ett sista steg, för att hålla våra filer synkroniserade, spegla de ändringar du gjort i deployment.template.json i deployment.debug.template.json.

## <a name="add-avro-writer-module"></a>Lägg till Avro Writer-modul

Avro Writer-modulen har två ansvarsområden i vår lösning, att lagra meddelanden och ladda upp filer.

* **Lagra meddelanden**: när Avro Writer-modulen tar emot ett meddelande skriver den meddelandet till det lokala filsystemet i Avro-format. Vi använder en bindfäste, som monterar en katalog (i det här fallet /data/avrofiles) i en sökväg i modulens behållare. Detta fäste gör det möjligt för modulen att skriva till en lokal sökväg (/avrofiles) och ha dessa filer tillgängliga direkt från IoT Edge-enheten.

* **Ladda upp filer:** Avro Writer-modulen använder azure IoT Hub-filöverföringsfunktionen för att överföra filer till ett Azure-lagringskonto. När en fil har laddats upp tas filen bort från disken

### <a name="create-module-and-copy-files"></a>Skapa modul- och kopieringsfiler

1. Sök efter **python: Välj Tolk**i kommandopaletten.

1. Välj den tolk som\\finns i C: Python37.

1. Öppna kommandopaletten igen och sök efter och välj sedan **Terminal: Välj Standardskal**.

1. När du uppmanas till det väljer du **Kommandotolken**.

1. Öppna ett nytt terminalskal, **Terminal** > **New Terminal**.

1. Högerklicka på modulens mapp i Visual Studio Code och välj **Lägg till IoT Edge Module**.

1. Välj **Python-modul**.

1. Namnge modulen "avroFileWriter".

1. När du uppmanas till Docker Image Repository använder du samma register som du använde när du lade till routermodulen.

1. Kopiera filer från exempelmodulen till lösningen.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Om du uppmanas att skriva över `y` main.py skriver `Enter`du och trycker sedan på .

1. Observera att filemanager.py och schema.py har lagts till i lösningen och att main.py har uppdaterats.

> [!NOTE]
> När du öppnar en Python-fil kan du bli ombedd att installera pylint. Du behöver inte installera linter för att slutföra den här självstudien.

### <a name="bind-mount-for-data-files"></a>Bind fäste för datafiler

Som nämnts i introt, författaren modulen förlitar sig på förekomsten av binda montera för att skriva Avro filer till enhetens filsystem.

#### <a name="add-directory-to-device"></a>Lägga till katalog på enheten

1. Anslut till din VM-enhet i IoT Edge med SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Skapa katalogen som ska innehålla de sparade bladenhetsmeddelandena.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Uppdatera katalogbehörigheter för att göra den skrivbar av behållaren.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. Verifiera katalogen har nu skriv-(w) behörighet för användare, grupp och ägare.

   ```bash
   ls -la /data
   ```

   ![Katalogbehörigheter för avrofiler](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Lägga till katalog i modulen

Om du vill lägga till katalogen i modulens behållare ändrar vi dockerfilerna som är associerade med avroFileWriter-modulen. Det finns tre Dockerfiles associerade med modulen: Dockerfile.amd64, Dockerfile.amd64.debug och Dockerfile.arm32v7. Dessa filer bör hållas synkroniserade om vi vill felsöka eller distribuera till en arm32-enhet. För den här artikeln, fokusera bara på Dockerfile.amd64.

1. Öppna filen **Dockerfile.amd64** på utvecklingsmaskinen.

2. Ändra filen så att den ser ut som följande exempel:

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

   `mkdir` Kommandona `chown` och instruerar Docker-byggprocessen att skapa en katalog på den översta nivån som kallas /avrofiles i avbildningen och sedan göra moduleuser till ägare av den katalogen. Det är viktigt att dessa kommandon infogas när modulanvändaren `useradd` har lagts till i avbildningen med kommandot och innan kontexten växlar till moduleuser (USER moduleuser).

3. Gör motsvarande ändringar i Dockerfile.amd64.debug och Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Uppdatera modulkonfigurationen

Det sista steget för att skapa bindningen är att uppdatera filerna deployment.template.json (och deployment.debug.template.json) med bindningsinformationen.

1. Öppna deployment.template.json.

2. Ändra moduldefinitionen för avroFileWriter genom att lägga till `Binds` parametern som pekar behållarkatalogen /avrofiles till den lokala katalogen på kantenheten. Moduldefinitionen bör matcha det här exemplet:

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

3. Gör motsvarande ändringar i deployment.debug.template.json.

### <a name="bind-mount-for-access-to-configyaml"></a>Bind fäste för åtkomst till config.yaml

Vi måste lägga till ytterligare en binda för författaren modulen. Denna bindning ger modulen åtkomst till att läsa anslutningssträngen från filen /etc/iotedge/config.yaml på IoT Edge-enheten. Vi behöver anslutningssträngen för att skapa en IoTHubClient så att vi kan anropa metoden upload\_blob\_async för att ladda upp filer till IoT-hubben. Stegen för att lägga till den här bindningen liknar de i föregående avsnitt.

#### <a name="update-directory-permission"></a>Uppdatera katalogbehörighet

1. Anslut till din IoT Edge-enhet med SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Lägg till läsbehörighet i filen config.yaml.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. Verifiera att behörigheterna är korrekt inställda.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Kontrollera att behörigheterna för config.yaml är **-r--r--r--**.

#### <a name="add-directory-to-module"></a>Lägga till katalog i modul

1. Öppna filen **Dockerfile.amd64** på utvecklingsmaskinen.

2. Lägg till ytterligare `mkdir` `chown` en uppsättning kommandon och kommandon i filen så att det ser ut som:

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

3. Gör motsvarande ändringar i Dockerfile.amd64.debug och Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Uppdatera modulkonfigurationen

1. Öppna **filen deployment.template.json.**

2. Ändra moduldefinitionen för avroFileWriter genom `Binds` att lägga till en andra rad i parametern som pekar behållarens katalog (/app/iotconfig) till den lokala katalogen på enheten (/etc/iotedge).

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

3. Gör motsvarande ändringar i deployment.debug.template.json.

## <a name="install-dependencies"></a>Installera beroenden

Skrivmodulen är beroende av två Python-bibliotek, fastavro och PyYAML. Vi måste installera beroenden på vår utvecklingsmaskin och instruera Docker-byggprocessen att installera dem i vår moduls avbildning.

### <a name="pyyaml"></a>Pyyaml (på andra)

1. Öppna **filen requirements.txt på** din utvecklingsmaskin och lägg till pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Öppna **filen Dockerfile.amd64** och `pip install` lägg till ett kommando för att uppgradera installationsverktyg.

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

3. Gör motsvarande ändringar i Dockerfile.amd64.debug. <!--may not be necessary. Add 'if needed'?-->

4. Installera pyyaml lokalt genom att öppna en terminal i Visual Studio-kod och skriva

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro (fastavro)

1. I requirements.txt, tillsätt fastavro efter pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. Installera fastavro till din utvecklingsmaskin med Visual Studio Code-terminalen.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Konfigurera om IoT-hubb

Genom att introducera IoT Edge-enheten och modulerna i systemet har vi ändrat våra förväntningar på vilka data som ska skickas till navet och för vilket ändamål. Vi måste konfigurera om routing i navet för att hantera vår nya verklighet.

> [!NOTE]
> Vi konfigurerar om navet innan du distribuerar moduler eftersom vissa av navinställningarna, särskilt filöverföring, måste konfigureras korrekt för att avroFileWriter-modulen ska fungera korrekt

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Konfigurera väg för RUL-meddelanden i IoT Hub

Med routern och klassificeraren på plats förväntar vi oss att ta emot vanliga meddelanden som bara innehåller enhets-ID och RUL-förutsägelsen för enheten. Vi vill dirigera RUL-data till sin egen lagringsplats där vi kan övervaka status för enheterna, bygga rapporter och brandvarningar efter behov. Samtidigt vill vi att alla enhetsdata som fortfarande skickas direkt av en lövenhet som ännu inte har kopplats till vår IoT Edge-enhet ska fortsätta att dirigera till den aktuella lagringsplatsen.

#### <a name="create-a-rul-message-route"></a>Skapa en RUL-meddelandeväg

1. Navigera till din IoT Hub i Azure-portalen.

2. Välj **Meddelanderoutning**från vänster navigering .

3. Välj **Lägg till**.

4. Namnge rutten **RulMessageRoute**.

5. Välj **Lägg till** bredvid **slutpunktsväljaren** och välj **Blob-lagring**.

6. I formuläret **Lägg till en lagringsslutpunkt** namnger du slutpunkten **ruldata**.

7. Välj **Välj Välj en behållare**.

8. Välj det lagringskonto som används i hela den här självstudien, som heter som **iotedgeandml\<unika suffix\>**.

9. Välj **ruldata-behållaren** och klicka på **Markera**.

10. Klicka på **Skapa** om du vill skapa lagringsslutpunkten.

11. För **routningsfrågan**anger du följande fråga:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. Expandera avsnittet **Testa** och sedan avsnittet **Meddelande.** Ersätt meddelandet med det här exemplet på våra förväntade meddelanden:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. Välj **Testrutt**. Om testet lyckas visas "Meddelandet matchade frågan".

14. Klicka på **Spara**.

#### <a name="update-turbofandevicetostorage-route"></a>Uppdatera turbofanDeviceToStorage rutt

Vi vill inte dirigera de nya förutsägelsedata till vår gamla lagringsplats, så uppdatera rutten för att förhindra det.

1. Välj fliken Rutter på **routningssidan för** IoT Hub **Message.**

2. Välj **turbofanDeviceDataToStorage**eller vilket namn du gav till din ursprungliga enhetsdataväg.

3. Uppdatera routningsfrågan till

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. Expandera avsnittet **Testa** och sedan avsnittet **Meddelande.** Ersätt meddelandet med det här exemplet på våra förväntade meddelanden:

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

5. Välj **Testrutt**. Om testet lyckas visas "Meddelandet matchade frågan".

6. Välj **Spara**.

### <a name="configure-file-upload"></a>Konfigurera filuppladdning

Konfigurera funktionen för uppladdning av IoT Hub-filer så att filförfattaren kan överföra filer till lagring.

1. Välj **Filuppladdning**från den vänstra navigatorn i IoT Hub .

2. Välj **Azure Storage Container**.

3. Välj ditt lagringskonto i listan.

4. Markera behållaren **uploadturbofanfiles** och klicka på **Välj**.

5. Välj **Spara**. Portalen meddelar dig när sparan är klar.

> [!Note]
> Vi aktiverar inte överföringsmeddelandet för den här självstudien, men se [Ta emot en filöverföringsavisering](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) för mer information om hur du hanterar meddelande om filöverföring.

## <a name="build-publish-and-deploy-modules"></a>Skapa, publicera och distribuera moduler

Nu när vi har gjort konfigurationsändringarna är vi redo att skapa avbildningarna och publicera dem i vårt Azure-behållarregister. Byggprocessen använder filen deployment.template.json för att avgöra vilka moduler som behöver skapas. Inställningarna för varje modul, inklusive version, finns i module.json-filen i modulmappen. Byggprocessen kör först en Docker-version på Dockerfiles som matchar den aktuella konfigurationen som finns i module.json-filen för att skapa en avbildning. Sedan publicerar den avbildningen till registret från module.json-filen med en versionstagg som matchar den i module.json-filen. Slutligen producerar den ett konfigurationsspecifikt distributionsmanifest (till exempel deployment.amd64.json), som vi kommer att distribuera till IoT Edge-enheten. IoT Edge-enheten läser informationen från distributionsmanifestet och baserat på instruktionerna hämtar du modulerna, konfigurerar rutterna och anger önskade egenskaper. Den här distributionsmetoden har två biverkningar som du bör vara medveten om:

* **Distributionsfördröjning:** Eftersom IoT Edge-körningen måste känna igen ändringen av de önskade egenskaperna innan den börjar konfigureras om, kan det ta en viss tid efter att du har distribuerat dina moduler tills körningen hämtar dem och börjar uppdatera IoT Edge-enheten.

* **Modulversioner är viktiga:** Om du publicerar en ny version av en modulbehållare till behållarregistret med samma versionstaggar som den tidigare modulen hämtas inte den nya versionen av modulen. Det gör en jämförelse av versionstaggen för den lokala avbildningen och önskad avbildning från distributionsmanifestet. Om dessa versioner matchar, tar körningen ingen åtgärd. Därför är det viktigt att öka versionen av modulen varje gång du vill distribuera nya ändringar. Öka versionen genom **version** att ändra versionsegenskapen under **taggegenskapen** i module.json-filen för modulen som du ändrar. Sedan bygga och publicera modulen.

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

1. Öppna ett terminalfönster för Visual Studio-kod i Visual Studio-kod på utvecklings-VM och logga in på behållarregistret.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. Högerklicka på deployment.template.json i Visual Studio Code och välj **Bygg och push IoT Edge-lösning**.

### <a name="view-modules-in-the-registry"></a>Visa moduler i registret

När bygget har slutförts kan vi använda Azure-portalen för att granska våra publicerade moduler.

1. I Azure-portalen navigerar du till arbetsytan Azure Machine Learning och klickar på hyperlänken för **Registret**.

    ![Navigera till registret från maskininlärningstjänstens arbetsyta](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. Välj **Databaser på**registersidan.

3. Observera att båda modulerna du har skapat, **avrofilewriter** och **turbofanrouter**, visas som databaser.

4. Välj **turbofanrouter** och notera att du har publicerat en bild taggad som 0.0.1-amd64.

   ![Visa första taggade versionen av turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Distribuera moduler till IoT Edge-enhet

Vi har byggt och konfigurerat modulerna i vår lösning, nu kommer vi att distribuera modulerna till IoT Edge-enheten.

1. Högerklicka på filen **deployment.amd64.json** i konfigurationsmappen i Visual Studio-kod.

2. Välj **Skapa distribution för en enhet**.

3. Välj din IoT Edge-enhet, **aaTurboFanEdgeDevice**.

4. Uppdatera panelen Azure IoT Hub-enheter i Utforskaren för Visual Studio-kod. Du bör se att de tre nya modulerna har distribuerats men ännu inte körs.

5. Uppdatera igen efter några minuter och du kommer att se modulerna körs.

   ![Visa löpmoduler i Visual Studio-kod](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Det kan ta flera minuter för modulerna att starta och bosätta sig i ett stabilt körtillstånd. Under den tiden kan du se moduler starta och stoppa när de försöker upprätta en anslutning till IoT Edge-hubbmodulen.

## <a name="diagnosing-failures"></a>Diagnostisera fel

I det här avsnittet delar vi några tekniker för att förstå vad som har gått fel med en modul eller moduler. Ofta kan ett fel först upptäckas från statusen i Visual Studio-koden.

### <a name="identify-failed-modules"></a>Identifiera misslyckade moduler

* **Visual Studio-kod:** Titta på azure IoT Hub-enhetspanelen. Om de flesta moduler är i körtillstånd men en stoppas, måste du undersöka den stoppade modulen ytterligare. Om alla moduler är i stoppat tillstånd under en längre tid kan det tyda på fel också.

* **Azure-portal:** Genom att navigera till din IoT-hubb i portalen och sedan hitta sidan enhetsinformation (under IoT Edge, öka detaljnivån i enheten) kan det hända att en modul har rapporterat ett fel eller aldrig har rapporterat något till IoT-hubben.

### <a name="diagnosing-from-the-device"></a>Diagnostisera från enheten

Genom att logga in på IoT Edge-enheten kan du få tillgång till en hel del information om status för dina moduler. Den viktigaste mekanismen vi använder är Docker-kommandona som låter oss undersöka behållare och bilder på enheten.

1. Lista alla behållare som körs. Vi förväntar oss att se en behållare för varje modul med ett namn som motsvarar modulen. Det här kommandot visar också den exakta bilden för behållaren inklusive version så att du kan matcha med dina förväntningar. Du kan också lista bilder genom att ersätta "bild" för "behållare" i kommandot.

   ```bash
   sudo docker container ls
   ```

2. Hämta loggarna för en behållare. Det här kommandot matar ut det som har skrivits till StdErr och StdOut i behållaren. Detta kommando fungerar för behållare som har startat och sedan dog av någon anledning. Det är också användbart för att förstå vad som har hänt med edgeAgent eller edgeHub-behållarna.

   ```bash
   sudo docker container logs <container name>
   ```

3. Inspektera en behållare. Det här kommandot ger en ton information om bilden. Data kan filtreras beroende på vad du letar efter. Om du till exempel vill se om bindorna på avroFileWriter är korrekta kan du använda kommandot:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Anslut till en behållare som körs. Det här kommandot kan vara användbart om du vill undersöka behållaren medan den körs:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapade vi en IoT Edge-lösning i Visual Studio-kod med tre moduler, en klassificerare, en router och en filskrivare/uppladdare. Vi ställer in vägar så att modulerna kan kommunicera med varandra på kantenheten, ändrade konfigurationen av kantenheten och uppdaterade Dockerfiles för att installera beroenden och lägga till bindningsfästen till modulernas behållare. Därefter uppdaterade vi konfigurationen av IoT Hub för att dirigera våra meddelanden baserat på typ och för att hantera filöverföringar. Med allt på plats distribuerade vi modulerna till IoT Edge-enheten och såg till att modulerna kördes korrekt.

Mer information finns på följande sidor:

* [Lär dig hur du distribuerar moduler och etablerar vägar i IoT Edge](module-composition.md)
* [IoT Hub-frågesyntaxen för meddelandedirigering](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [IoT Hub-meddelanderoutning: nu med routning på meddelandetext](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [Ladda upp filer med IoT Hub](../iot-hub/iot-hub-devguide-file-upload.md)
* [Ladda upp filer från enheten till molnet med IoT Hub](../iot-hub/iot-hub-python-python-file-upload.md)

Fortsätt till nästa artikel för att börja skicka data och se din lösning i aktion.

> [!div class="nextstepaction"]
> [Skicka data via transparent gateway](tutorial-machine-learning-edge-07-send-data-to-hub.md)
