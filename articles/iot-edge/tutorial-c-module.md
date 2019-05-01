---
title: Självstudien utveckla C-modell för Linux - Azure IoT Edge | Microsoft Docs
description: Den här självstudien visar hur du skapar en IoT Edge-modul med C-koden och distribuerar den till en Linux-enhet med IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: f5f6b6473a3cbe078ae2357e1f908788e54af25d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64573415"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Självstudier: Utveckla en C IoT Edge-modul för Linux-enheter

Använd Visual Studio Code för att utveckla C-koden och distribuera den till en Linux-enhet som kör Azure IoT Edge. 

Du kan använda IoT Edge-moduler till att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Använda Visual Studio Code för att skapa en IoT Edge-modul i C
> * Använda Visual Studio Code och Docker för att skapa en Docker-avbildning och publicera den till ett containerregister
> * Distribuera modulen till din IoT Edge-enhet
> * Visa genererade data

IoT Edge-modulen som du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys är användbar för att minska mängden data som kommuniceras till och lagras i molnet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Lösningen omfattning

Den här kursen visar hur du utvecklar en modul i **C** med **Visual Studio Code**, och hur du distribuerar det till en **Linux enheten**. Om du utvecklar moduler för Windows-enheter kan du gå till [utveckla en C IoT Edge-modul för Windows-enheter](tutorial-c-module-windows.md) i stället.

Använd följande tabell för att förstå dina alternativ för utveckling och distribution C-moduler till Linux: 

| C | Visual Studio-koden | Visual Studio 2017 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Använda VS Code för C-moduler på Linux AMD64](./media/tutorial-c-module/green-check.png) | ![Använda VS 2017 för C-moduler på Linux AMD64](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Använda VS Code för C-moduler på Linux ARM32](./media/tutorial-c-module/green-check.png) | ![Använda VS 2017 för C-moduler på Linux ARM32](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du påbörjar den här självstudiekursen ska du har gått igenom den tidigare självstudiekursen för att ställa in din utvecklingsmiljö för utveckling av Linux-behållare: [Utveckla IoT Edge-moduler för Linux-enheter](tutorial-develop-for-linux.md). När du har slutfört självstudien bör du har följande krav på plats: 

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En [Linux-enhet som kör Azure IoT Edge](quickstart-linux.md)
* Ett behållarregister, till exempel [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) konfigurerats med den [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) konfigurerats för att köra Linux-behållare.

För att utveckla en IoT Edge-modul i C kan du installera följande ytterligare krav på utvecklingsdatorn: 

* [C/C++-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) för Visual Studio Code.

## <a name="create-a-module-project"></a>Skapa en modul-projekt

Följande steg för att skapa ett projekt med IoT Edge-modulen för C med hjälp av Visual Studio Code och tillägget Azure IoT Tools. När du har skapat en projektmall kan du lägga till ny kod så att modulen filtrerar ut meddelanden baserat på deras rapporterade egenskaper. 

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Skapa en C-lösningsmall som du kan anpassa med din egen kod.

1. Välj **Visa** > **Kommandopalett** för att öppna kommandopaletten i VS Code.

2. I kommandopaletten skriver du och kör kommandot **Azure: Logga in** och följer anvisningarna för att logga in på ditt Azure-konto. Om du redan har loggat in kan du hoppa över det här steget.

3. I kommandopaletten skriver och kör du kommandot **Azure IoT Edge: Ny IoT Edge-lösning**. Skapa lösningen genom att följ anvisningarna på kommandopaletten.

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **C-modul**. |
   | Ange ett modulnamn | Ge modulen namnet **CModule**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen fylls i baserat på det namn du angav i föregående steg. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. <br><br> Den slutliga avbildningsplatsen ser ut så här: \<registernamn\>.azurecr.io/cmodule. |
 
   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-c-module/repository.png)

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerregistret och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten.

1. Öppna .env-filen i VS Code-utforskaren.
2. Uppdatera fälten med det **användarnamn** och **lösenord** som du kopierade från Azure Container-registret.
3. Spara filen.

### <a name="select-your-target-architecture"></a>Välj din mål-arkitektur

Visual Studio Code kan för närvarande kan utveckla C-moduler för Linux AMD64- och Linux ARM32v7-enheter. Du måste välja vilken arkitektur som mål med varje lösning eftersom behållaren har skapats och körs på olika sätt för varje arkitekturtyp av. Standardvärdet är Linux AMD64. 

1. Öppna kommandopaletten och Sök efter **Azure IoT Edge: Ange standard målplattform för lösning**, eller klicka på genvägsikonen i Sidopanel längst ned i fönstret. 

2. Välj mål-arkitektur i kommandopaletten, från listan med alternativ. Den här självstudien använder vi en Ubuntu-dator som IoT Edge-enhet, så kommer behåller du standardvärdet **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

Standardkoden för modulen tar emot meddelanden på ett inkommande kö och skickar dem via en utgående kö. Vi lägger till ytterligare kod så att meddelanden vid gränsen bearbetar i modulen innan den vidarebefordrar dem till IoT Hub. Uppdatera modulen så att den analyserar temperaturdata i varje meddelande och endast skickar meddelandet till IoT Hub om temperaturen överstiger ett visst tröskelvärde. 

1. Data från sensorn i det här scenariot kommer i JSON-format. Om du vill filtrera meddelanden i JSON-format importerar du ett JSON-bibliotek för C. Den här självstudien använder Parson.

   1. Ladda ned den [Parson GitHub-lagringsplatsen](https://github.com/kgabis/parson). Kopiera filerna **parson.c** och **parson.h** till mappen **CModule**.

   2. Öppna **modules** > **CModule** > **CMakeLists.txt**. Längst upp i filen importerar du Parson-filerna som ett bibliotek med namnet **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Lägg till **my_parson** i listan över bibliotek i funktionen **target_link_libraries** i CMakeLists.txt.

   4. Spara filen **CMakeLists.txt**.

   5. Öppna **modules** > **CModule** > **main.c**. Längst ned i listan över ingår satser, lägga till en ny som ska innehålla `parson.h` för JSON-stöd:

      ```c
      #include "parson.h"
      ```

1. I filen **main.c** lägger du till en global variabel med namnet `temperatureThreshold` efter include-avsnittet. Variabeln anger det värde som den uppmätta temperaturen måste överstiga för att data ska skickas till IoT Hub.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Hitta den `CreateMessageInstance` funktionen i main.c. Ersätt den inre if-else-instruktionen med följande kod som lägger till några rader funktioner: 

   ```c
   if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
   {
       free(messageInstance);
       messageInstance = NULL;
   }
   else
   {
       messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
       MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
       if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
       {
          printf("ERROR: Map_AddOrUpdate Failed!\r\n");
       }
   }
   ```

   De nya raderna med kod i annan instruktionen lägga till en ny egenskap i meddelandet som etiketter meddelandet som en avisering. Den här koden etiketten alla meddelanden som aviseringar, eftersom vi lägger till funktioner som bara skickar meddelanden till IoT Hub om de rapporterar höga temperaturer. 

1. Ersätt hela funktionen `InputQueue1Callback` med följande kod. Den här funktionen implementerar själva meddelandefiltret. När ett meddelande tas emot, kontrollerar den om den rapporterade temperaturen överstiger tröskelvärdet. Om Ja, vidarebefordrar meddelandet via den utgående kön. Annars kan du sedan meddelandet ignoreras. 

    ```c
    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) != IOTHUB_MESSAGE_OK)
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Lägg till en `moduleTwinCallback`-funktion. Metoden tar emot uppdateringar av önskade egenskaper från modultvillingen och uppdaterar variabeln **temperatureThreshold** som ska matchas. Alla moduler har en egen modultvilling, vilket innebär att du kan konfigurera den kod som körs i en modul direkt från molnet.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

1. Hitta den `SetupCallbacksForModule` funktion. Ersätt funktionen med följande kod som lägger till en **annars om** -uttrycket för att kontrollera om modultvillingen har uppdaterats.

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = __FAILURE__;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = __FAILURE__;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

1. Spara filen main.c.

1. I VS Code-utforskaren öppnar du filen **deployment.template.json** i arbetsytan för IoT Edge-lösningen. 

1. Lägg till CModule-modultvillingen till distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet `moduleContent` efter `$edgeHub`-modultvillingen:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Lägga till CModule-tvilling till distributionsmall](./media/tutorial-c-module/module-twin.png)

1. Spara filen **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Skapa och skicka din modul

I det föregående avsnittet du skapade en IoT Edge-lösning och lagt till kod till CModule som filtrerar ut meddelanden där temperaturen som rapporteras datorn ligger inom godkända gränser. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

1. Öppna den VS Code-terminalen genom att välja **visa** > **Terminal**.

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

4. Klicka på uppdateringsknappen. Den nya **CModule**-modulen visas och körs tillsammans med **TempSensor**-modulen och **$edgeAgent** och **$edgeHub**.

## <a name="view-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas.

Du kan visa statusen för din IoT Edge-enhet i avsnittet om **Azure IoT Hub-enheter** i Visual Studio Code-utforskaren. Expandera enhetsinformationen så ser du en lista med moduler som distribueras och körs.

1. I Visual Studio Code-Utforskaren högerklickar du på namnet på din IoT Edge-enhet och välj **börja övervaka D2C meddelanden**.

2. Visa meddelanden som inkommer på din IoT-hubb. Det kan ta en stund innan meddelanden tas emot, eftersom IoT Edge-enheten måste ta emot dess ny distribution och starta alla moduler. Vi ändringar i koden CModule vänta sedan tills datorn temperaturen når 25 grader innan du skickar meddelanden. Det ger också meddelandetypen **avisering** att alla meddelanden som når gränsen temperatur. 

   ![Visa meddelanden anländer till IoT Hub](./media/tutorial-c-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Redigera modultvillingen

Vi använde modultvilling CModule i manifestet distribution Ange tröskelvärdet för temperatur vid 25 grader. Du kan använda modultvillingen för att ändra funktionen utan att behöva uppdatera modulen koden.

1. Expandera information under din IoT Edge-enhet för att se modulerna som körs i Visual Studio Code. 

2. Högerklicka på **CModule** och välj **redigera modultvilling**. 

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

