---
title: Skapa anpassad Python-modul - Azure IoT Edge | Microsoft-dokument
description: Den här självstudien visar hur du skapar en IoT Edge-modul med Python-kod och distribuerar den till en gränsenhet.
services: iot-edge
author: shizn
manager: philmea
ms.reviewer: kgremban
ms.author: xshi
ms.date: 10/14/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: a9ac9a3167d5335f6a6fd44049c892ba463dacad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372676"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-for-linux-devices"></a>Självstudiekurs: Utveckla och distribuera en Python IoT Edge-modul för Linux-enheter

Använd Visual Studio Code för att utveckla Python-kod och distribuera den till en Linux-enhet som kör Azure IoT Edge.

Du kan använda Azure IoT Edge-moduler till att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien hjälper dig att skapa och distribuera en IoT Edge-modul som filtrerar sensordata på IoT Edge-enheten som du konfigurerar i snabbstarten. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * använda Visual Studio Code till att skapa en IoT Edge Python-modul
> * använda Visual Studio Code och Docker till att skapa en Docker-avbildning och publicera den till registret
> * distribuera modulen till din IoT Edge-enhet
> * visa genererade data.

IoT Edge-modulen du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys vid kanten är användbar när du vill minska mängden data som skickas till och lagras i molnet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Lösningsscope

Den här självstudien visar hur du utvecklar en modul i **Python** med **Visual Studio-kod**och hur du distribuerar den till en **Linux-enhet**. IoT Edge stöder inte Python-moduler för Windows-enheter.

Använd följande tabell för att förstå dina alternativ för att utveckla och distribuera Python-moduler till Linux:

| Python | Visual Studio-koden | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Använd VS-kod för Python-moduler på Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Använd VS-kod för Python-moduler på Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Krav

Innan du påbörjar den här självstudien bör du ha gått igenom den tidigare självstudien för att ställa in din utvecklingsmiljö för Linux-containerutveckling: [Utveckla IoT Edge-moduler för Linux-enheter](tutorial-develop-for-linux.md). Genom att fylla i den självstudien bör du ha följande förutsättningar på plats:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En [Linux-enhet som kör Azure IoT Edge](quickstart-linux.md)
* Ett behållarregister, till exempel [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio-kod](https://code.visualstudio.com/) som konfigurerats med [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) konfigurerad för att köra Linux-behållare.

Om du vill utveckla en IoT Edge-modul i Python installerar du följande ytterligare förutsättningar på utvecklingsmaskinen:

* [Python-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-python.python) för Visual Studio Code.
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) för att installera Python-paket (ingår vanligtvis i Python-installationen).

>[!Note]
>Se till att din `bin`-mapp finns på sökvägen för din plattform. Vanligtvis `~/.local/` för UNIX och macOS, eller `%APPDATA%\Python` i Windows.

## <a name="create-a-module-project"></a>Skapa ett modulprojekt

Följande steg skapar en IoT Edge Python-modul med Visual Studio Code och Azure IoT-verktygen.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Använd VS-koden för att skapa en Python-lösningsmall som du kan bygga ovanpå.

1. I Visual Studio-kod väljer du **Visa** > **terminal** för att öppna den integrerade VS-koden-terminalen.

1. Välj **Visa** > **kommandopalett** för att öppna kommandopaletten VS-kod.

1. Ange och kör kommandot **Azure: Logga in** i kommandopaletten och följ anvisningarna för att logga in med ditt Azure-konto. Om du redan är inloggad kan du hoppa över det här steget.

1. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) på kommandopaletten. Följ anvisningarna och ange följande information för att skapa din lösning:

   | Field | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **Python-modul**. |
   | Ange ett modulnamn | Ge modulen namnet **PythonModule**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen fylls i baserat på det namn du angav i föregående steg. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. <br><br>Den slutliga avbildningslagringsplatsen ser ut så här: \<registernamn\>.azurecr.io/pythonmodule. |

   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-python-module/repository.png)

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerlagringsplatsen och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten.

1. Öppna **env-filen i** VS-kodutforskaren.
2. Uppdatera fälten med det **användarnamn** och **lösenord** som du kopierade från Azure Container-registret.
3. Spara .env-filen.

### <a name="select-your-target-architecture"></a>Välj målarkitektur

För närvarande kan Visual Studio Code utveckla Python-moduler för Linux AMD64- och Linux ARM32v7-enheter. Du måste välja vilken arkitektur du riktar dig till med varje lösning, eftersom behållaren är byggd och körs på olika sätt för varje arkitekturtyp. Standard är Linux AMD64.

1. Öppna kommandopaletten och sök efter **Azure IoT Edge: Ange standardmålplattform för edge-lösning**eller välj genvägsikonen i sidofältet längst ned i fönstret.

2. I kommandopaletten väljer du målarkitekturen i listan med alternativ. För den här guiden använder vi en Ubuntu virtuell maskin som IoT Edge-enhet, så kommer att behålla standard **amd64**.

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

Varje mall innehåller exempelkod, som tar simulerade sensordata från modulen **SimuleradTemperatureSensor** och dirigerar den till IoT-hubben. I det här avsnittet lägger du till kod som expanderar **PythonModule** för att analysera meddelanden innan de skickas.

1. Öppna **moduler** > **PythonModule** > **main.py**i VS-kodutforskaren .

2. Längst upp i filen **main.py** importerar du **json**-biblioteket:

    ```python
    import json
    ```

3. Lägg till variablerna **TEMPERATURE_THRESHOLD** och **TWIN_CALLBACKS** under de globala räknarna. Tröskelvärdet för temperatur är det värde som den uppmätta datortemperaturen måste överstiga för att data ska skickas till IoT-hubben.

    ```python
    # global counters
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    RECEIVED_MESSAGES = 0
    ```

4. Ersätt **input1_listener** funktion med följande kod:

    ```python
        # Define behavior for receiving an input message on input1
        # Because this is a filter module, we forward this message to the "output1" queue.
        async def input1_listener(module_client):
            global RECEIVED_MESSAGES
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    input_message = await module_client.receive_message_on_input("input1")  # blocking call
                    message = input_message.data
                    size = len(message)
                    message_text = message.decode('utf-8')
                    print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
                    custom_properties = input_message.custom_properties
                    print ( "    Properties: %s" % custom_properties )
                    RECEIVED_MESSAGES += 1
                    print ( "    Total messages received: %d" % RECEIVED_MESSAGES )
                    data = json.loads(message_text)
                    if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
                        custom_properties["MessageType"] = "Alert"
                        print ( "Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
                        await module_client.send_message_to_output(input_message, "output1")
                except Exception as ex:
                    print ( "Unexpected error in input1_listener: %s" % ex )

        # twin_patch_listener is invoked when the module twin's desired properties are updated.
        async def twin_patch_listener(module_client):
            global TWIN_CALLBACKS
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    data = await module_client.receive_twin_desired_properties_patch()  # blocking call
                    print( "The data in the desired properties patch was: %s" % data)
                    if "TemperatureThreshold" in data:
                        TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
                    TWIN_CALLBACKS += 1
                    print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
                except Exception as ex:
                    print ( "Unexpected error in twin_patch_listener: %s" % ex )
    ```

5. Uppdatera **lyssnarna** för att också lyssna på dubbla uppdateringar.

    ```python
        # Schedule task for C2D Listener
        listeners = asyncio.gather(input1_listener(module_client), twin_patch_listener(module_client))

        print ( "The sample is now waiting for messages. ")
    ```

6. Spara filen main.py.

7. Öppna **filen deployment.template.json** i IoT Edge-lösningsarbetsytan i VS-kodutforskaren.

8. Lägg till **PythonModule**-modultvillingen i distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet **moduleContent** efter **$edgeHub**-modultvillingen:

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Lägga till modultvilling till distributionsmall](./media/tutorial-python-module/module-twin.png)

9. Spara filen deployment.template.json.

## <a name="build-and-push-your-module"></a>Skapa och tryck på din modul

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod i PythonModule som filtrerar bort meddelanden där den rapporterade maskintemperaturen ligger inom de acceptabla gränserna. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

1. Öppna den integrerade VS-koden-terminalen genom att välja **Visa** > **terminal**.

1. Logga in på Docker genom att ange följande kommando i terminalen. Logga in med användarnamn, lösenord och inloggningsserver från ditt Azure-behållarregister. Du kan hämta dessa värden från avsnittet **Access-nycklar** i registret i Azure-portalen.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du kan få en säkerhetsvarning `--password-stdin`som rekommenderar användning av . Även om bästa praxis rekommenderas för produktionsscenarier, ligger den utanför den här självstudiens omfattning. Mer information finns i [inloggningsreferensen för docker.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

1. Högerklicka på filen **deployment.template.json** i VS-kodutforskaren och välj **Bygg och Push IoT Edge-lösning**.

   Kommandot Bygg och push startar tre åtgärder. Först skapas en ny mapp i lösningen som kallas **config** som innehåller hela distributionsmanifestet, inbyggt av information i distributionsmallen och andra lösningsfiler. För det `docker build` andra körs den för att skapa behållaravbildningen baserat på lämplig dockerfile för din målarkitektur. Sedan körs `docker push` det för att skicka avbildningsdatabasen till behållarregistret.

## <a name="deploy-modules-to-device"></a>Distribuera moduler till enheten

Använd Utforskaren för Visual Studio-kod och Azure IoT Tools-tillägget för att distribuera modulprojektet till din IoT Edge-enhet. Du har redan ett distributionsmanifest förberett för ditt scenario, **filen deployment.json** i konfigurationsmappen. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

Kontrollera att din IoT Edge-enhet är igång.

1. Expandera avsnittet **Azure IoT Hub Devices** i Utforskaren för Visual Studio-kod för att se listan över IoT-enheter.

2. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Create Deployment for Single Device** (Skapa distribution för en enskild enhet).

3. Välj filen **deployment.json** i mappen **config** och klicka sedan på **Select Edge Deployment Manifest** (Välj distributionsmanifest för Edge). Använd inte filen deployment.template.json.

4. Klicka på uppdateringsknappen. Du bör se den nya **PythonModule** körs tillsammans med **Modulen SimuleradTemperatureSensor** och **$edgeAgent** och **$edgeHub**.

## <a name="view-the-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas.

Du kan visa statusen för din IoT Edge-enhet i avsnittet om **Azure IoT Hub-enheter** i Visual Studio Code-utforskaren. Expandera enhetsinformationen så ser du en lista med moduler som distribueras och körs.

1. Högerklicka på namnet på IoT Edge-enheten i Utforskaren för Visual Studio-kod och välj **Startövervakning inbyggd händelseslutpunkt**.

2. Visa meddelanden som kommer till din IoT Hub. Det kan ta ett tag innan meddelandena kommer fram. IoT Edge-enheten måste ta emot sin nya distribution och starta alla moduler. Sedan väntar de ändringar vi gjorde i PythonModule-koden tills maskintemperaturen når 25 grader innan du skickar meddelanden. Meddelandet läggs också till **i** alla meddelanden som når det temperaturtröskeln.

## <a name="edit-the-module-twin"></a>Redigera modultvillingen

Vi använde pythonmodulmodultvillingen i distributionsmanifestet för att ställa in temperaturtröskeln till 25 grader. Du kan använda modultvillingen för att ändra funktionaliteten utan att behöva uppdatera modulkoden.

1. Expandera informationen under IoT Edge-enheten i Visual Studio-kod för att se de moduler som körs.

2. Högerklicka på **PythonModule** och välj **Redigera modultvilling**.

3. Hitta **TemperatureThreshold** i önskade egenskaper. Ändra dess värde till en ny temperatur 5 grader till 10 grader högre än den senaste rapporterade temperaturen.

4. Spara modultvillingfilen.

5. Högerklicka någonstans i redigeringsfönstret för modultvilling och välj **Uppdatera modultvilling**.

6. Övervaka inkommande meddelanden från enheten till molnet. Du bör se meddelandena sluta tills den nya temperaturtröskeln har uppnåtts.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och De Azure-resurser som du använde i den här artikeln för att undvika avgifter.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att bygga egna moduler kan du läsa mer om hur du [utvecklar egna IoT Edge-moduler](module-development.md) eller hur du [utvecklar moduler med Visual Studio Code](how-to-vs-code-develop-module.md). Exempel på IoT Edge-moduler, inklusive den simulerade temperaturmodulen, finns i [IoT Edge-modulprover](https://github.com/Azure/iotedge/tree/master/edge-modules) och [IoT Python SDK-exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios).

Du kan fortsätta med nästa självstudier för att lära dig hur Azure IoT Edge kan hjälpa dig att distribuera Azure-molntjänster för att bearbeta och analysera data på gränsen.

> [!div class="nextstepaction"]
> [Funktioner](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
