---
title: Självstudie om att skapa anpassad Java-modul – Azure IoT Edge | Microsoft Docs
description: Den här självstudien beskriver hur du skapar en IoT Edge-modul med Java-kod och distribuerar den till en gränsenhet.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/25/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: f099d280615607382bd424063d39bb26cdeea793
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557874"
---
# <a name="tutorial-develop-a-java-iot-edge-module-and-deploy-to-your-simulated-device"></a>Självstudie: Utveckla en IoT Edge-modul i Java och distribuera till den simulerade enheten

Du kan använda Azure IoT Edge-moduler för att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. Du kommer att använda den simulerade IoT Edge-enheten som du skapade i snabbstarterna Distribuera Azure IoT Edge på en simulerad enhet i [Windows](quickstart.md) eller [Linux](quickstart-linux.md). I den här guiden får du lära dig att:    

> [!div class="checklist"]
> * använda Visual Studio Code för att skapa en IoT Edge-modul i Java som baseras på mallpaketet Maven i Azure IoT Edge och Java-SDK:et för Azure IoT-enheter.
> * använda Visual Studio Code och Docker till att skapa en Docker-avbildning och publicera den till registret
> * distribuera modulen till din IoT Edge-enhet
> * visa genererade data.


IoT Edge-modulen du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys vid kanten är användbar när du vill minska mängden data som skickas till och lagras i molnet. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Nödvändiga komponenter

En Azure IoT Edge-enhet:

* Du kan konfigurera en IoT Edge-enhet genom att följa stegen i snabbstarterna för [Linux](quickstart-linux.md) eller [Windows](quickstart.md).
* För IoT Edge på Windows-enheter har version 1.0.5 inte stöd för Java-moduler. Mer information finns i [viktig information om 1.0.5](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5). Stegvisa instruktioner för hur du installerar en specifik version finns i avsnittet om att [uppdatera IoT Edge-säkerhetsdaemon och -körning](how-to-update-iot-edge.md).

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure. 

Utvecklingsresurser:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Java Extension Pack](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) för Visual Studio Code.
* [Azure IoT Edge-tillägg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) för Visual Studio Code. 
* [Java SE Development Kit 10](https://aka.ms/azure-jdks), och [ange `JAVA_HOME`miljövariabeln](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) så att den pekar mot din JDK-installation.
* [Maven 3.](https://maven.apache.org/)
* [Docker CE](https://docs.docker.com/install/)
   * Om du utvecklar på en Windows-enhet ser du till att Docker har [konfigurerats för att använda Linux-containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). 


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
   | Resursgrupp | För enklare hantering använder du samma resursgrupp för alla testresurser som du skapar i snabbstarterna och självstudierna om IoT Edge. Till exempel **IoTEdgeResources**. |
   | Plats | Välj en plats i närheten av dig. |
   | Administratörsanvändare | Ändra värdet till **Aktivera**. |
   | SKU | Välj **Grundläggande**. | 

5. Välj **Skapa**.

6. När du har skapat containerregistret går du till det och väljer **Åtkomstnycklar**. 

7. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du kan använda dessa värden senare i självstudien för att ge åtkomst till containerregistret. 

## <a name="create-an-iot-edge-module-project"></a>Skapa ett projekt för IoT Edge-modulen
Följande steg skapar ett IoT Edge-modulprojekt som baseras på Maven-mallpaketet i Azure IoT Edge och Java-SDK:n för Azure IoT-enheter. Du skapar projektet med hjälp av Visual Studio Code och Azure IoT Edge-tillägget.

### <a name="create-a-new-solution"></a>Skapa en ny lösning

Skapa en Java-lösningsmall som du kan anpassa med din egen kod. 

1. I Visual Studio Code väljer du **Visa** > **Kommandopalett** för att öppna kommandopaletten i VS Code. 

2. I kommandopaletten anger och kör du kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning). Skapa lösningen genom att följ anvisningarna på kommandopaletten.

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **Java-modul**. |
   | Ange ett värde för groupId | Ange ett grupp-ID-värde eller acceptera standardvärdet **com.edgemodule**. |
   | Ange ett modulnamn | Ge modulen namnet **JavaModule**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen har fyllts i från föregående steg. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. Den slutliga strängen ser ut så här: \<registernamn\>.azurecr.io/javamodule. |
 
   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-java-module/repository.png)
   
Om det är första gången som en Java-modul skapas kan det ta flera minuter att ladda ned Maven-paket. Sedan läser VS Code in arbetsytan för IoT Edge-lösningen. Lösningens arbetsyta innehåller fem komponenter på den högsta nivån. Mappen **modules** innehåller Java-koden för din modul samt Dockerfiles som används för att skapa modulen som en containeravbildning. Filen **\.env** lagrar dina autentiseringsuppgifter för containerregistret. Filen **deployment.template.json** innehåller informationen som IoT Edge-körningen använder för att distribuera moduler på en enhet. Och filen **deployment.debug.template.json** innehåller felsökningsversionen av modulerna. Du kommer inte att redigera mappen **\.vscode** eller filen **\.gitignore** i den här självstudiekursen. 

Om du inte angav ett containerregister när du skapade lösningen, men accepterade standardvärdet localhost:5000, har du ingen \.env-fil. 

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerregistret och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten. 

1. Öppna .env-filen i VS Code-utforskaren. 
2. Uppdatera fälten med det **användarnamn** och **lösenord** som du kopierade från Azure Container-registret. 
3. Spara filen. 

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

1. Öppna **modules** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java** i VS Code-utforskaren.

5. Lägg till följande kod längst upp i filen för att importera nya refererade klasser.

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;
    
    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;
    
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

5. Lägg till följande definition i klassen **App**. Den här variabeln anger ett tröskelvärde för temperatur. Den uppmätta datortemperaturen rapporteras inte till IoT Hub förrän den överskridet det här värdet. 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

7. Ersätt körningsmetoden **MessageCallbackMqtt** med följande kod. Den här metoden anropas när modulen tar emot ett MQTT-meddelande från IoT Edge-hubben. Den filtrerar ut meddelanden som rapporterar temperaturer under temperaturtröskelvärdet som angetts via modultvillingen.

    ```java
        private int counter = 0;
       @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;
 
            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    ```

8. Lägg till följande två statiska inre klasser i klassen **App**. De här klasserna uppdatera variabeln tempThreshold när modultvillingens önskade egenskap ändras. Alla moduler har en egen modultvilling, vilket innebär att du kan konfigurera den kod som körs i en modul direkt från molnet.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
 
    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

9. Lägg till följande rader i metoden **main** efter **client.open()** för att prenumerera på uppdateringar av modultvillingen.

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

11. Spara filen.

12. I VS Code-utforskaren öppnar du filen deployment.template.json på arbetsytan för IoT Edge-lösningen. Den här filen instruerar **$edgeAgent** att distribuera två moduler: **tempSensor** och **JavaModule**. Standardplattformen för din IoT Edge är inställd på **amd64** i VS Code-statusfältet, vilket innebär att **JavaModule** är inställd på Linux amd64-versionen för avbildningen. Ändra standardplattformen i statusfältet från **amd64** till **arm32v7** eller **windows-amd64** om det är arkitekturen för din IoT Edge-enhet. 

   Läs mer om distributionsmanifest i avsnittet om att [förstå hur IoT Edge-moduler kan användas, konfigureras och återanvändas](module-composition.md).

   Avsnittet **registryCredentials** i filen deployment.template.json lagrar dina autentiseringsuppgifter för Docker-registret. Själva användarnamns- och lösenordsparen lagras i .env-filen, som ignoreras av Git.  

13. Lägg till modultvillingen **JavaModule** i distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet **moduleContent** efter **$edgeHub**-modultvillingen: 

   ```json
       "JavaModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Lägga till modultvilling till distributionsmall](./media/tutorial-java-module/module-twin.png)

14. Spara filen.

## <a name="build-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod i **JavaModule** för att filtrera ut meddelanden om att temperaturen för den rapporterade datorn ligger under den godkända gränsen. Nu skapar du lösningen som en containeravbildning och push-överför den till ditt containerregister. 

1. Logga in på Docker genom att ange följande kommando i Visual Studio Code-terminalen. Sedan kan du skicka modulavbildningen till ditt Azure-containerregister.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Använd användarnamnet, lösenordet och inloggningsservern som du kopierade från Azure-containerregistret i det första avsnittet. Du kan också hämta dessa värden från avsnittet **Åtkomstnycklar** i ditt register i Azure Portal.

2. I VS Code-utforskaren högerklickar du på filen deployment.template.json och väljer **Build and Push IoT Edge solution** (Skapa och skicka IoT Edge-lösning). 

När du instruerar Visual Studio Code att skapa din lösning hämtar den först information från distributionsmallen och genererar en .json-distributionsfil i en ny mapp med namnet **config**. Sedan körs två kommandon i en integrerad terminal: `docker build` och `docker push`. Dessa två kommandon skapar koden, lägger Java-appen i container och push-överför sedan koden till containerregistret du angav när du initierade lösningen. 

Den fullständiga adressen med tagg för containeravbildningen finns i den integrerade VS Code-terminalen. Avbildningsadressen skapas från information som finns i filen module.json med formatet \<lagringsplats\>:\<version\>-\<plattform\>. I den här självstudien bör den se ut så här: registryname.azurecr.io/javamodule:0.0.1-amd64.

## <a name="deploy-and-run-the-solution"></a>Distribuera och kör lösningen

I stegen i snabbstartsartikeln som du följde för att konfigurera IoT Edge-enheten distribuerade du en modul med hjälp av Azure Portal. Du kan även distribuera moduler med hjälp av Azure IoT Hub Toolkit-tillägget (tidigare Azure IoT Toolkit-tillägget) för Visual Studio Code. Du har redan ett distributionsmanifest som är förberett för ditt scenario, filen **deployment.json**. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

1. I kommandopaletten i VS Code kör du kommandot **Azure: Sign in** (Logga in) och följer anvisningarna för att logga in på ditt Azure-konto. Om du redan är inloggad kan du hoppa över det här steget.

2. I kommandopaletten i VS Code kör du **Azure IoT Hub: Välj IoT-hubb**. 

3. Välj den prenumeration och IoT-hubb som innehåller den IoT Edge-enhet som du vill konfigurera. 

4. I VS Code-utforskaren expanderar du avsnittet **Azure IoT Hub-enheter**. 

5. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Create Deployment for Single Device** (Skapa distribution för en enskild enhet). 

   ![Skapa distribution för en enskild enhet](./media/tutorial-java-module/create-deployment.png)

6. Välj filen **deployment.json** i mappen **config** och klicka sedan på **Select Edge Deployment Manifest** (Välj distributionsmanifest för Edge). Använd inte filen deployment.template.json. 

7. Klicka på uppdateringsknappen. Den nya **JavaModule**-modulen visas och körs tillsammans med **TempSensor**-modulen och **$edgeAgent** och **$edgeHub**.  

## <a name="view-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas. 

Du kan visa statusen för din IoT Edge-enhet i avsnittet om **Azure IoT Hub-enheter** i Visual Studio Code-utforskaren. Expandera enhetsinformationen så ser du en lista med moduler som distribueras och körs. 

På IoT Edge-enheten kan du visa statusen för dina distributionsmoduler med hjälp av kommandot `iotedge list`. Du bör se fyra moduler: de modulerna för IoT Edge-körning, tempSensor och den anpassade modul du skapade i den här självstudien. Det kan ta några minuter för alla moduler att starta, så kör kommandot igen om du till en början inte ser alla. 

Du kan visa de meddelanden som genereras av alla moduler med hjälp av kommandot `iotedge logs <module name>`. 

Du kan visa meddelanden när de anländer till IoT-hubben med hjälp av Visual Studio Code. 

1. Om du vill övervaka data som kommer till IoT-hubben väljer du ellipsen (**...** ) och sedan **Start Monitoring D2C Messages** (Starta övervakning av D2C-meddelanden).
2. Om du vill övervaka D2C-meddelandet för en specifik enhet högerklickar du på enheten i listan och väljer **Starta övervakning av D2C-meddelanden**.
3. Om du vill stoppa övervakningen av data kör du kommandot **Azure IoT Hub: Stop monitoring D2C message** (Sluta övervaka D2C-meddelande) i kommandopaletten. 
4. Om du vill visa eller uppdatera modultvillingen högerklickar du på modulen i listan och väljer **Redigera modultvilling**. Om du vill uppdatera modultvillingen sparar du JSON-tvillingfilen, högerklickar i redigeringsområdet och väljer **Uppdatera modultvilling**.
5. Om du vill visa Docker-loggar installerar du [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) för VS Code. Du kan hitta moduler som körs lokalt i Docker-utforskaren. Du kan visa dem i den integrerade terminalen genom att välja **Visa loggar** på snabbmenyn.
 
## <a name="clean-up-resources"></a>Rensa resurser 

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet. 

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul med kod för att filtrera rådata som genereras av din IoT Edge-enhet. Du kan fortsätta med någon av följande självstudier om du vill veta mer om hur Azure IoT Edge kan hjälpa dig att omvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Lagra data på gränsen med SQL Server-databaser](tutorial-store-data-sql-server.md)

