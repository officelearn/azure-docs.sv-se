---
title: Självstudie utveckla C-modulen för Windows-Azure IoT Edge | Microsoft Docs
description: Den här självstudien visar hur du skapar en IoT Edge-modul med C-kod och distribuerar den till en Windows-enhet som kör IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: fdd1aeea20160bb1a9f91de934bd9268a179648a
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529223"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Självstudie: utveckla en C IoT Edge-modul för Windows-enheter

Använd Visual Studio för att utveckla C-kod och distribuera den till en Windows-enhet som kör Azure IoT Edge. 

Du kan använda Azure IoT Edge-moduler för att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. I den här guiden får du lära dig att:    

> [!div class="checklist"]
> * Använd Visual Studio för att skapa en IoT Edge-modul som baseras på C SDK.
> * Använd Visual Studio och Docker för att skapa en Docker-avbildning och publicera den i registret.
> * distribuera modulen till din IoT Edge-enhet
> * visa genererade data.

IoT Edge-modulen du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys är användbar för att minska mängden data som kommuniceras till och lagras i molnet. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Lösnings omfång

Den här självstudien visar hur du utvecklar en modul i **C** med **Visual Studio 2019**och hur du distribuerar den till en **Windows-enhet**. Om du utvecklar moduler för Linux-enheter går du till [utveckla en C IoT Edge-modul för Linux-enheter](tutorial-c-module.md) i stället. 

Använd följande tabell för att förstå alternativen för att utveckla och distribuera C-moduler till Windows-enheter: 

| C | Visual Studio-kod | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![Utveckla C-moduler för WinAMD64 i Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Krav

Innan du påbörjar den här självstudien bör du ha gått igenom den föregående själv studie kursen för att konfigurera utvecklings miljön för utveckling av Windows-behållare: [utveckla IoT Edge moduler för Windows-enheter](tutorial-develop-for-windows.md). När du har slutfört den här självstudien bör du ha följande krav på plats: 

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En [Windows-enhet som kör Azure IoT Edge](quickstart.md).
* Ett behållar register som [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) konfigurerat med [Azure IoT Edge tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) -tillägget.
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) konfigurerat för att köra Windows-behållare.
* Installera Azure IoT C SDK för Windows x64 till vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```
   
> [!TIP]
> Om du använder Visual Studio 2017 (version 15,7 eller senare) kan du ladda ned och installera [Azure IoT Edge verktyg](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) för vs 2017 från Visual Studio Marketplace

## <a name="create-a-module-project"></a>Skapa ett modul-projekt

Följande steg skapar ett IoT Edge module-projekt som baseras på C SDK med hjälp av Visual Studio och tillägget Azure IoT Edge-verktyg. När du har skapat en Project-mall lägger du till ny kod så att modulen filtrerar ut meddelanden utifrån deras rapporterade egenskaper. 

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Skapa en C-lösningsmall som du kan anpassa med din egen kod.

1. Starta Visual Studio 2019 och välj **Skapa nytt projekt**.

2. I fönstret nytt projekt söker du **IoT Edge** projekt och väljer **Azure IoT Edge (Windows amd64)-** projektet. Klicka på **Next**. 

   ![Skapa ett nytt Azure IoT Edge-projekt](./media/tutorial-c-module-windows/new-project.png)

3. I fönstret Konfigurera ditt nya projekt byter du namn på projektet och lösningen till något beskrivande som **CTutorialApp**. Skapa projektet genom att klicka på **skapa** . 

   ![Konfigurera ett nytt Azure IoT Edge projekt](./media/tutorial-c-module-windows/configure-project.png)

4. I fönstret IoT Edge program och modul konfigurerar du ditt projekt med följande värden: 

   | Fält | Värde |
   | ----- | ----- |
   | Välj en mall | Välj **C-modul**. | 
   | Modulnamn projekt namn | Ge modulen namnet **CModule**. | 
   | Docker-avbildningens lagrings plats | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Behållar avbildningen fylls i automatiskt från värdet för modulens projekt namn. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. <br><br> Den slutliga avbildningsplatsen ser ut så här: \<registernamn\>.azurecr.io/cmodule. |

   ![Konfigurera ditt projekt för mål enheten, modultypen och behållar registret](./media/tutorial-c-module-windows/add-application-and-module.png)

5. Välj **Lägg till** för att skapa projektet. 

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Distributions manifestet delar autentiseringsuppgifterna för behållar registret med IoT Edge Runtime. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten. Använd autentiseringsuppgifterna från avsnittet **åtkomst nycklar** i Azure Container Registry. 

1. Öppna filen **Deployment. template. JSON** i Visual Studio-Solution Explorer. 

2. Hitta egenskapen **registryCredentials** i önskade egenskaper för $edgeAgent. 

3. Uppdatera egenskapen med dina autentiseringsuppgifter med följande format: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Spara filen deployment.template.json. 

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

Koden för standardmodulen tar emot meddelanden i en indatakö och skickar dem vidare via en utgående kö. Nu ska vi lägga till ytterligare kod så att modulen bearbetar meddelandena på gränsen innan de vidarebefordrar dem till IoT Hub. Uppdatera modulen så att den analyserar temperatur data i varje meddelande, och skickar endast meddelandet till IoT Hub om temperaturen överskrider ett visst tröskelvärde. 


1. Data från sensorn i det här scenariot kommer i JSON-format. Om du vill filtrera meddelanden i JSON-format importerar du ett JSON-bibliotek för C. Den här självstudien använder Parson.

   1. Ladda ned den [Parson GitHub-lagringsplatsen](https://github.com/kgabis/parson). Kopiera **Parson. c** -och **Parson. h** -filerna till **CModule** -projektet.

   2. I Visual Studio öppnar du filen **CMakeLists. txt** från projektmappen CModule. Längst upp i filen importerar du Parson-filerna som ett bibliotek med namnet **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Lägg till **my_parson** i listan över bibliotek i avsnittet **Target_link_libraries** i filen CMakeLists. txt.

   4. Spara filen **CMakeLists.txt**.

   5. Öppna **CModule**  > **main. c**. Lägg till en ny i slutet av listan över include-instruktioner för att ta med `parson.h` för JSON-stöd:

      ```c
      #include "parson.h"
      ```

2.  I filen **main. c** lägger du till en global variabel som heter `temperatureThreshold` bredvid variabeln messagesReceivedByInput1Queue. Variabeln anger det värde som den uppmätta temperaturen måste överstiga för att data ska skickas till IoT Hub.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Hitta `CreateMessageInstance`-funktionen i Main. c. Ersätt den inre if-else-instruktionen med följande kod som lägger till några rader med funktioner: 

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

   De nya kodrader i Else-instruktionen lägger till en ny egenskap i meddelandet, som namnger meddelandet som en avisering. Den här koden etiketterar alla meddelanden som aviseringar, eftersom vi kommer att lägga till funktioner som bara skickar meddelanden till IoT Hub om de rapporterar höga temperaturer. 

4. Hitta `InputQueue1Callback` funktionen och ersätt hela-funktionen med följande kod. Den här funktionen implementerar själva meddelandefiltret. När ett meddelande tas emot kontrollerar det om den rapporterade temperaturen överskrider tröskelvärdet. Om ja, vidarebefordrar det meddelandet via kön för utdata. Annars ignorerar den meddelandet. 

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

        // If temperature exceeds threshold, send to output1
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
        // If message does not exceed threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

5. Lägg till en `moduleTwinCallback`-funktion. Metoden tar emot uppdateringar av önskade egenskaper från modultvillingen och uppdaterar variabeln **temperatureThreshold** som ska matchas. Alla moduler har en egen modultvilling, vilket innebär att du kan konfigurera den kod som körs i en modul direkt från molnet.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
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

6. Hitta `SetupCallbacksForModule` funktionen. Ersätt funktionen med följande kod som lägger till en **Else If** -instruktion för att kontrol lera om modulen har uppdaterats. 

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

7. Spara filen main.c.

8. Öppna filen **Deployment. template. JSON** . 

9. Lägg till CModule-modultvillingen till distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet `moduleContent` efter `$edgeHub`-modultvillingen:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Lägga till CModule-tvilling till distributionsmall](./media/tutorial-c-module-windows/module-twin.png)

1. Spara filen **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Bygga och pusha din modul

I föregående avsnitt skapade du en IoT Edge-lösning och lagt till kod i **CModule** för att filtrera bort meddelanden där den rapporterade dator temperaturen är under det acceptabla tröskelvärdet. Nu behöver du bygga lösningen som en containeravbildning och push-överföra den till ditt containerregister. 

1. Använd följande kommando för att logga in på Docker på din utvecklings dator. Logga in med användar namnet, lösen ordet och inloggnings servern från Azure Container Registry. Du kan hämta dessa värden från avsnittet **åtkomst nycklar** i registret i Azure Portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du kan få en säkerhets varning som rekommenderar att du använder `--password-stdin`. Det bästa tillvägagångs sättet rekommenderas för produktions scenarier, men det ligger utanför omfånget för den här självstudien. Mer information finns i [inloggnings referens för Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

2. Högerklicka på det projekt namn som du vill bygga i Visual Studio-Solution Explorer. Standard namnet är **AzureIotEdgeApp1** och eftersom du skapar en Windows-modul bör tillägget vara **Windows. amd64**. 

3. Välj **build-och push-IoT Edge moduler**. 

   Kommandot build och push startar tre åtgärder. Först skapar den en ny mapp i lösningen som heter **config** som innehåller det fullständiga distributions manifestet, som bygger ut information i distributions mal len och andra lösningsfiler. För det andra körs `docker build` för att bygga behållar avbildningen baserat på lämpliga Dockerfile för din mål arkitektur. Sedan körs `docker push` för att push-överföra avbildnings lagrings platsen till behållar registret. 

## <a name="deploy-modules-to-device"></a>Distribuera moduler till enhet

Använd Visual Studio Cloud Explorer och tillägget Azure IoT Edge Tools för att distribuera modulfönstret till din IoT Edge-enhet. Du har redan ett distributions manifest som är för berett för ditt scenario, filen **Deployment. JSON** i mappen config. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

Kontrol lera att din IoT Edges enhet är igång. 

1. Expandera resurserna i Visual Studio Cloud Explorer för att se din lista över IoT-enheter. 

2. Högerklicka på namnet på den IoT Edge enhet som du vill ta emot distributionen av. 

3. Välj **skapa distribution**.

4. I Utforskaren väljer du filen **Deployment. Windows-amd64** i mappen config i din lösning. 

5. Uppdatera Cloud Explorer för att se de distribuerade modulerna som visas på din enhet. 


## <a name="view-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas. 

Du kan använda tillägget IoT Edge-verktyg för att visa meddelanden när de kommer till din IoT Hub. 

1. I Visual Studio Cloud Explorer väljer du namnet på din IoT Edge-enhet. 

2. I listan **åtgärder** väljer du **starta övervakning inbyggd händelse slut punkt**. 

3. Visa meddelanden som kommer till IoT Hub. Det kan ta en stund innan meddelandena anländer, eftersom den IoT Edge enheten måste ta emot sin nya distribution och starta alla moduler. Ändringarna vi gjorde i CModule-koden väntar tills datorns temperatur når 25 grader innan meddelanden skickas. Den lägger också till meddelande typ **avisering** till alla meddelanden som når detta temperatur tröskelvärde. 

   ![Visa meddelanden som anländer på IoT Hub](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Redigera modulens dubbla

Vi använde CModule-modulen för att ange temperatur tröskeln vid 25 grader. Du kan använda modulen för att ändra funktionen utan att behöva uppdatera modulens kod.

1. Öppna filen **Deployment. Windows-amd64. JSON** i Visual Studio. (Inte filen Deployment. template. Om du inte ser distributions manifestet i konfigurations filen i Solution Explorer väljer du ikonen **Visa alla filer** i Explorer-verktygsfältet.)

2. Hitta CModule och ändra värdet för parametern **temperatureThreshold** till en ny temperatur på 5 grader till 10 grader högre än den senaste rapporterade temperaturen. 

3. Spara filen **Deployment. Windows-amd64. JSON** .

4. Följ distributions stegen igen för att tillämpa det uppdaterade distributions manifestet på enheten. 

5. Övervaka inkommande meddelanden från enhet till molnet. Du bör se att meddelandena stannar tills det nya temperatur tröskelvärdet har uppnåtts. 

## <a name="clean-up-resources"></a>Rensa resurser 

Om du tänker fortsätta till nästa rekommenderade artikel kan du behålla de resurser och konfigurationer du har skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet. 

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du använde i den här artikeln för att undvika avgifter. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul med kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att skapa dina egna moduler kan du lära dig mer om hur du [utvecklar egna IoT Edge moduler](module-development.md) eller hur du [utvecklar moduler med Visual Studio](how-to-visual-studio-develop-module.md). Du kan fortsätta till nästa självstudier för att lära dig hur Azure IoT Edge kan hjälpa dig att distribuera Azure Cloud Services för att bearbeta och analysera data i gränsen.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom vision service](tutorial-deploy-custom-vision.md)
