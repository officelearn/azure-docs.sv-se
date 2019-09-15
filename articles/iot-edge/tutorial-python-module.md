---
title: Skapa anpassad python-modul – Azure IoT Edge | Microsoft Docs
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
ms.openlocfilehash: 2784d57f3f85094230b481dd9fedca191edb39d4
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001111"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-for-linux-devices"></a>Självstudier: Utveckla och distribuera en python IoT Edge-modul för Linux-enheter

Använd Visual Studio Code för att utveckla C-kod och distribuera den till en Linux-enhet som kör Azure IoT Edge. 

Du kan använda Azure IoT Edge-moduler för att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensor data på den IoT Edge enhet som du har konfigurerat i snabb starten. I den här guiden får du lära dig att:    

> [!div class="checklist"]
> * använda Visual Studio Code till att skapa en IoT Edge Python-modul
> * använda Visual Studio Code och Docker till att skapa en Docker-avbildning och publicera den till registret
> * distribuera modulen till din IoT Edge-enhet
> * visa genererade data.


IoT Edge-modulen du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys vid kanten är användbar när du vill minska mängden data som skickas till och lagras i molnet. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Följande guide gäller för v1 python SDK, som sedan har föråldrats. Vi arbetar för närvarande för att göra den här guiden v2 kompatibel. Se det här utrymmet för uppdateringar.

## <a name="solution-scope"></a>Lösnings omfång

Den här självstudien visar hur du utvecklar en modul i **python** med **Visual Studio Code**och hur du distribuerar den till en **Linux-enhet**. IoT Edge stöder inte python-moduler för Windows-enheter. 

Använd följande tabell för att förstå alternativen för att utveckla och distribuera python-moduler till Linux: 

| Python | Visual Studio-koden | Visual Studio 2017/2019 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Använda VS Code för python-moduler på Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Använda VS Code för python-moduler på Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudien bör du ha gått igenom den föregående kursen för att konfigurera utvecklings miljön för utveckling av Linux-behållare: [Utveckla IoT Edge moduler för Linux-enheter](tutorial-develop-for-linux.md). Genom att slutföra någon av de här självstudierna bör du ha följande krav på plats: 

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En [Linux-enhet som kör Azure IoT Edge](quickstart-linux.md)
* Ett behållar register som [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio-kod](https://code.visualstudio.com/) som kon figurer ATS med [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) konfigurerat för att köra Linux-behållare.

Om du vill utveckla en IoT Edge-modul i python installerar du följande ytterligare krav på utvecklings datorn: 

* [Python-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-python.python) för Visual Studio Code. 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) för att installera Python-paket (ingår vanligtvis i Python-installationen).

>[!Note]
>Se till att din `bin`-mapp finns på sökvägen för din plattform. Vanligtvis `~/.local/` för UNIX och macOS, eller `%APPDATA%\Python` i Windows.

## <a name="create-a-module-project"></a>Skapa ett modul-projekt
Följande steg skapar en IoT Edge Python-modul med Visual Studio Code och Azure IoT-verktygen.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Använd VS-koden för att skapa en python-lösning som du kan bygga ovanpå. 

1. I Visual Studio Code väljer du **Visa** > **Terminal** för att öppna den integrerade Visual Studio Code-terminalen.

1. Välj **Visa** > **Kommandopalett** för att öppna kommandopaletten i VS Code. 

1. I kommandopaletten skriver och kör du kommandot **Azure: Logga in** och följer anvisningarna för att logga in på ditt Azure-konto. Om du redan är inloggad kan du hoppa över det här steget.

1. Skriv och kör kommandot **Azure IoT Edge på kommandopaletten: Ny IoT Edge-lösning**. Följ anvisningarna och ange följande information för att skapa din lösning:

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

### <a name="select-your-target-architecture"></a>Välj din mål arkitektur

För närvarande kan Visual Studio Code utveckla C-moduler för Linux AMD64-och Linux ARM32v7-enheter. Du måste välja vilken arkitektur du vill använda för varje lösning, eftersom behållaren har skapats och körs på olika sätt för varje arkitektur typ. Standardvärdet är Linux AMD64. 

1. Öppna kommando paletten och Sök efter **Azure IoT Edge: Ange standard plattform för Edge-lösning**eller Välj gen vägs ikonen i sido fältet längst ned i fönstret. 

2. I paletten kommando väljer du mål arkitekturen i listan med alternativ. I den här självstudien använder vi en virtuell Ubuntu-dator som IoT Edge enhet, så behåller standard- **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

Varje mall innehåller exempel kod, som tar simulerade sensor data från **SimulatedTemperatureSensor** -modulen och dirigerar den till IoT Hub. I det här avsnittet lägger du till kod som expanderar **PythonModule** för att analysera meddelanden innan de skickas. 

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

## <a name="build-and-push-your-module"></a>Bygga och pusha din modul

I föregående avsnitt skapade du en IoT Edge-lösning och lagt till kod i PythonModule som filtrerar bort meddelanden där den rapporterade datorns temperatur ligger inom de acceptabla gränserna. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

1. Öppna den VS Code-integrerade terminalen genom att välja **Visa** > **Terminal**.

1. Logga in på Docker genom att ange följande kommando i terminalen. Logga in med användar namnet, lösen ordet och inloggnings servern från Azure Container Registry. Du kan hämta dessa värden från avsnittet **åtkomst nycklar** i registret i Azure Portal.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du kan få en säkerhets varning som rekommenderar att du använder `--password-stdin`. Det bästa tillvägagångs sättet rekommenderas för produktions scenarier, men det ligger utanför omfånget för den här självstudien. Mer information finns i inloggnings [](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referens för Docker.

2. I VS Code-utforskaren högerklickar du på filen **deployment.template.json** och väljer **Build and Push IoT Edge solution** (Skapa och skicka IoT Edge-lösning).

   Kommandot build och push startar tre åtgärder. Först skapar den en ny mapp i lösningen som heter **config** och som innehåller det fullständiga distributions manifestet, och bygger ut information i distributions mal len och andra filer i lösningen. Sedan körs `docker build` den för att bygga behållar avbildningen baserat på lämpliga Dockerfile för din mål arkitektur. Sedan körs `docker push` den för att skicka avbildnings lagrings platsen till behållar registret.


## <a name="deploy-modules-to-device"></a>Distribuera moduler till enhet

Använd Visual Studio Code Explorer och tillägget Azure IoT Tools för att distribuera modulfönstret till din IoT Edge-enhet. Du har redan ett distributions manifest som är för berett för ditt scenario, filen **Deployment. JSON** i mappen config. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

Kontrol lera att din IoT Edges enhet är igång.

1. I Visual Studio Code Explorer expanderar du avsnittet **Azure IoT Hub-enheter** för att se din lista över IoT-enheter.

2. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Create Deployment for Single Device** (Skapa distribution för en enskild enhet).

3. Välj filen **deployment.json** i mappen **config** och klicka sedan på **Select Edge Deployment Manifest** (Välj distributionsmanifest för Edge). Använd inte filen deployment.template.json.

4. Klicka på uppdateringsknappen. Du bör se den nya **PythonModule** som körs tillsammans med **SimulatedTemperatureSensor** -modulen och **$edgeAgent** och **$edgeHub**. 

## <a name="view-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas.

Du kan visa statusen för din IoT Edge-enhet i avsnittet om **Azure IoT Hub-enheter** i Visual Studio Code-utforskaren. Expandera enhetsinformationen så ser du en lista med moduler som distribueras och körs.

1. I Visual Studio Code Explorer högerklickar du på namnet på din IoT Edge enhet och väljer **starta övervakning inbyggd händelse slut punkt**.

2. Visa meddelanden som kommer till IoT Hub. Det kan ta en stund innan meddelandena anländer, eftersom den IoT Edge enheten måste ta emot sin nya distribution och starta alla moduler. Ändringarna vi gjorde i PythonModule-koden väntar tills datorns temperatur når 25 grader innan meddelanden skickas. Den lägger också till meddelande typ **avisering** till alla meddelanden som når detta temperatur tröskelvärde. 

## <a name="edit-the-module-twin"></a>Redigera modulens dubbla

Vi använde PythonModule-modulen dubbla i distributions manifestet för att ange temperatur tröskel vid 25 grader. Du kan använda modulen för att ändra funktionen utan att behöva uppdatera modulens kod.

1. I Visual Studio Code, expanderar du informationen under IoT Edge enheten för att se de moduler som körs. 

2. Högerklicka på **PythonModule** och välj **Redigera modul dubbla**. 

3. Hitta **TemperatureThreshold** i önskade egenskaper. Ändra värdet till en ny temperatur 5 grader till 10 grader högre än den senaste rapporterade temperaturen. 

4. Spara modulens dubbla fil.

5. Högerklicka någonstans i modulens dubbla redigerings fönster och välj **Uppdatera modul dubbla**. 

6. Övervaka inkommande meddelanden från enhet till molnet. Du bör se att meddelandena stannar tills det nya temperatur tröskelvärdet har uppnåtts. 

## <a name="clean-up-resources"></a>Rensa resurser 

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet. 

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du använde i den här artikeln för att undvika avgifter. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att skapa dina egna moduler kan du lära dig mer om hur du [utvecklar dina egna IoT Edge moduler](module-development.md) eller hur du [utvecklar moduler med Visual Studio Code](how-to-vs-code-develop-module.md). Du kan fortsätta till nästa självstudier för att lära dig hur Azure IoT Edge kan hjälpa dig att distribuera Azure Cloud Services för att bearbeta och analysera data i gränsen.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)