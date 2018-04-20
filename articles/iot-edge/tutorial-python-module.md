---
title: Azure IoT kant Python-modulen | Microsoft Docs
description: Skapa en IoT-Edge-modul med Python-kod och distribuerar den till en insticksenhet
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: d5bad277e6a54b23f0e3ef7321e82d212ae885d3
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
---
# <a name="develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device---preview"></a>Utveckla och distribuera en Python IoT kant-modul till den simulerade enheten - förhandsgranskning

Du kan använda IoT kant-moduler för att distribuera kod som implementerar affärslogiken direkt till IoT Edge-enheter. Den här självstudiekursen vägleder dig genom att skapa och distribuera en IoT-Edge-modul som filtrerar sensordata. Ska du använda simulerade IoT gränsenheten som du skapade i distribuera Azure IoT kanten på en simulerad enhet i [Windows] [ lnk-tutorial1-win] eller [Linux] [ lnk-tutorial1-lin]självstudier. I den här guiden får du lära dig att:    

> [!div class="checklist"]
> * Använd Visual Studio-koden för att skapa en IoT kant Python-modul
> * Använda Visual Studio Code och Docker för att skapa en docker-avbildning och publicera den i registret 
> * Distribuera modulen till din IoT Edge-enhet
> * Visa genererade data


Modulen IoT kant som du skapar i den här självstudiekursen filtrerar temperatur data som genereras av enheten. Den endast skickar meddelanden uppströms om är överskrider ett angivet tröskelvärde. Den här typen av analys i utkanten är användbart för att minska mängden data meddelas och lagras i molnet. 

> [!IMPORTANT]
> För närvarande kan Python-modul endast köra i amd64 Linux behållare. Kan inte körs i Windows-behållare eller ARM-baserade behållare. 

## <a name="prerequisites"></a>Förutsättningar

* Azure IoT gränsenheten som du skapade i Snabbstart eller första självstudierna.
* Primärnyckelns anslutningssträng för IoT Edge-enheten.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Python-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Docker](https://docs.docker.com/engine/installation/) på samma dator som har Visual Studio-koden. Community Edition (CE) är tillräcklig för den här kursen. 
* [Python](https://www.python.org/downloads/).
* [PIP](https://pip.pypa.io/en/stable/installing/#installation) för att installera Python-paket.

## <a name="create-a-container-registry"></a>Skapa ett behållarregister
I den här självstudien använder du Azure IoT Edge-tillägget för VS Code för att skapa en modul och skapa en **behållaravbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.  

Du kan använda valfritt Docker-kompatibelt register för den här självstudien. Två populära Docker-registertjänster som finns tillgängliga i molnet är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). I den här kursen använder vi Azure Container Registry. 

1. I [Azure Portal](https://portal.azure.com) väljer du **Skapa en resurs** > **Behållare** > **Azure Container Registry**.
2. Namnge ditt register, välj en prenumeration, välj en resursgrupp och ange SKU:n till **Basic**. 
3. Välj **Skapa**.
4. När du har skapat ditt behållarregister, navigerar du till det och väljer **Åtkomstnycklar**. 
5. Växla till **Administratörsanvändare** för att **Aktivera**.
6. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du kommer att använda de här värdena senare i självstudien. 

## <a name="create-an-iot-edge-module-project"></a>Skapa ett projekt för IoT kant-modul
Följande steg visar hur du skapar en IoT kant Python-modul med hjälp av Visual Studio Code och Azure IoT kant-tillägget.
1. I Visual Studio Code väljer **visa** > **integrerad Terminal** att öppna VS koden integrerad terminal.
2. Ange följande kommando för att installera (eller uppdatera) i terminalen integrerad i **cookiecutter**:

    ```cmd/sh
    pip install -U cookiecutter
    ```

3. Skapa ett projekt för den nya modulen. Följande kommando skapar projektmappen **FilterModule**, med lagringsplatsen för behållaren. Parametern för `image_repository` ska vara i form av `<your container registry name>.azurecr.io/filtermodule` om du använder Azure-behållaren registret. Ange följande kommando i den aktuella arbetsmappen:

    ```cmd/sh
    cookiecutter --no-input https://github.com/Azure/cookiecutter-azure-iot-edge-module module_name=FilterModule image_repository=<your container registry address>/filtermodule
    ```
 
4. Välj **filen** > **öppna mappen**.
5. Bläddra till den **FilterModule** mappen och klicka på **Välj mapp** öppna projektet i VS-kod.
6. Klicka i VS kod explorer **main.py** att öppna den.
7. Längst upp i den **FilterModule** namnområde, importera den `json` bibliotek:

    ```python
    import json
    ```

8. Lägg till den `TEMPERATURE_THRESHOLD` och `TWIN_CALLBACKS` under globala räknare. Tröskelvärdet för temperatur anger det värde som uppmätta temperaturen får överstiga för data som ska skickas till IoT-hubb.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

9. Uppdatera funktionen `receive_message_callback` med nedan innehåll.

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

10. Lägga till en ny funktion `device_twin_callback`. Den här funktionen kommer att anropas när egenskaperna uppdateras.

    ```python
    # device_twin_callback is invoked when twin's desired properties are updated.
    def device_twin_callback(update_state, payload, user_context):
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

11. I klassen `HubManager`, Lägg till en ny rad i den `__init__` metod för att initiera den `device_twin_callback` funktionen som du just lagt till.

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
        
   Använd användarnamn, lösenord och inloggningsserver som du kopierade från Azure-behållaren registret när du skapade den.

2. I VS Code-utforskaren högerklickar du på filen **module.json** och klickar sedan på **Docker-avbildning för Build and Push IoT-modul**. I popup-listrutan överst i fönstret VS-kod, väljer din plattform för behållare, exempelvis **amd64** för Linux-behållaren. VS kod containerize den `main.py` och nödvändiga beroendena sedan push-installera den behållaren registret som du angav. Det kan ta flera minuter för första gången du att skapa avbildningen.

3. Den fullständiga adressen med tagg för behållaravbildningen finns i den integrerade VS Code-terminalen. Mer information om versions- och push-definitionen finns i filen `module.json`.

## <a name="add-registry-credentials-to-edge-runtime"></a>Lägga till registret autentiseringsuppgifter Edge runtime
Lägg till autentiseringsuppgifterna för ditt register i Edge-runtimen på den dator där du kör din Edge-enhet. Dessa autentiseringsuppgifter ge runtime-åtkomst till pull-behållaren. 

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
2. Kontrollera att den **tempSensor** modulen fylls i automatiskt. Om inte, Använd följande steg för att lägga till den:
    1. Välj **Lägg till IoT Edge-modul**.
    2. I **Namn**-fältet skriver du `tempSensor`.
    3. I **URI för avbildning**-fältet skriver du `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Lämna de andra inställningarna oförändrade och klicka på **Spara**.
9. Lägg till den **filterModule** modul som du skapade i föregående avsnitt. 
    1. Välj **Lägg till IoT Edge-modul**.
    2. I **Namn**-fältet skriver du `filterModule`.
    3. I **URI för avbildning**-fältet, anger du adressen till din avbildning, till exempel `<your container registry address>/filtermodule:0.0.1-amd64`. Avbildningens fullständiga adress kan hittas i föregående avsnitt.
    4. Kontrollera den **aktivera** så att du kan redigera modulen dubbla. 
    5. Ersätt JSON i textrutan för modulen dubbla med följande JSON: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Klicka på **Spara**.
10. Klicka på **Nästa**.
11. I steget **Ange vägar** kopierar du den JSON nedanför till textrutan. Moduler publicera alla meddelanden i Edge-körningsmiljön. Deklarativ regler i körningsmiljön definiera där meddelanden. I den här kursen behöver du två vägar. Första vägen transporter meddelanden från temperatursensorn till filtermodul via slutpunkten ”input1”, vilket är den slutpunkt du konfigurerade med den **FilterMessages** hanterare. Den andra vägen transporterar meddelanden från filtermodulen till IoT Hub. I den här vägen är `upstream` ett särskilt mål som talar om för Edge-hubben att skicka meddelanden till IoT Hub. 

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
6. Återgå till IoT Edge-enhetens informationssida och välj **Uppdatera**. Du bör se den nya **filtermodule** körs tillsammans med den **tempSensor** modulen och **IoT kant runtime**. 

## <a name="view-generated-data"></a>Visa genererade data

Övervaka meddelanden från enhet till molnet som skickas från din IoT Edge-enhet till din IoT-hubb:
1. Konfigurera Azure IoT Toolkit-tillägget med anslutningssträngen för din IoT-hubb: 
    1. Öppna Utforskaren VS kod genom att välja **visa** > **Explorer**. 
    3. I Utforskaren klickar du på **IOT HUB-enheter** och klicka sedan på **...** . Klicka på **ange anslutningssträngen för IoT-hubb** och ange anslutningssträngen för IoT-hubb som din IoT insticksenhet ansluter till i popup-fönstret. 

        För att hitta anslutningssträngen panelen för din IoT-hubb i Azure-portalen och klicka sedan på **principer för delad åtkomst**. I **principer för delad åtkomst**, klicka på den **iothubowner** princip och kopiera IoT-hubb anslutningen sträng i den **iothubowner** fönster.   

1. Om du vill övervaka data som inkommer till IoT-hubben, Välj **visa** > **kommandot paletten** och Sök efter den **IoT: börja övervaka D2C meddelande** menykommandot. 
2. Om du vill stoppa övervakningen av data, Använd den **IoT: stoppa övervakningen D2C meddelandet** kommando. 

## <a name="next-steps"></a>Nästa steg

I kursen får skapat du en gräns för IoT-modul som innehåller kod för att filtrera rådata som genereras av enheten IoT kant. Du kan fortsätta in på något av följande kurser vill veta mer om andra sätt att Azure IoT gräns kan du göra data till affärsinsikter kant.

> [!div class="nextstepaction"]
> [Distribuera Azure-funktion som en modul](tutorial-deploy-function.md)
> [distribuera Azure Stream Analytics som en modul](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
