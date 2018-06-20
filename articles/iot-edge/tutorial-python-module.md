---
title: Azure IoT Edge Python-modul | Microsoft Docs
description: Skapa en IoT Edge-modul med Python-kod och distribuera den till en gränsenhet
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 88d772306cb9e67216b380aa885284ebedc77b5f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632116"
---
# <a name="develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device---preview"></a>Utveckla och distribuera en Python IoT Edge-modul till din simulerade enhet – förhandsversion

Du kan använda IoT Edge-moduler till att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. Du kommer att använda den simulerade IoT Edge-enheten som du skapade i självstudien Distribuera Azure IoT Edge på en simulerad enhet i [Windows][lnk-tutorial1-win] eller [Linux][lnk-tutorial1-lin]. I den här guiden får du lära dig hur man:    

> [!div class="checklist"]
> * Använda Visual Studio Code till att skapa en IoT Edge Python-modul
> * Använda Visual Studio Code och Docker för att skapa en Docker-avbildning och publicera den till ditt register 
> * Distribuera modulen till din IoT Edge-enhet
> * Visa genererade data


IoT Edge-modulen som du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys är användbar för att minska mängden data som kommuniceras till och lagras i molnet. 

> [!IMPORTANT]
> Python-modulen kan för närvarande bara köras i amd64 Linux-behållare. Det går inte att köra den i Windows-behållare eller ARM-baserade behållare. 

## <a name="prerequisites"></a>Nödvändiga komponenter

* Azure IoT Edge-enheten som du skapade i snabbstarten eller den första självstudien.
* Primärnyckelns anslutningssträng för IoT Edge-enheten.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Python-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Docker](https://docs.docker.com/engine/installation/) på samma dator som har Visual Studio Code. Community Edition (CE) räcker för den här självstudien. 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) för att installera Python-paket (ingår vanligtvis i Python-installationen).

## <a name="create-a-container-registry"></a>Skapa ett behållarregister
I den här självstudien använder du Azure IoT Edge-tillägget för VS Code för att skapa en modul och skapa en **behållaravbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.  

Du kan använda valfritt Docker-kompatibelt register för den här självstudien. Två populära Docker-registertjänster som finns tillgängliga i molnet är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). I den här kursen använder vi Azure Container Registry. 

1. I [Azure Portal](https://portal.azure.com) väljer du **Skapa en resurs** > **Behållare** > **Azure Container Registry**.
2. Namnge ditt register, välj en prenumeration, välj en resursgrupp och ange SKU:n till **Basic**. 
3. Välj **Skapa**.
4. När du har skapat ditt behållarregister, navigerar du till det och väljer **Åtkomstnycklar**. 
5. Växla till **Administratörsanvändare** för att **Aktivera**.
6. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du kommer att använda de här värdena senare i självstudien. 

## <a name="create-an-iot-edge-module-project"></a>Skapa ett projekt för IoT Edge-modulen
Följande steg visar hur du skapar en IoT Edge Python-modul med Visual Studio Code och Azure IoT Edge-tillägget.
1. I Visual Studio Code väljer du **Visa** > **Integrerad terminal** för att öppna den integrerade Visual Studio Code-terminalen.
2. I den integrerade terminalen anger du följande kommando för att installera (eller uppdatera) **cookiecutter** (Vi rekommenderar att detta antingen görs i en virtuell miljö eller som en användarinstallation enligt nedan):

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. Skapa ett projekt för den nya modulen. Följande kommando skapar projektmappen **FilterModule** på behållarens lagringsplats. Parametern `image_repository` bör ha formatet `<your container registry name>.azurecr.io/filtermodule` om du använder Azure-behållarregistret. Ange följande kommando i den aktuella arbetsmappen:

    ```cmd/sh
    cookiecutter --no-input https://github.com/Azure/cookiecutter-azure-iot-edge-module module_name=FilterModule image_repository=<your container registry address>/filtermodule
    ```
 
4. Välj **Arkiv** > **Öppna mapp**.
5. Bläddra till mappen **FilterModule** och klicka på **Välj mapp** för att öppna projektet i VS Code.
6. Klicka på **main.py** i VS Code-utforskaren för att öppna den.
7. Längst upp i namnområdet **FilterModule** importerar du biblioteket `json`:

    ```python
    import json
    ```

8. Lägg till `TEMPERATURE_THRESHOLD`, `RECEIVE_CALLBACKS` och `TWIN_CALLBACKS` under de globala räknarna. Tröskelvärdet för temperatur är det värde som den uppmätta temperaturen måste överstiga för att data ska skickas till IoT Hub.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = RECEIVE_CALLBACKS = 0
    ```

9. Uppdatera funktionen `receive_message_callback` med nedanstående innehåll.

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we will forward this message onto the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print("    Data: <<<{}>>> & Size={:d}".format(message_text, size))
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print("    Properties: {}".format(key_value_pair))
        RECEIVE_CALLBACKS += 1
        print("    Total calls received: {:d}".format(RECEIVE_CALLBACKS))
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature {} exceeds threshold {}".format(data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

10. Lägg till en ny funktion `device_twin_callback`. Funktionen kommer att anropas när önskade egenskaper uppdateras.

    ```python
    # device_twin_callback is invoked when twin's desired properties are updated.
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print("\nTwin callback called with:\nupdateStatus = {}\npayload = {}\ncontext = {}".format(update_state, payload, user_context))
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print("Total calls confirmed: {:d}\n".format(TWIN_CALLBACKS))
    ```

11. I klassen `HubManager` lägger du till en ny rad i `__init__`-metoden för att initiera funktionen `device_twin_callback` som du nyss lade till.

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_device_twin_callback(device_twin_callback, self)
    ```


12. Spara filen.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Skapa en Docker-avbildning och publicera den till ditt register

1. Logga in på Docker genom att ange följande kommando i den integrerade VS Code-terminalen: 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   Använd användarnamn, lösenord och inloggningsserver som du kopierade från Azure-behållarregistret när du skapade den.

2. I VS Code-utforskaren högerklickar du på filen **module.json** och klickar sedan på **Docker-avbildning för Build and Push IoT-modul**. I popup-listrutan överst i VS Code-fönstret väljer du din behållarplattform, exempelvis **amd64** för Linux-behållare. VS Code lägger `main.py` och önskade beroenden i en behållare och push-överför den till det behållarregister som du har angett. Det kan ta flera minuter att skapa avbildningen första gången.

3. Den fullständiga adressen med tagg för behållaravbildningen finns i den integrerade VS Code-terminalen. Mer information om versions- och push-definitionen finns i filen `module.json`.

## <a name="add-registry-credentials-to-edge-runtime"></a>Lägga till autentiseringsuppgifter för registret i Edge-körningen
Lägg till autentiseringsuppgifterna för ditt register i Edge-runtimen på den dator där du kör din Edge-enhet. Med dessa autentiseringsuppgifter får körningen åtkomst till att hämta behållaren. 

- I Windows kör du följande kommando:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- I Linux kör du följande kommando:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Kör lösningen

1. I [Azure Portal](https://portal.azure.com), navigerar du till din IoT-hubb.
2. Gå till **IoT Edge (förhandsversion)** och välj IoT Edge-enhet.
3. Välj **Ange moduler**. 
2. Kontrollera att modulen **tempSensor** fylls i automatiskt. Om inte använder du följande steg för att lägga till den:
    1. Välj **Lägg till IoT Edge-modul**.
    2. I **Namn**-fältet skriver du `tempSensor`.
    3. I **URI för avbildning**-fältet skriver du `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Lämna de andra inställningarna oförändrade och klicka på **Spara**.
9. Lägg till modulen **filterModule** som du skapade i föregående avsnitt. 
    1. Välj **Lägg till IoT Edge-modul**.
    2. I **Namn**-fältet skriver du `filterModule`.
    3. I **URI för avbildning**-fältet, anger du adressen till din avbildning, till exempel `<your container registry address>/filtermodule:0.0.1-amd64`. Avbildningens fullständiga adress kan hittas i föregående avsnitt.
    4. Markera kryssrutan **Aktivera** så att du kan redigera modultvillingen. 
    5. Ersätt JSON i textrutan för modultvillingen med följande JSON: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Klicka på **Spara**.
10. Klicka på **Nästa**.
11. I steget **Ange vägar** kopierar du den JSON nedanför till textrutan. Moduler publicerar alla meddelanden i Edge-körningen. Deklarativa regler i körningen definierar var meddelandena flödar. I den här självstudien behöver du två vägar. Den första vägen transporterar meddelanden från temperatursensorn till filtermodulen via slutpunkten ”input1”, vilket är den slutpunkt som du konfigurerade med hanteraren **FilterMessages**. Den andra vägen transporterar meddelanden från filtermodulen till IoT Hub. I den här vägen är `upstream` ett särskilt mål som talar om för Edge-hubben att skicka meddelanden till IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Klicka på **Nästa**.
5. I steget **Granska mallen**, klickar du på **Skicka**. 
6. Återgå till IoT Edge-enhetens informationssida och välj **Uppdatera**. Du bör nu se att den nya **filtermodule** körs tillsammans med **tempSensor**-modulen och **IoT Edge-körning**. 

## <a name="view-generated-data"></a>Visa genererade data

Övervaka meddelanden från enhet till molnet som skickas från din IoT Edge-enhet till din IoT-hubb:
1. Konfigurera Azure IoT Toolkit-tillägget med anslutningssträngen för din IoT-hubb: 
    1. Öppna VS Code-utforskaren genom att välja **Visa** > **Utforskaren**. 
    3. I utforskaren klickar du på **IOT HUB-ENHETER** och sedan på **...**. Klicka på **Ange anslutningssträngen för IoT Hub** och ange anslutningssträngen för den IoT-hubb som din IoT Edge-enhet ansluter till i popup-fönstret. 

        För att hitta anslutningssträngen klickar du på panelen för din IoT-hubb i Azure Portal och sedan på **Principer för delad åtkomst**. I **Principer för delad åtkomst** klickar du på principen **iothubowner** och kopierar IoT Hub-anslutningssträngen i fönstret **iothubowner**.   

1. Om du vill övervaka data som kommer till IoT-hubben väljer du **Visa** > **Kommandopalett** och söker efter menykommandot **IoT: Börja övervaka D2C-meddelande**. 
2. Om du vill stoppa dataövervakningen använder du kommandot **IoT: Stoppa övervakning av D2C-meddelande**. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet. Du kan fortsätta med någon av följande självstudier om du vill lära dig mer om hur Azure IoT Edge kan förvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Distribuera Azure-funktionen som en modul](tutorial-deploy-function.md)
> [Distribuera Azure Stream Analytics som en modul](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
