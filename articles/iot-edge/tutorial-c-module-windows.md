---
title: Självstudien utveckla C-modulen för Windows - Azure IoT Edge | Microsoft Docs
description: Den här självstudien visar hur du skapar en IoT Edge-modul med C-koden och distribuerar den till en Windows-enhet med IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 93910fd0baeace9da474073960dbdb83251a1a63
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576560"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Självstudier: Utveckla en C IoT Edge-modul för Windows-enheter

Använd Visual Studio för att utveckla C-koden och distribuera den till en Windows-enhet som kör Azure IoT Edge. 

Du kan använda Azure IoT Edge-moduler för att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. I den här guiden får du lära dig att:    

> [!div class="checklist"]
> * Använd Visual Studio för att skapa en IoT Edge-modul som baseras på .NET Core 2.1 SDK.
> * Använda Visual Studio och Docker för att skapa en Docker-avbildning och publicera den till registret.
> * distribuera modulen till din IoT Edge-enhet
> * visa genererade data.

IoT Edge-modulen du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys vid kanten är användbar när du vill minska mängden data som skickas till och lagras i molnet. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Lösningen omfattning

Den här kursen visar hur du utvecklar en modul i **C** med **Visual Studio 2017**, och hur du distribuerar det till en **Windows-enhet**. Om du utvecklar moduler för Linux-enheter kan du gå till [utveckla en C IoT Edge-modul för Linux enheter](tutorial-c-module.md) i stället. 

Använd följande tabell för att förstå dina alternativ för utveckling och distribution C-moduler till Windows-enheter: 

| C | Visual Studio-koden | Visual Studio 2017 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![Utveckla C-moduler för WinAMD64 i Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du påbörjar den här självstudiekursen ska du har gått igenom den tidigare självstudiekursen för att ställa in din utvecklingsmiljö för utveckling av Windows-behållare: [Utveckla IoT Edge-moduler för Windows-enheter](tutorial-develop-for-windows.md). När du har slutfört självstudien bör du ha följande krav på plats: 

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En [Windows-enhet som kör Azure IoT Edge](quickstart.md).
* Ett behållarregister, till exempel [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio?view=vs-2017), version 15.7 eller högre, som har konfigurerats med den [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) tillägget.
* [Docker CE](https://docs.docker.com/install/) konfigurerats för att köra Windows-behållare.
* Azure IoT SDK för C. 

## <a name="create-a-module-project"></a>Skapa en modul-projekt

Följande steg skapar en IoT Edge-modulen projekt som baseras på .NET Core 2.0 SDK med hjälp av Visual Studio och Azure IoT Edge-Tools-tillägget. När du har skapat en projektmall kan du lägga till ny kod så att modulen filtrerar ut meddelanden baserat på deras rapporterade egenskaper. 

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Skapa en C-lösningsmall som du kan anpassa med din egen kod.

1. Köra Visual Studio som administratör.

2. Välj **Arkiv** > **Nytt** > **Projekt**. 

3. I fönstret nytt projekt väljer du den **Azure IoT** programprojektet typ och väljer den **Azure IoT Edge** projekt. Byt namn på projektet och lösningen till något beskrivande liknande **CTutorialApp**. Klicka på **OK** för att skapa projektet. 

   ![Skapa ett nytt projekt i Azure IoT Edge](./media/tutorial-c-module-windows/new-project.png)

4. Konfigurera ditt projekt i IoT Edge-programmet och modulfönstret, med följande värden: 

   | Fält | Värde |
   | ----- | ----- |
   | Programplattform | Avmarkera **Linux Amd64**, och kontrollera **WindowsAmd64**. |
   | Välj en mall | Välj **C modulen**. | 
   | Modulen projektnamn | Ge modulen namnet **CModule**. | 
   | Lagringsplatsen för docker-avbildningen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Behållaravbildningen innehåller redan från modulen Projekt namn-värde. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. <br><br> Den slutliga avbildningsplatsen ser ut så här: \<registernamn\>.azurecr.io/cmodule. |

   ![Konfigurera ditt projekt för målenhet och Modultyp behållarregister](./media/tutorial-c-module-windows/add-application-and-module.png)

5. Välj **OK** att tillämpa ändringarna. 

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Distribution av manifestet delar autentiseringsuppgifterna för behållarregistret med IoT Edge-körningen. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten. Använd autentiseringsuppgifter från den **åtkomstnycklar** avsnitt av Azure container registry. 

1. I Visual Studio solution explorer, öppna den **deployment.template.json** fil. 

2. Hitta den **registryCredentials** -egenskapen i $edgeAgent önskade egenskaper. 

3. Uppdatera egenskapen med dina autentiseringsuppgifter efter det här formatet: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }

4. Save the deployment.template.json file. 

### Update the module with custom code

The default module code receives messages on an input queue and passes them along through an output queue. Let's add some additional code so that the module processes the messages at the edge before forwarding them to IoT Hub. Update the module so that it analyzes the temperature data in each message, and only sends the message to IoT Hub if the temperature exceeds a certain threshold. 


1. The data from the sensor in this scenario comes in JSON format. To filter messages in JSON format, import a JSON library for C. This tutorial uses Parson.

   1. Download the [Parson GitHub repository](https://github.com/kgabis/parson). Copy the **parson.c** and **parson.h** files into the **CModule** project.

   2. In Visual Studio, open the **CMakeLists.txt** file from the CModule project folder. At the top of the file, import the Parson files as a library called **my_parson**.

      ```
      add_library(my_parson        parson.c        parson.h    )
      ```

   3. Add **my_parson** to the list of libraries in the **target_link_libraries** section of the CMakeLists.txt file.

   4. Save the **CMakeLists.txt** file.

   5. Open **CModule** > **main.c**. At the bottom of the list of include statements, add a new one to include `parson.h` for JSON support:

      ```c
      #include "parson.h"
      ```

2.  I den **main.c** Lägg till en global variabel som kallas `temperatureThreshold` bredvid messagesReceivedByInput1Queue variabeln. Variabeln anger det värde som den uppmätta temperaturen måste överstiga för att data ska skickas till IoT Hub.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Hitta den `CreateMessageInstance` funktionen i main.c. Ersätt den inre if-else-instruktionen med följande kod som lägger till några rader funktioner: 

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

4. Hitta den `InputQueue1Callback` fungera, och Ersätt hela funktionen med följande kod. Den här funktionen implementerar själva meddelandefiltret. När ett meddelande tas emot, kontrollerar den om den rapporterade temperaturen överstiger tröskelvärdet. Om Ja, vidarebefordrar meddelandet via den utgående kön. Annars kan du sedan meddelandet ignoreras. 

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

6. Hitta den `SetupCallbacksForModule` funktion. Ersätt funktionen med följande kod som lägger till en **annars om** -uttrycket för att kontrollera om modultvillingen har uppdaterats. 

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

7. Spara filen main.c.

8. Öppna den **deployment.template.json** fil. 

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

## <a name="build-and-push-your-module"></a>Skapa och skicka din modul

I det föregående avsnittet du skapat en IoT Edge-lösning och lagt till kod till den **CModule** att filtrera bort meddelanden där rapporterade datorn temperaturen är under tröskelvärdet för godkända. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister. 

1. Använd följande kommando för att logga in på Docker på din utvecklingsdator. Logga in med användarnamn, lösenord och inloggningsserver från Azure container registry. Du kan hämta dessa värden från den **åtkomstnycklar** avsnitt på ditt register i Azure-portalen.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du får en säkerhetsvarning som rekommenderar användning av `--password-stdin`. Även om den bästa metoden rekommenderas för produktionsscenarier, ligger utanför omfånget för den här självstudien. Mer information finns i den [docker-inloggning](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referens.

2. I Visual Studio solution explorer, högerklicka på projektnamnet som du vill skapa. Standardnamnet är **AzureIotEdgeApp1** och eftersom du skapar en Windows-modul, tillägget bör vara **Windows.Amd64**. 

3. Välj **bygga och Push IoT Edge-moduler**. 

   Versions- och push-kommando startar tre åtgärder. Först skapar den en ny mapp i lösningen kallas **config** som innehåller de fullständig distribution manifest, inbyggda ut information i mallen för distribution och andra lösningsfiler. Dessutom körs `docker build` att skapa behållaravbildningen baserat på dockerfile som är lämpliga för din mål-arkitektur. Sedan körs `docker push` att skicka lagringsplatsen för avbildningen till behållarregistret. 

## <a name="deploy-modules-to-device"></a>Distribuera moduler till enhet

Använda Visual Studio cloud explorer och verktyg för Azure IoT Edge-tillägget för att distribuera projektet modulen till din IoT Edge-enhet. Du redan har ett manifest för distribution som förberedd för ditt scenario, den **deployment.json** fil i config-mappen. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

Se till att din IoT Edge-enhet är igång. 

1. I Visual Studio cloud explorer, expandera resurser för att se din lista över IoT-enheter. 

2. Högerklicka på namnet på IoT Edge-enhet som du vill ta emot distributionen. 

3. Välj **skapa distribution**.

4. I Utforskaren, Välj den **deployment.windows amd64** fil i config-mappen i din lösning. 

5. Uppdatera cloud explorer om du vill se de distribuerade moduler som visas under din enhet. 


## <a name="view-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas. 

Du kan använda tillägget IoT Edge-verktyg för att visa meddelanden när de anländer till din IoT-hubb. 

1. I Visual Studio cloud explorer, väljer du namnet på din IoT Edge-enhet. 

2. I den **åtgärder** väljer **börja övervaka D2C-meddelande**. 

3. Visa meddelanden som inkommer på din IoT-hubb. Det kan ta en stund innan meddelanden tas emot, eftersom IoT Edge-enheten måste ta emot dess ny distribution och starta alla moduler. Vi ändringar i koden CModule vänta sedan tills datorn temperaturen når 25 grader innan du skickar meddelanden. Det ger också meddelandetypen **avisering** att alla meddelanden som når gränsen temperatur. 

   ![Visa meddelanden anländer till IoT Hub](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Redigera modultvillingen

Vi använde modultvilling CModule Ange tröskelvärdet för temperatur vid 25 grader. Du kan använda modultvillingen för att ändra funktionen utan att behöva uppdatera modulen koden.

1. Visual Studio, öppna den **deployment.windows amd64.json** fil. (Inte deployment.template-fil. Om du inte ser distributionen visas i config-filen i solution explorer, Välj den **visa alla filer** ikon i explorer-verktygsfältet.)

2. Hitta den CModule läsningen och ändra värdet för den **temperatureThreshold** parametern till en ny temperatur 5 grader och 10 grader som är högre än den senaste rapporterade temperaturen. 

3. Spara den **deployment.windows amd64.json** fil.

4. Gör distributionen igen om du vill tillämpa det uppdaterade distributionen manifestet till din enhet. 

5. Övervaka inkommande meddelanden för enhet-till-moln. Du bör se meddelanden stoppa tills det nya temperatur-tröskelvärdet har uppnåtts. 

## <a name="clean-up-resources"></a>Rensa resurser 

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet. 

I annat fall kan du ta bort de lokala konfigurationerna och Azure-resurser som du använt i den här artikeln för att undvika avgifter. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul med kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att skapa egna moduler kan du läsa mer om [utveckla dina egna IoT Edge-moduler](module-development.md) eller hur du [utveckla moduler med Visual Studio](how-to-visual-studio-develop-module.md). Du kan fortsätta till nästa självstudierna för att lära dig hur Azure IoT Edge kan hjälpa dig att distribuera Azure-molntjänster för att bearbeta och analysera data på gränsen.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Maskininlärning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
