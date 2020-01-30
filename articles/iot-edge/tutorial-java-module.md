---
title: Självstudie – självstudier för anpassad Java-modul med Azure IoT Edge
description: Den här självstudien beskriver hur du skapar en IoT Edge-modul med Java-kod och distribuerar den till en gränsenhet.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 228e50160e5c13b2d24a504b02c4bb7e3a420a46
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772915"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>Självstudie: utveckla en Java IoT Edge-modul för Linux-enheter

Du kan använda Azure IoT Edge-moduler för att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. Du använder den simulerade IoT Edge enheten som du skapade i distributions Azure IoT Edge på en simulerad enhet i [Linux](quickstart-linux.md) -snabb starten. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
>
> * använda Visual Studio Code för att skapa en IoT Edge-modul i Java som baseras på mallpaketet Maven i Azure IoT Edge och Java-SDK:et för Azure IoT-enheter.
> * använda Visual Studio Code och Docker till att skapa en Docker-avbildning och publicera den till registret
> * distribuera modulen till din IoT Edge-enhet
> * visa genererade data.

IoT Edge-modulen du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys vid kanten är användbar när du vill minska mängden data som skickas till och lagras i molnet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Lösnings omfång

Den här självstudien visar hur du utvecklar en modul i **Java** med **Visual Studio Code**och hur du distribuerar den till en **Linux-enhet**. IoT Edge stöder inte Java-moduler för Windows-enheter.

Använd följande tabell för att förstå alternativen för att utveckla och distribuera Java-moduler:

| Java | Visual Studio-kod | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Använda VS Code för Java-moduler på Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Använda VS Code för Java-moduler på Linux-ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Krav

Innan du påbörjar den här självstudien bör du ha gått igenom den föregående kursen för att konfigurera din utvecklings miljö för att utveckla Linux-behållare: [utveckla IoT Edge moduler för Linux-enheter](tutorial-develop-for-linux.md). Genom att slutföra någon av de här självstudierna bör du ha följande krav på plats:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En [Linux-enhet som kör Azure IoT Edge](quickstart-linux.md)
* Ett behållar register som [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio-kod](https://code.visualstudio.com/) som kon figurer ATS med [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) konfigurerat för att köra Linux-behållare.

Om du vill utveckla en IoT Edge-modul i Java installerar du följande ytterligare krav på utvecklings datorn: 

* [Java Extension Pack](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) för Visual Studio Code.
* [Java SE Development Kit 10](https://aka.ms/azure-jdks), och [ange `JAVA_HOME`miljövariabeln](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) så att den pekar mot din JDK-installation.
* [Maven 3.](https://maven.apache.org/)

## <a name="create-a-module-project"></a>Skapa ett modul-projekt

Följande steg skapar ett IoT Edge-modulprojekt som baseras på Maven-mallpaketet i Azure IoT Edge och Java-SDK:n för Azure IoT-enheter. Du skapar projektet med hjälp av Visual Studio Code och Azure IoT-verktygen.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Skapa en Java-lösningsmall som du kan anpassa med din egen kod.

1. I Visual Studio Code väljer du **Visa** > **Kommandopalett** för att öppna kommandopaletten i VS Code.

2. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) på kommandopaletten. Skapa lösningen genom att följ anvisningarna på kommandopaletten.

   | Field | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **Java-modul**. |
   | Ange ett värde för groupId | Ange ett grupp-ID-värde eller acceptera standardvärdet **com.edgemodule**. |
   | Ange ett modulnamn | Ge modulen namnet **JavaModule**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen fylls i baserat på det namn du angav i föregående steg. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. <br><br>Den slutliga avbildningslagringsplatsen ser ut så här: \<registernamn\>.azurecr.io/javamodule. |

   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-java-module/repository.png)

Om det är första gången du skapar Java-modulen kan det ta flera minuter att ladda ned maven-paketen. När lösningen är klar läser fönstret VS Code in din IoT Edge lösnings arbets yta. Lösnings arbets ytan innehåller fem komponenter på översta nivån:

* Mappen **moduler** innehåller Java-koden för modulen och Docker-filerna för att bygga modulen som en behållar avbildning.
* Filen **\.env** lagrar dina autentiseringsuppgifter för containerregistret.
* Filen **deployment.template.json** innehåller informationen som IoT Edge-körningen använder för att distribuera moduler på en enhet.
* Filen **Deployment. debug. template. JSON** File behållar fel söknings versionen av moduler.
* Du redigerar inte mappen **\.vscode** eller filen **\.gitignore** i den här självstudiekursen.

Om du inte angav ett containerregister när du skapade lösningen, men accepterade standardvärdet localhost:5000, har du ingen \.env-fil.

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerregistret och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten.

1. Öppna .env-filen i VS Code-utforskaren.
2. Uppdatera fälten med det **användarnamn** och **lösenord** som du kopierade från Azure Container-registret.
3. Spara filen.

### <a name="select-your-target-architecture"></a>Välj din mål arkitektur

För närvarande kan Visual Studio Code utveckla Java-moduler för Linux AMD64-och Linux ARM32v7-enheter. Du måste välja vilken arkitektur du vill använda för varje lösning, eftersom behållaren har skapats och körs på olika sätt för varje arkitektur typ. Standardvärdet är Linux AMD64.

1. Öppna paletten kommando och Sök efter **Azure IoT Edge: Ange standard plattform för Edge-lösning**eller Välj gen vägs ikonen i sido fältet längst ned i fönstret.

2. I paletten kommando väljer du mål arkitekturen i listan med alternativ. I den här självstudien använder vi en virtuell Ubuntu-dator som IoT Edge enhet, så behåller standard- **amd64**.

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

1. Öppna **modules** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java** i VS Code-utforskaren.

2. Lägg till följande kod längst upp i filen för att importera nya refererade klasser.

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

3. Lägg till följande definition i klassen **App**. Den här variabeln anger ett tröskelvärde för temperatur. Den uppmätta datortemperaturen rapporteras inte till IoT Hub förrän den överskridet det här värdet.

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

4. Ersätt körningsmetoden **MessageCallbackMqtt** med följande kod. Den här metoden anropas när modulen tar emot ett MQTT-meddelande från IoT Edge-hubben. Den filtrerar ut meddelanden som rapporterar temperaturer under temperaturtröskelvärdet som angetts via modultvillingen.

    ```java
    protected static class MessageCallbackMqtt implements MessageCallback {
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
    }
    ```

5. Lägg till följande två statiska inre klasser i klassen **App**. De här klasserna uppdatera variabeln tempThreshold när modultvillingens önskade egenskap ändras. Alla moduler har en egen modultvilling, vilket innebär att du kan konfigurera den kod som körs i en modul direkt från molnet.

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

6. Lägg till följande rader i metoden **main** efter **client.open()** för att prenumerera på uppdateringar av modultvillingen.

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

7. Spara App.java-filen.

8. Öppna filen **deployment.template.json** i arbetsytan för IoT Edge-lösningen i VS Code-utforskaren.

9. Lägg till modultvillingen **JavaModule** i distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet **moduleContent** efter **$edgeHub**-modultvillingen:

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Lägga till modultvilling till distributionsmall](./media/tutorial-java-module/module-twin.png)

10. Spara filen deployment.template.json.

## <a name="build-and-push-your-module"></a>Bygga och pusha din modul

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod i **JavaModule** för att filtrera ut meddelanden om att temperaturen för den rapporterade datorn ligger under den godkända gränsen. Nu skapar du lösningen som en containeravbildning och push-överför den till ditt containerregister.

1. Öppna den VS Code-integrerade terminalen genom att välja **Visa** > **Terminal**.

1. Logga in på Docker genom att ange följande kommando i terminalen. Logga in med användar namnet, lösen ordet och inloggnings servern från Azure Container Registry. Du kan hämta dessa värden från avsnittet **åtkomst nycklar** i registret i Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du kan få en säkerhets varning som rekommenderar att du använder `--password-stdin`. Det bästa tillvägagångs sättet rekommenderas för produktions scenarier, men det ligger utanför omfånget för den här självstudien. Mer information finns i [inloggnings referens för Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

1. I VS Code-utforskaren högerklickar du på filen **deployment.template.json** och väljer **Build and Push IoT Edge solution** (Skapa och skicka IoT Edge-lösning).

   Kommandot build och push startar tre åtgärder. Först skapar den en ny mapp i lösningen som heter **config** som innehåller det fullständiga distributions manifestet, som bygger på information i distributions mal len och andra lösningsfiler. För det andra kör den `docker build` för att bygga behållar avbildningen baserat på lämpliga Dockerfile för din mål arkitektur. Sedan körs `docker push` för att push-överföra avbildnings lagrings platsen till behållar registret.

## <a name="deploy-modules-to-device"></a>Distribuera moduler till enhet

Använd Visual Studio Code Explorer och tillägget Azure IoT Tools för att distribuera modulfönstret till din IoT Edge-enhet. Du har redan ett distributions manifest som är för berett för ditt scenario, filen **Deployment. JSON** i mappen config. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

Kontrol lera att din IoT Edges enhet är igång.

1. I Visual Studio Code Explorer expanderar du avsnittet **Azure IoT Hub-enheter** för att se din lista över IoT-enheter.

2. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Create Deployment for Single Device** (Skapa distribution för en enskild enhet).

3. Välj filen **deployment.json** i mappen **config** och klicka sedan på **Select Edge Deployment Manifest** (Välj distributionsmanifest för Edge). Använd inte filen deployment.template.json.

4. Klicka på uppdateringsknappen. Du bör se den nya **JavaModule** som körs tillsammans med **SimulatedTemperatureSensor** -modulen och **$edgeAgent** och **$edgeHub**.  

## <a name="view-the-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas.

Du kan visa statusen för din IoT Edge-enhet i avsnittet om **Azure IoT Hub-enheter** i Visual Studio Code-utforskaren. Expandera enhetsinformationen så ser du en lista med moduler som distribueras och körs.

1. I Visual Studio Code Explorer högerklickar du på namnet på din IoT Edge enhet och väljer **starta övervakning inbyggd händelse slut punkt**.

2. Visa meddelanden som kommer till IoT Hub. Det kan ta en stund innan meddelandena tas emot. Den IoT Edge enheten måste ta emot den nya distributionen och starta alla moduler. Ändringarna vi gjorde i JavaModule-koden väntar tills datorns temperatur når 25 grader innan meddelanden skickas. Den lägger också till meddelande typ **avisering** till alla meddelanden som når detta temperatur tröskelvärde.

## <a name="edit-the-module-twin"></a>Redigera modulens dubbla

Vi använde JavaModule-modulen dubbla i distributions manifestet för att ange temperatur tröskel vid 25 grader. Du kan använda modulen för att ändra funktionen utan att behöva uppdatera modulens kod.

1. I Visual Studio Code, expanderar du informationen under IoT Edge enheten för att se de moduler som körs.

2. Högerklicka på **JavaModule** och välj **Redigera modul dubbla**.

3. Hitta **TemperatureThreshold** i önskade egenskaper. Ändra värdet till en ny temperatur 5 grader till 10 grader högre än den senaste rapporterade temperaturen.

4. Spara modulens dubbla fil.

5. Högerklicka någonstans i modulens dubbla redigerings fönster och välj **Uppdatera modul dubbla**.

6. Övervaka inkommande meddelanden från enhet till molnet. Du bör se att meddelandena stannar tills det nya temperatur tröskelvärdet har uppnåtts.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en IoT Edge-modul som filtrerar rå data som genereras av din IoT Edge-enhet. När du är redo att skapa dina egna moduler kan du lära dig mer om hur du [utvecklar IoT Edge moduler](module-development.md) eller hur du [utvecklar moduler med Visual Studio Code](how-to-vs-code-develop-module.md). Se [IoT Edge module](https://github.com/Azure/iotedge/tree/master/edge-modules) -exempel för kod exempel, inklusive modulen simulerad temperatur.

Fortsätt till nästa själv studie kurs och lär dig hur Azure IoT Edge hjälper dig att distribuera Azure Cloud Services för att bearbeta och analysera data i gränsen.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
