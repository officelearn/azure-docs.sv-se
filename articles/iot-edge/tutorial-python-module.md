---
title: Självstudie om Azure IoT Edge Python | Microsoft Docs
description: Den här självstudien visar hur du skapar en IoT Edge-modul med Python-kod och distribuerar den till en gränsenhet.
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5766f9708d2439f42f9ad77169fd1fe7f7dc451e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439120"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>Självstudie: Utveckla och distribuera en Python IoT Edge-modul till din simulerade enhet

Du kan använda Azure IoT Edge-moduler för att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. Du kommer att använda den simulerade IoT Edge-enheten som du skapade i snabbstarterna Distribuera Azure IoT Edge på en simulerad enhet i [Windows][lnk-quickstart-win] eller [Linux][lnk-quickstart-lin]. I den här guiden får du lära dig att:    

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
* Python-moduler för IoT Edge stöder inte ARM-processorer eller Windows-enheter.

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på standardnivå i Azure. 

Utvecklingsresurser:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge-tillägg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) för Visual Studio Code.
* [Python-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-python.python) för Visual Studio Code. 
* [Docker CE](https://docs.docker.com/engine/installation/). 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) för att installera Python-paket (ingår vanligtvis i Python-installationen).

## <a name="create-a-container-registry"></a>Skapa ett containerregister
I den här självstudien använder du Azure IoT Edge-tillägget för VS Code för att skapa en modul och skapa en **containeravbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.  

Du kan använda valfritt Docker-kompatibelt register för den här självstudien. Två populära Docker-registertjänster som är tillgängliga i molnet är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). I den här kursen använder vi Azure Container Registry. 

1. I [Azure Portal](https://portal.azure.com) väljer du **Skapa en resurs** > **Behållare** > **Azure Container Registry**.
2. Namnge ditt register, välj en prenumeration, välj en resursgrupp och ange SKU:n till **Basic**. 
3. Välj **Skapa**.
4. När du har skapat ditt containerregister går du till det och väljer **Åtkomstnycklar**. 
5. Växla till **Administratörsanvändare** för att **Aktivera**.
6. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du kommer att använda de här värdena senare i självstudien. 

## <a name="create-an-iot-edge-module-project"></a>Skapa ett projekt för IoT Edge-modulen
Följande steg skapar en IoT Edge Python-modul med Visual Studio Code och Azure IoT Edge-tillägget.

### <a name="create-a-new-solution"></a>Skapa en ny lösning

Använd Python-paketet **cookiecutter** för att skapa en Python-lösningsmall som du kan utgå ifrån. 

1. I Visual Studio Code väljer du **Visa** > **Integrerad terminal** för att öppna den integrerade Visual Studio Code-terminalen.

2. Installera (eller uppdatera) **cookiecutter** som du använde för att skapa IoT Edge-lösningsmallen i VS Code genom att köra följande kommando i den integrerade terminalen:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. Välj **Visa** > **Kommandopalett** för att öppna kommandopaletten i VS Code. 

4. Ange och kör kommandot **Azure: Logga in** på kommandopaletten och följ anvisningarna för att logga in med ditt Azure-konto. Om du redan är inloggad kan du hoppa över det här steget.

5. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) på kommandopaletten. Ange följande information i kommandopaletten för att skapa din lösning: 

   1. Välj den mapp där du vill skapa lösningen. 
   2. Ange ett namn för din lösning eller välj standardnamnet **EdgeSolution**.
   3. Välj **Python-modulen** som mall för modulen. 
   4. Ge modulen namnet **PythonModule**. 
   5. Ange det Azure-containerregister du skapade i föregående avsnitt som avbildningslagringsplats för den första modulen. Ersätt **localhost:5000** med det serverinloggningsvärde som du kopierade. Den slutliga strängen ser ut så här: \<registernamn\>.azurecr.io/pythonmodule.
 
VS Code-fönstret läser in IoT Edge-lösningens arbetsyta: modulmappen, en mallfil för distributionsmanifestet och en \.env-fil. 

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

## <a name="build-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod i **PythonModule** för att filtrera ut meddelanden om att temperaturen för den rapporterade datorn ligger under det godkända tröskelvärdet. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister. 

1. Logga in på Docker genom att ange följande kommando i den integrerade Visual Studio Code-terminalen: Sedan kan du skicka modulavbildningen till ditt Azure-containerregister: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Använd användarnamnet, lösenordet och inloggningsservern du kopierade från Azure-containerregistret i det första avsnittet. Du kan också hämta dessa värden från avsnittet **Åtkomstnycklar** i ditt register i Azure Portal.

2. I VS Code-utforskaren öppnar du filen deployment.template.json i arbetsytan för IoT Edge-lösningen. 

   Den här filen anger att **$edgeAgent** ska distribuera två moduler: **tempSensor** som simulerar enhetsdata och **PythonModule**. **PythonModule.image**-värdet är inställt på en Linux amd64-version av avbildningen. Läs mer om distributionsmanifest i avsnittet om att [förstå hur IoT Edge-moduler kan användas, konfigureras och återanvändas](module-composition.md).

   Den här filen innehåller även autentiseringsuppgifter för registret. Ditt användarnamn och lösenord fylls i med platshållare i mallfilen. När du genererar distributionsmanifestet uppdateras fälten med de värden du lade till i .env-filen. 

3. Lägg till **PythonModule**-modultvillingen i distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet **moduleContent** efter **$edgeHub**-modultvillingen: 
    ```json
        "PythonModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Spara filen.

5. Högerklicka på filen deployment.template.json och välj **Skapa IoT Edge-lösning** i VS Code-utforskaren. 

När du instruerar Visual Studio Code att skapa din lösning hämtar den först information från distributionsmallen och genererar en .json-distributionsfil i en ny mapp med namnet **config**. Sedan körs två kommandon i en integrerad terminal: `docker build` och `docker push`. Dessa två kommandon skapar koden, lägger Python-koden i en container och push-överför sedan koden till containerregistret du angav när du initierade lösningen. 

Den fullständiga adressen med tagg för containeravbildningen i `docker build`-kommandot som körs i den integrerade VS Code-terminalen. Avbildningsadressen skapas från information i filen module.json och har formatet \<lagringsplats\>:\<version\>-\<plattform\>. I den här självstudien ska adressen se ut så här: registryname.azurecr.io/pythonmodule:0.0.1-amd64.

## <a name="deploy-and-run-the-solution"></a>Distribuera och kör lösningen

Du kan använda Azure Portal till att distribuera din Python-modul till en IoT Edge-enhet, precis som du gjorde i snabbstarterna. Du kan också distribuera och övervaka moduler från Visual Studio Code. I följande avsnitt använder du Azure IoT Edge-filnamnstillägget för VS Code som listades under förutsättningarna. Installera tillägget nu om du inte redan har gjort det. 

1. Öppna kommandopaletten för VS Code genom att välja **Visa** > **Kommandopalett**.

2. Sök efter och kör kommandot **Azure: Logga in**. Följ anvisningarna för att logga in på ditt Azure-konto. 

3. Leta upp kommandot **Azure IoT Hub: Välj IoT** i kommandopaletten och kör det. 

4. Välj den prenumeration som innehåller din IoT-hubb och välj sedan den IoT-hubb du vill komma åt.

5. I VS Code-utforskaren expanderar du avsnittet **Azure IoT Hub-enheter**. 

6. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Skapa distribution för IoT Edge-enhet**. 

7. Bläddra till lösningsmappen som innehåller din **PythonModule**. Öppna mappen config, välj filen deployment.json och välj sedan på **Välj distributionsmanifest för Edge**.

8. Uppdatera avsnittet **Azure IoT Hub-enheter**. Du bör se den nya **PythonModule** köras tillsammans med **TempSensor**-modulen och **$edgeAgent** och **$edgeHub**. 

## <a name="view-generated-data"></a>Visa genererade data

1. Om du vill övervaka data som kommer till IoT-hubben väljer du ellipsen (**...** ) och sedan **Start Monitoring D2C Messages** (Starta övervakning av D2C-meddelanden).
2. Om du vill övervaka D2C-meddelandet för en specifik enhet högerklickar du på enheten i listan och väljer **Starta övervakning av D2C-meddelanden**.
3. Om du vill stoppa dataövervakningen kör du kommandot **Azure IoT Hub: Sluta övervaka D2C-meddelande** på kommandopaletten. 
4. Om du vill visa eller uppdatera modultvillingen högerklickar du på modulen i listan och väljer **Redigera modultvilling**. Om du vill uppdatera modultvillingen sparar du JSON-tvillingfilen, högerklickar i redigeringsområdet och väljer **Uppdatera modultvilling**.
5. Om du vill visa Docker-loggar installerar du [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) för VS Code. Du kan hitta de moduler som körs lokalt i Docker-utforskaren. I kontextmenyn klickar du på **Visa loggar** om du vill se dem i den integrerade terminalen. 

## <a name="clean-up-resources"></a>Rensa resurser 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader. 

> [!IMPORTANT]
> Det går inte att ångra borttagningen av Azure-resurser och resursgrupper. När dessa objekt tas bort tas resursgruppen och alla resurser som finns i den bort permanent. Var noga så att du inte tar bort fel resursgrupp eller resurser av misstag. Om du har skapat IoT-hubben i en befintlig resursgrupp som innehåller resurser som du vill behålla, tar du bara bort själva IoT-hubbresursen i stället för att ta bort resursgruppen.
>

Om du bara vill ta bort IoT-hubben kör du följande kommando med namnet på hubben och resursgruppens:

```azurecli-interactive
az iot hub delete --name {hub_name} --resource-group IoTEdgeResources
```


Ta bort hela resursgruppen med namnet:

1. Logga in på [Azure-portalen](https://portal.azure.com) och välj **Resursgrupper**.

2. I textrutan **Filtrera efter namn** anger du namnet på den resursgrupp som innehåller din IoT-hubb. 

3. Till höger om resursgruppen i resultatlistan väljer du ellipsen (**...**) och sedan **Ta bort resursgrupp**.

4. Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på resursgruppen igen för att bekräfta och välj sedan **Ta bort**. Efter en liten stund tas resursgruppen och resurser som finns i den bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul med kod för att filtrera rådata som genereras av din IoT Edge-enhet. Du kan fortsätta med någon av följande självstudier om du vill lära dig mer om hur Azure IoT Edge kan hjälpa dig att omvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Distribuera Azure-funktioner som en modul](tutorial-deploy-function.md)
> [Distribuera Azure Stream Analytics som en modul](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
