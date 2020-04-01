---
title: Självstudiekurs utvecklar C-modul för Windows - Azure IoT Edge | Microsoft-dokument
description: Den här självstudien visar hur du skapar en IoT Edge-modul med C-kod och distribuerar den till en Windows-enhet som kör IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 09d039801107a44df4f3bf3745a1e074e6d708b8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76760972"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Självstudiekurs: Utveckla en C IoT Edge-modul för Windows-enheter

Använd Visual Studio för att utveckla C-kod och distribuera den till en Windows-enhet som kör Azure IoT Edge.

Du kan använda Azure IoT Edge-moduler till att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * Använd Visual Studio för att skapa en IoT Edge-modul som baseras på C SDK.
> * Använd Visual Studio och Docker för att skapa en Docker-avbildning och publicera den i registret.
> * distribuera modulen till din IoT Edge-enhet
> * visa genererade data.

IoT Edge-modulen du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys vid kanten är användbar när du vill minska mängden data som skickas till och lagras i molnet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Lösningsscope

Den här självstudien visar hur du utvecklar en modul i **C** med **Visual Studio 2019** och distribuerar den till en **Windows-enhet**. Om du utvecklar moduler för Linux-enheter går du till [Utveckla en C IoT Edge-modul för Linux-enheter](tutorial-c-module.md) istället.

Använd följande tabell för att förstå dina alternativ för att utveckla och distribuera C-moduler till Windows-enheter:

| C | Visual Studio-koden | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![Utveckla C-moduler för WinAMD64 i Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Krav

Innan du påbörjar den här självstudien bör du ha gått igenom den tidigare självstudien för att konfigurera utvecklingsmiljön för Windows-behållarutveckling: [Utveckla IoT Edge-moduler för Windows-enheter](tutorial-develop-for-windows.md). När du har slutfört den självstudien bör du ha följande förutsättningar på plats:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En [Windows-enhet som kör Azure IoT Edge](quickstart.md).
* Ett behållarregister, till exempel [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) konfigurerad med tillägget [Azure IoT Edge Tools.](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) konfigurerad för att köra Windows-behållare.
* Installera Azure IoT C SDK för Windows x64 via vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Om du använder Visual Studio 2017 (version 15.7 eller senare) kan du hämta och installera [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) för VS 2017 från Visual Studio marketplace

## <a name="create-a-module-project"></a>Skapa ett modulprojekt

Följande steg skapar ett IoT Edge-modulprojekt som baseras på C SDK med hjälp av Visual Studio och Azure IoT Edge Tools-tillägget. När du har skapat en projektmall lägger du till ny kod så att modulen filtrerar bort meddelanden baserat på deras rapporterade egenskaper.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Skapa en C-lösningsmall som du kan anpassa med din egen kod.

1. Starta Visual Studio 2019 och välj **Skapa nytt projekt**.

2. Sök efter **IoT Edge** och välj **Azure IoT Edge (Windows amd64)** projektet. Klicka på **Nästa**.

   ![Skapa ett nytt Azure IoT Edge-projekt](./media/tutorial-c-module-windows/new-project.png)

3. Byt namn på projektet och lösningen till något beskrivande som **CTutorialApp**. Klicka på **Skapa** om du vill skapa projektet.

   ![Konfigurera ett nytt Azure IoT Edge-projekt](./media/tutorial-c-module-windows/configure-project.png)

4. Konfigurera projektet med följande värden:

   | Field | Värde |
   | ----- | ----- |
   | Välj en mall | Välj **C-modul**. |
   | Namn på modulprojekt | Ge modulen namnet **CModule**. |
   | Databas för Docker-bild | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Behållaravbildningen är förifylld från modulens projektnamnsvärde. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. <br><br> Den slutliga avbildningsplatsen ser ut så här: \<registernamn\>.azurecr.io/cmodule. |

   ![Konfigurera projektet för målenhet, modultyp och behållarregister](./media/tutorial-c-module-windows/add-application-and-module.png)

5. Välj **Lägg till** om du vill skapa projektet.

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Distributionsmanifestet delar autentiseringsuppgifterna för behållarregistret med IoT Edge-körningen. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten. Använd autentiseringsuppgifterna från avsnittet **Access-nycklar** i ditt Azure-behållarregister.

1. Öppna **filen deployment.template.json** i Utforskaren för Visual Studio-lösningen.

2. Leta reda på egenskapen **Credentials** i de $edgeAgent önskade egenskaperna. Den bör ha registeradressen automatiskt ifylld från den information du angav när du skapade projektet. Fälten användarnamn och lösenord ska innehålla variabelnamn. Ett exempel:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Öppna **.env-filen** i modullösningen. (Den är dold som standard i Lösningsutforskaren, så du kan behöva välja knappen **Visa alla filer** för att visa den.) .env-filen ska innehålla samma användarnamns- och lösenordsvariabler som du såg i filen deployment.template.json.

4. Lägg till **värdena för användarnamn** och **lösenord** från ditt Azure-behållarregister.

5. Spara ändringarna i .env-filen.

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

Standardmodulkoden tar emot meddelanden i en indatakö och skickar dem vidare via en utdatakö. Nu ska vi lägga till ytterligare kod så att modulen bearbetar meddelandena vid kanten innan du vidarebefordrar dem till IoT Hub. Uppdatera modulen så att den analyserar temperaturdata i varje meddelande och skickar bara meddelandet till IoT Hub om temperaturen överskrider ett visst tröskelvärde.

1. Data från sensorn i det här scenariot kommer i JSON-format. Om du vill filtrera meddelanden i JSON-format importerar du ett JSON-bibliotek för C. Den här självstudien använder Parson.

   1. Ladda ned [Databasen Parson GitHub](https://github.com/kgabis/parson). Kopiera **filerna parson.c** och **parson.h** till **CModule-projektet.**

   2. Öppna filen **CMakeLists.txt** i Visual Studio från mappen CModule-projekt. Längst upp i filen importerar du Parson-filerna som ett bibliotek med namnet **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Lägg `my_parson` till i listan över bibliotek i **avsnittet target_link_libraries** i filen CMakeLists.txt.

   4. Spara filen **CMakeLists.txt**.

   5. Öppna **CModule** > **main.c**. Längst ned i listan över include-satser lägger du till en ny som ska inkluderas `parson.h` för JSON-stöd:

      ```c
      #include "parson.h"
      ```

2. I **filen main.c** lägger du `temperatureThreshold` till en global variabel som anropas bredvid variabeln MessagesReceivedByInput1Queue. Variabeln anger det värde som den uppmätta temperaturen måste överstiga för att data ska skickas till IoT Hub.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Hitta `CreateMessageInstance` funktionen i main.c. Ersätt den inre if-else-satsen med följande kod som lägger till några rader med funktioner:

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

   De nya kodraderna i else-satsen lägger till en ny egenskap i meddelandet, som etiketterar meddelandet som en avisering. Den här koden etiketterar alla meddelanden som aviseringar, eftersom vi lägger till funktioner som bara skickar meddelanden till IoT Hub om de rapporterar höga temperaturer.

4. Hitta `InputQueue1Callback` funktionen och ersätt hela funktionen med följande kod. Den här funktionen implementerar själva meddelandefiltret. När ett meddelande tas emot kontrollerar det om den rapporterade temperaturen överskrider tröskelvärdet. Om ja, vidarebefordrar det meddelandet via utdatakön. Om inte, ignorerar det meddelandet.

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
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

6. Hitta `SetupCallbacksForModule` funktionen. Ersätt funktionen med följande kod som lägger till en **else if-sats** för att kontrollera om modultvillingen har uppdaterats.

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

8. Öppna **filen deployment.template.json.**

9. Lägg till CModule-modultvillingen till distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet `moduleContent` efter `$edgeHub`-modultvillingen:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Lägga till CModule-tvilling till distributionsmall](./media/tutorial-c-module-windows/module-twin.png)

10. Spara filen **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Skapa och tryck på din modul

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod i **CModule** för att filtrera bort meddelanden där den rapporterade maskintemperaturen ligger under det acceptabla tröskelvärdet. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

1. Använd följande kommando för att logga in på Docker på utvecklingsmaskinen. Logga in med användarnamn, lösenord och inloggningsserver från ditt Azure-behållarregister. Du kan hämta dessa värden från avsnittet **Access-nycklar** i registret i Azure-portalen.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du kan få en säkerhetsvarning `--password-stdin`som rekommenderar användning av . Även om bästa praxis rekommenderas för produktionsscenarier, ligger den utanför den här självstudiens omfattning. Mer information finns i [inloggningsreferensen för docker.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

2. Högerklicka på det projektnamn som du vill skapa i Utforskaren för Visual Studio-lösningen. Standardnamnet är **AzureIotEdgeApp1** och eftersom du skapar en Windows-modul ska tillägget vara **Windows.Amd64**.

3. Välj **Bygg och tryck på IoT Edge-moduler**.

   Kommandot Bygg och push startar tre åtgärder. Först skapas en ny mapp i lösningen som kallas **config** som innehåller hela distributionsmanifestet, inbyggt av information i distributionsmallen och andra lösningsfiler. För det `docker build` andra körs den för att skapa behållaravbildningen baserat på lämplig dockerfile för din målarkitektur. Sedan körs `docker push` det för att skicka avbildningsdatabasen till behållarregistret.

## <a name="deploy-modules-to-device"></a>Distribuera moduler till enheten

Använd Visual Studio-molnutforskaren och Azure IoT Edge Tools-tillägget för att distribuera modulprojektet till din IoT Edge-enhet. Du har redan ett distributionsmanifest förberett för ditt scenario, **filen deployment.json** i konfigurationsmappen. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

Kontrollera att din IoT Edge-enhet är igång.

1. Expandera resurserna i Visual Studio-molnutforskaren för att se listan över IoT-enheter.

2. Högerklicka på namnet på den IoT Edge-enhet som du vill ta emot distributionen.

3. Välj **Skapa distribution**.

4. I utforskaren väljer du filen **deployment.windows-amd64** i konfigurationsmappen för din lösning.

5. Uppdatera molnutforskaren för att se de distribuerade modulerna som anges under enheten.

## <a name="view-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas.

Du kan använda tillägget IoT Edge Tools för att visa meddelanden när de anländer till din IoT Hub.

1. I Visual Studio-molnutforskaren väljer du namnet på din IoT Edge-enhet.

2. Välj Starta övervakning **inbyggd händelseslutpunkt i**listan **Åtgärder** .

3. Visa meddelanden som kommer till din IoT Hub. Det kan ta ett tag innan meddelandena kommer fram, eftersom IoT Edge-enheten måste ta emot sin nya distribution och starta alla moduler. Sedan väntar de ändringar vi gjorde i CModule-koden tills maskintemperaturen når 25 grader innan du skickar meddelanden. Meddelandet läggs också till **i** alla meddelanden som når det temperaturtröskeln.

   ![Visa meddelanden som anländer till IoT Hub](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Redigera modultvillingen

Vi använde CModule modultvillingen för att ställa in temperaturtröskeln till 25 grader. Du kan använda modultvillingen för att ändra funktionaliteten utan att behöva uppdatera modulkoden.

1. Öppna filen **deployment.windows-amd64.json** i Visual Studio. (Inte filen deployment.template. Om distributionsmanifestet inte visas i konfigurationsfilen i lösningsutforskaren markerar du ikonen **Visa alla filer** i explorer-verktygsfältet.)

2. Hitta CModule twin och ändra värdet på **temperaturSvarvningsparametern** till en ny temperatur 5 grader till 10 grader högre än den senaste rapporterade temperaturen.

3. Spara filen **deployment.windows-amd64.json.**

4. Följ distributionsstegen igen för att tillämpa det uppdaterade distributionsmanifestet på enheten.

5. Övervaka inkommande meddelanden från enheten till molnet. Du bör se meddelandena sluta tills den nya temperaturtröskeln har uppnåtts.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och De Azure-resurser som du använde i den här artikeln för att undvika avgifter.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul med kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att bygga egna moduler kan du läsa mer om hur du [utvecklar egna IoT Edge-moduler](module-development.md) eller hur du [utvecklar moduler med Visual Studio](how-to-visual-studio-develop-module.md). Exempel på IoT Edge-moduler, inklusive den simulerade temperaturmodulen, finns i [IoT Edge-modulprover](https://github.com/Azure/iotedge/tree/master/edge-modules) och [IoT C SDK-exempel](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples).

Du kan fortsätta med nästa självstudier för att lära dig hur Azure IoT Edge kan hjälpa dig att distribuera Azure-molntjänster för att bearbeta och analysera data på gränsen.

> [!div class="nextstepaction"]
> [Funktioner](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
