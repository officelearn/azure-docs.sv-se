---
title: Självstudie om Azure IoT Edge Python | Microsoft Docs
description: Den här självstudien visar hur du skapar en IoT Edge-modul med Python-kod och distribuerar den till en gränsenhet.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 11/25/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 00e04f4cab11b33dc0d7bf718ac15009c673727f
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52312824"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>Självstudie: Utveckla och distribuera en Python IoT Edge-modul till din simulerade enhet

Du kan använda Azure IoT Edge-moduler för att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. Du använder den simulerade IoT Edge-enhet som du skapade i snabbstarterna. I den här guiden får du lära dig att:    

> [!div class="checklist"]
> * använda Visual Studio Code till att skapa en IoT Edge Python-modul
> * använda Visual Studio Code och Docker till att skapa en Docker-avbildning och publicera den till registret
> * distribuera modulen till din IoT Edge-enhet
> * visa genererade data.


IoT Edge-modulen du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys vid kanten är användbar när du vill minska mängden data som skickas till och lagras i molnet. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Nödvändiga komponenter

En Azure IoT Edge-enhet:

* Du kan använda utvecklingsdatorn eller en virtuell dator som en gränsenhet genom att följa stegen i snabbstarten för [Linux-](quickstart-linux.md).
* Python-moduler för IoT Edge stöder inte Windows-enheter.

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure. 

Utvecklingsresurser:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge-tillägg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) för Visual Studio Code.
* [Python-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-python.python) för Visual Studio Code. 
* [Docker CE](https://docs.docker.com/engine/installation/). 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) för att installera Python-paket (ingår vanligtvis i Python-installationen).

>[!Note]
>Se till att din `bin`-mapp finns på sökvägen för din plattform. Vanligtvis `~/.local/` för UNIX och macOS, eller `%APPDATA%\Python` i Windows.

## <a name="create-a-container-registry"></a>Skapa ett containerregister

I den här självstudien använder du Azure IoT Edge-tillägget för Visual Studio Code för att skapa en modul och skapa en **containeravbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.  

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
   | SKU | Välj **Grundläggande**. | 

5. Välj **Skapa**.

6. När du har skapat containerregistret går du till det och väljer **Åtkomstnycklar**. 

7. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du kan använda dessa värden senare i självstudien för att ge åtkomst till containerregistret. 

## <a name="create-an-iot-edge-module-project"></a>Skapa ett projekt för IoT Edge-modulen
Följande steg skapar en IoT Edge Python-modul med Visual Studio Code och Azure IoT Edge-tillägget.

### <a name="create-a-new-solution"></a>Skapa en ny lösning

Använd Python-paketet **cookiecutter** för att skapa en Python-lösningsmall som du kan utgå ifrån. 

1. I Visual Studio Code väljer du **Visa** > **Terminal** för att öppna den integrerade Visual Studio Code-terminalen.

2. Installera (eller uppdatera) **cookiecutter** som du använde för att skapa IoT Edge-lösningsmallen i VS Code genom att köra följande kommando i den integrerade terminalen:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >Se till att katalogen där cookiecutter installeras är i PATH (sökvägen) för din miljö så att det blir möjligt att anropa det från en kommandotolk. Katalogen är en del av utdata för installationsskriptet, till exempel `C:\Users\{user}\AppData\Roaming\Python\Python{version}\Scripts`.
   >
   >Starta om Visual Studio Code för att hämta ändringarna till PATH (sökvägen). 

3. Välj **Visa** > **Kommandopalett** för att öppna kommandopaletten i VS Code. 

4. Ange och kör kommandot **Azure: Logga in** på kommandopaletten och följ anvisningarna för att logga in med ditt Azure-konto. Om du redan är inloggad kan du hoppa över det här steget.

5. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) på kommandopaletten. Skapa lösningen genom att följ anvisningarna på kommandopaletten.

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **Python-modul**. |
   | Ange ett modulnamn | Ge modulen namnet **PythonModule**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen har fyllts i från föregående steg. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. Den slutliga strängen ser ut så här: \<registernamn\>.azurecr.io/pythonmodule. |
 
   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-python-module/repository.png)

VS Code läser in arbetsytan för IoT Edge-lösningen. Lösningens arbetsyta innehåller fem komponenter på den högsta nivån. Mappen **modules** innehåller Python-koden för din modul samt Dockerfiles som används för att skapa modulen som en containeravbildning. Filen **\.env** lagrar dina autentiseringsuppgifter för containerregistret. Filen **deployment.template.json** innehåller informationen som IoT Edge-körningen använder för att distribuera moduler på en enhet. Och filen **deployment.debug.template.json** innehåller felsökningsversionen av modulerna. Du kommer inte att redigera mappen **\.vscode** eller filen **\.gitignore** i den här självstudiekursen.  

Om du inte angav ett containerregister när du skapade lösningen, men accepterade standardvärdet localhost:5000, har du ingen \.env-fil. 

<!--
   ![Python solution workspace](./media/tutorial-python-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerlagringsplatsen och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten. 

1. Öppna .env-filen i VS Code-utforskaren. 
2. Uppdatera fälten med det **användarnamn** och **lösenord** som du kopierade från Azure Container-registret. 
3. Spara filen. 

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

Exempelkod ingår i alla mallar. Koden hämtar simulerade sensordata från **tempSensor**-modulen och skickar dem till IoT-hubben. I det här avsnittet lägger du till kod som expanderar **PythonModule** för att analysera meddelanden innan de skickas. 

1. Öppna **modules** > **PythonModule** > **main.py** i VS Code-utforskaren.

2. Längst upp i filen **main.py** importerar du **json**-biblioteket:

    ```python
    import json
    ```

3. Lägg till variablerna **TEMPERATURE_THRESHOLD** och **TWIN_CALLBACKS** under de globala räknarna. Tröskelvärdet för temperatur är det värde som den uppmätta datortemperaturen måste överstiga för att data ska skickas till IoT-hubben.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. Ersätt funktionen **receive_message_callback** med följande kod:

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we forward this message to the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

5. Lägg till en ny funktion med namnet **module_twin_callback**. Funktionen anropas när önskade egenskaper uppdateras.

    ```python
    # module_twin_callback is invoked when the module twin's desired properties are updated.
    def module_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print ( "\nTwin callback called with:\nupdateStatus = %s\npayload = %s\ncontext = %s" % (update_state, payload, user_context) )
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
    ```

6. I klassen **HubManager** lägger du till en ny rad i metoden **__init__** som initierar funktionen **module_twin_callback** du precis lade till:

    ```python
    # Sets the callback when a module twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. Spara filen.

8. Öppna filen **deployment.template.json** i VS Code-utforskaren. 

   Den här filen anger att **$edgeAgent** ska distribuera två moduler: **tempSensor** som simulerar enhetsdata och **PythonModule**. Standardplattformen för din IoT Edge är inställd på **amd64** i VS Code-statusfältet, vilket innebär att **PythonModule** är inställd på Linux amd64-versionen för avbildningen. Ändra standardplattformen i statusfältet från **amd64** till **arm32v7** eller **windows-amd64** om det är arkitekturen för din IoT Edge-enhet. Läs mer om distributionsmanifest i avsnittet om att [förstå hur IoT Edge-moduler kan användas, konfigureras och återanvändas](module-composition.md).

   Den här filen innehåller även autentiseringsuppgifter för registret. Ditt användarnamn och lösenord fylls i med platshållare i mallfilen. När du genererar distributionsmanifestet uppdateras fälten med de värden du lade till i .env-filen. 

9. Lägg till **PythonModule**-modultvillingen i distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet **moduleContent** efter **$edgeHub**-modultvillingen: 

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Lägga till modultvilling till distributionsmall](./media/tutorial-python-module/module-twin.png)

10. Spara filen.

## <a name="build-and-push-your-solution"></a>Skapa och push-överföra lösningen

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod i **PythonModule** för att filtrera ut meddelanden om att temperaturen för den rapporterade datorn ligger under det godkända tröskelvärdet. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister. 

1. Logga in på Docker genom att ange följande kommando i den integrerade Visual Studio Code-terminalen: Sedan kan du skicka modulavbildningen till ditt Azure-containerregister: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Använd användarnamnet, lösenordet och inloggningsservern du kopierade från Azure-containerregistret i det första avsnittet. Du kan också hämta dessa värden från avsnittet **Åtkomstnycklar** i ditt register i Azure Portal.

2. I VS Code-utforskaren högerklickar du på filen deployment.template.json och väljer **Build and Push IoT Edge solution** (Skapa och skicka IoT Edge-lösning). 

När du instruerar Visual Studio Code att skapa din lösning hämtar den först information från distributionsmallen och genererar en .json-distributionsfil i en ny mapp med namnet **config**. Sedan körs två kommandon i en integrerad terminal: `docker build` och `docker push`. Dessa två kommandon skapar koden, lägger Python-koden i en container och push-överför sedan koden till containerregistret du angav när du initierade lösningen. 

Den fullständiga adressen med tagg för containeravbildningen i `docker build`-kommandot som körs i den integrerade VS Code-terminalen. Avbildningsadressen skapas från information i filen module.json och har formatet \<lagringsplats\>:\<version\>-\<plattform\>. I den här självstudien ska adressen se ut så här: registryname.azurecr.io/pythonmodule:0.0.1-amd64.

## <a name="deploy-and-run-the-solution"></a>Distribuera och kör lösningen

I stegen i snabbstartsartikeln som du följde för att konfigurera IoT Edge-enheten distribuerade du en modul med hjälp av Azure Portal. Du kan även distribuera moduler med Azure IoT Toolkit-tillägget för Visual Studio Code. Du har redan ett distributionsmanifest som är förberett för ditt scenario, filen **deployment.json**. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

1. I kommandopaletten i VS Code kör du **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: Välj IoT Hub). 

2. Välj den prenumeration och IoT-hubb som innehåller den IoT Edge-enhet som du vill konfigurera. 

3. I VS Code-utforskaren expanderar du avsnittet **Azure IoT Hub-enheter**. 

4. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Create Deployment for Single Device** (Skapa distribution för en enskild enhet). 

   ![Skapa distribution för en enskild enhet](./media/tutorial-python-module/create-deployment.png)

5. Välj filen **deployment.json** i mappen **config** och klicka sedan på **Select Edge Deployment Manifest** (Välj distributionsmanifest för Edge). Använd inte filen deployment.template.json. 

6. Klicka på uppdateringsknappen. Du bör se den nya **PythonModule** köras tillsammans med **TempSensor**-modulen och **$edgeAgent** och **$edgeHub**. 

## <a name="view-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas. 

Du kan visa statusen för din IoT Edge-enhet i avsnittet om **Azure IoT Hub-enheter** i Visual Studio Code-utforskaren. Expandera enhetsinformationen så ser du en lista med moduler som distribueras och körs. 

Med hjälp av kommandot `iotedge list` på själva IoT Edge-enheten kan du se statusen för dina distributionsmoduler. Du bör se fyra moduler: de modulerna för IoT Edge-körning, tempSensor och den anpassade modul du skapade i den här självstudien. Det kan ta några minuter för alla moduler att starta, så kör kommandot igen om du till en början inte ser alla. 

Du kan visa de meddelanden som genereras av alla moduler med hjälp av kommandot `iotedge logs <module name>`. 

Du kan visa meddelanden när de anländer till IoT-hubben med hjälp av Visual Studio Code. 

1. Om du vill övervaka data som kommer till IoT-hubben väljer du ellipsen (**...** ) och sedan **Start Monitoring D2C Messages** (Starta övervakning av D2C-meddelanden).
2. Om du vill övervaka D2C-meddelandet för en specifik enhet högerklickar du på enheten i listan och väljer **Starta övervakning av D2C-meddelanden**.
3. Om du vill stoppa dataövervakningen kör du kommandot **Azure IoT Hub: Sluta övervaka D2C-meddelande** på kommandopaletten. 
4. Om du vill visa eller uppdatera modultvillingen högerklickar du på modulen i listan och väljer **Redigera modultvilling**. Om du vill uppdatera modultvillingen sparar du JSON-tvillingfilen, högerklickar i redigeringsområdet och väljer **Uppdatera modultvilling**.
5. Om du vill visa Docker-loggar installerar du [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) för VS Code. Du kan hitta de moduler som körs lokalt i Docker-utforskaren. I kontextmenyn klickar du på **Visa loggar** om du vill se dem i den integrerade terminalen. 

## <a name="clean-up-resources"></a>Rensa resurser 

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet. 

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

### <a name="delete-local-resources"></a>Ta bort lokala resurser

Om du vill ta bort IoT Edge-körningen och relaterade resurser från enheten kan du använda följande kommandon. 

Ta bort IoT Edge-körningen.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

När IoT Edge-körningen tas bort stoppas de containrar som den skapade, men de finns fortfarande kvar på enheten. Visa alla containrar.

   ```bash
   sudo docker ps -a
   ```

Ta bort de körningscontainrar som har skapats på enheten.

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Ta bort alla ytterligare containrar som visades i `docker ps`-utdata genom att referera till containernamn. 

Ta bort körmiljön för containern.

   ```bash
   sudo apt-get remove --purge moby
   ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul med kod för att filtrera rådata som genereras av din IoT Edge-enhet. Du kan fortsätta med någon av följande självstudier om du vill lära dig mer om hur Azure IoT Edge kan hjälpa dig att omvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Distribuera Azure-funktioner som en modul](tutorial-deploy-function.md)
> [Distribuera Azure Stream Analytics som en modul](tutorial-deploy-stream-analytics.md)
