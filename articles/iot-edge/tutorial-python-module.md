---
title: Skapa anpassad Python-modul – Azure IoT Edge | Microsoft Docs
description: Den här självstudien visar hur du skapar en IoT Edge-modul med Python-kod och distribuerar den till en gränsenhet.
services: iot-edge
author: shizn
manager: philmea
ms.reviewer: kgremban
ms.author: xshi
ms.date: 03/24/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 82da44409c4500ff097805efec33cec8cf6bbedd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64575650"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-for-linux-devices"></a>Självstudier: Utveckla och distribuera en Python IoT Edge-modul för Linux-enheter

Använd Visual Studio Code för att utveckla C-koden och distribuera den till en Linux-enhet som kör Azure IoT Edge. 

Du kan använda Azure IoT Edge-moduler för att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här vägledningen visar dig hur du skapar och distribuerar en IoT Edge-modul som filtrerar sensordata på IoT Edge-enhet som du skapade i snabbstarten. I den här guiden får du lära dig att:    

> [!div class="checklist"]
> * använda Visual Studio Code till att skapa en IoT Edge Python-modul
> * använda Visual Studio Code och Docker till att skapa en Docker-avbildning och publicera den till registret
> * distribuera modulen till din IoT Edge-enhet
> * visa genererade data.


IoT Edge-modulen du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys vid kanten är användbar när du vill minska mängden data som skickas till och lagras i molnet. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Lösningen omfattning

Den här kursen visar hur du utvecklar en modul i **Python** med **Visual Studio Code**, och hur du distribuerar det till en **Linux enheten**. IoT Edge stöder inte Python-moduler för Windows-enheter. 

Använd följande tabell för att förstå dina alternativ för att utveckla och distribuera Python-moduler till Linux: 

| Python | Visual Studio-koden | Visual Studio 2017 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Använda VS-kod för Python-moduler på Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Använda VS-kod för Python-moduler på Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du påbörjar den här självstudiekursen ska du har gått igenom den tidigare självstudiekursen för att ställa in din utvecklingsmiljö för utveckling av Linux-behållare: [Utveckla IoT Edge-moduler för Linux-enheter](tutorial-develop-for-linux.md). Genom att slutföra någon av dessa självstudier ska du har följande krav på plats: 

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En [Linux-enhet som kör Azure IoT Edge](quickstart-linux.md)
* Ett behållarregister, till exempel [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) konfigurerats med den [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) konfigurerats för att köra Linux-behållare.

För att utveckla en IoT Edge-modul i Python kan du installera följande ytterligare krav på utvecklingsdatorn: 

* [Python-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-python.python) för Visual Studio Code. 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) för att installera Python-paket (ingår vanligtvis i Python-installationen).

>[!Note]
>Se till att din `bin`-mapp finns på sökvägen för din plattform. Vanligtvis `~/.local/` för UNIX och macOS, eller `%APPDATA%\Python` i Windows.

## <a name="create-a-module-project"></a>Skapa en modul-projekt
Följande steg skapar en IoT Edge Python-modul med Visual Studio Code och Azure IoT-verktygen.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Använd Python-paketet **cookiecutter** för att skapa en Python-lösningsmall som du kan utgå ifrån. 

1. I Visual Studio Code väljer du **Visa** > **Terminal** för att öppna den integrerade Visual Studio Code-terminalen.

2. I terminalen anger du följande kommando för att installera (eller uppdatera) **cookiecutteru**, som du använder för att skapa IoT Edge-lösningsmallen:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >Se till att katalogen där cookiecutter installeras är i PATH (sökvägen) för din miljö så att det blir möjligt att anropa det från en kommandotolk. Katalogen är en del av utdata för installationsskriptet, till exempel `C:\Users\{user}\AppData\Roaming\Python\Python{version}\Scripts`.
   >
   >Starta om Visual Studio Code för att hämta ändringarna till PATH (sökvägen). 

3. Välj **Visa** > **Kommandopalett** för att öppna kommandopaletten i VS Code. 

4. I kommandopaletten skriver och kör du kommandot **Azure: Logga in** och följer anvisningarna för att logga in på ditt Azure-konto. Om du redan är inloggad kan du hoppa över det här steget.

5. Skriv och kör kommandot **Azure IoT Edge på kommandopaletten: New IoT Edge solution** (Ny IoT Edge-lösning). Följ anvisningarna och ange följande information för att skapa din lösning:

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **Python-modul**. |
   | Ange ett modulnamn | Ge modulen namnet **PythonModule**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen fylls i baserat på det namn du angav i föregående steg. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. <br><br>Den slutliga avbildningslagringsplatsen ser ut så här: \<registernamn\>.azurecr.io/pythonmodule. |
 
   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-python-module/repository.png)


### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerlagringsplatsen och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten. 

1. Öppna **.env**-filen i VS Code-utforskaren. 
2. Uppdatera fälten med det **användarnamn** och **lösenord** som du kopierade från Azure Container-registret. 
3. Spara .env-filen. 

### <a name="select-your-target-architecture"></a>Välj din mål-arkitektur

Visual Studio Code kan för närvarande kan utveckla C-moduler för Linux AMD64- och Linux ARM32v7-enheter. Du måste välja vilken arkitektur som mål med varje lösning eftersom behållaren har skapats och körs på olika sätt för varje arkitekturtyp av. Standardvärdet är Linux AMD64. 

1. Öppna kommandopaletten och Sök efter **Azure IoT Edge: Ange standard målplattform för lösning**, eller klicka på genvägsikonen i Sidopanel längst ned i fönstret. 

2. Välj mål-arkitektur i kommandopaletten, från listan med alternativ. Den här självstudien använder vi en Ubuntu-dator som IoT Edge-enhet, så kommer behåller du standardvärdet **amd64**. 

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

7. Spara filen main.py.

8. I VS Code-utforskaren öppnar du filen **deployment.template.json** i arbetsytan för IoT Edge-lösningen. 

9. Lägg till **PythonModule**-modultvillingen i distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet **moduleContent** efter **$edgeHub**-modultvillingen: 

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Lägga till modultvilling till distributionsmall](./media/tutorial-python-module/module-twin.png)

10. Spara filen deployment.template.json.

## <a name="build-and-push-your-module"></a>Skapa och skicka din modul

I det föregående avsnittet du skapade en IoT Edge-lösning och lagt till kod till PythonModule som filtrerar ut meddelanden där temperaturen som rapporteras datorn ligger inom godkända gränser. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

1. Öppna den VS Code-integrerade terminalen genom att välja **Visa** > **Terminal**.

1. Logga in på Docker genom att ange följande kommando i terminalen. Logga in med användarnamn, lösenord och inloggningsserver från Azure container registry. Du kan hämta dessa värden från den **åtkomstnycklar** avsnitt på ditt register i Azure-portalen.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du får en säkerhetsvarning som rekommenderar användning av `--password-stdin`. Även om den bästa metoden rekommenderas för produktionsscenarier, ligger utanför omfånget för den här självstudien. Mer information finns i den [docker-inloggning](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referens.

2. I VS Code-utforskaren högerklickar du på filen **deployment.template.json** och väljer **Build and Push IoT Edge solution** (Skapa och skicka IoT Edge-lösning).

   Versions- och push-kommando startar tre åtgärder. Först skapar den en ny mapp i lösningen kallas **config** som innehåller fullständig distribution manifest, byggas ut information i distributionsmallen och andra lösningsfiler. Dessutom körs `docker build` att skapa behållaravbildningen baserat på dockerfile som är lämpliga för din mål-arkitektur. Sedan körs `docker push` att skicka lagringsplatsen för avbildningen till behållarregistret.


## <a name="deploy-modules-to-device"></a>Distribuera moduler till enhet

Använd Visual Studio Code-Utforskaren och verktyg för Azure IoT-tillägget för att distribuera projektet modulen till din IoT Edge-enhet. Du redan har ett manifest för distribution som förberedd för ditt scenario, den **deployment.json** fil i config-mappen. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

Se till att din IoT Edge-enhet är igång.

1. I Visual Studio Code-Utforskaren expanderar den **Azure IoT Hub-enheter** avsnitt för att se din lista över IoT-enheter.

2. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Create Deployment for Single Device** (Skapa distribution för en enskild enhet).

3. Välj filen **deployment.json** i mappen **config** och klicka sedan på **Select Edge Deployment Manifest** (Välj distributionsmanifest för Edge). Använd inte filen deployment.template.json.

4. Klicka på uppdateringsknappen. Du bör se den nya **PythonModule** köras tillsammans med **TempSensor**-modulen och **$edgeAgent** och **$edgeHub**. 

## <a name="view-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas.

Du kan visa statusen för din IoT Edge-enhet i avsnittet om **Azure IoT Hub-enheter** i Visual Studio Code-utforskaren. Expandera enhetsinformationen så ser du en lista med moduler som distribueras och körs.

1. I Visual Studio Code-Utforskaren högerklickar du på namnet på din IoT Edge-enhet och välj **börja övervaka D2C meddelanden**.

2. Visa meddelanden som inkommer på din IoT-hubb. Det kan ta en stund innan meddelanden tas emot, eftersom IoT Edge-enheten måste ta emot dess ny distribution och starta alla moduler. Vi ändringar i koden PythonModule vänta sedan tills datorn temperaturen når 25 grader innan du skickar meddelanden. Det ger också meddelandetypen **avisering** att alla meddelanden som når gränsen temperatur. 

## <a name="edit-the-module-twin"></a>Redigera modultvillingen

Vi använde modultvilling PythonModule i manifestet distribution Ange tröskelvärdet för temperatur vid 25 grader. Du kan använda modultvillingen för att ändra funktionen utan att behöva uppdatera modulen koden.

1. Expandera information under din IoT Edge-enhet för att se modulerna som körs i Visual Studio Code. 

2. Högerklicka på **PythonModule** och välj **redigera modultvilling**. 

3. Hitta **TemperatureThreshold** i önskade egenskaper. Ändra värdet till en ny temperatur 5 grader och 10 grader som är högre än den senaste rapporterade temperaturen. 

4. Spara filen för modulen twin.

5. Högerklicka någonstans i modultvillingen redigera rutan och välj **uppdatering modultvilling**. 

5. Övervaka inkommande meddelanden för enhet-till-moln. Du bör se meddelanden stoppa tills det nya temperatur-tröskelvärdet har uppnåtts. 

## <a name="clean-up-resources"></a>Rensa resurser 

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet. 

I annat fall kan du ta bort de lokala konfigurationerna och Azure-resurser som du använt i den här artikeln för att undvika avgifter. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att skapa egna moduler kan du läsa mer om [utveckla dina egna IoT Edge-moduler](module-development.md) eller hur du [utveckla moduler med Visual Studio Code](how-to-vs-code-develop-module.md). Du kan fortsätta till nästa självstudierna för att lära dig hur Azure IoT Edge kan hjälpa dig att distribuera Azure-molntjänster för att bearbeta och analysera data på gränsen.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Maskininlärning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)