---
title: Självstudie om Azure IoT Edge med C | Microsoft Docs
description: Den här självstudien beskriver hur du skapar en IoT Edge-modul med C-kod och distribuerar den till en gränsenhet.
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 07/30/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 31560cbd4d8b4572ce930db7ffb8753f3e4a4bc0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425926"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Självstudie: Utveckla en C IoT Edge-modul och distribuera till den simulerade enheten

Du kan använda IoT Edge-moduler till att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. I den här guiden får du lära dig att:    

> [!div class="checklist"]
> * Använda Visual Studio Code för att skapa en IoT Edge-modul i C
> * Använda Visual Studio Code och Docker för att skapa en Docker-avbildning och publicera den till ett containerregister 
> * Distribuera modulen till din IoT Edge-enhet
> * Visa genererade data


IoT Edge-modulen som du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys är användbar för att minska mängden data som kommuniceras till och lagras i molnet. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Nödvändiga komponenter

En Azure IoT Edge-enhet:

* Du kan använda utvecklingsdatorn eller en virtuell dator som en gränsenhet genom att följa stegen i snabbstarten för [Linux-](quickstart-linux.md) eller [Windows-enheter](quickstart.md).

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på standardnivå i Azure. 

Utvecklingsresurser:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [C/C++-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) för Visual Studio Code.
* [Azure IoT Edge-tillägg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) för Visual Studio Code.
* [Docker CE](https://docs.docker.com/install/). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-container-registry"></a>Skapa ett containerregister
I den här självstudien använder du Azure IoT Edge-tillägget för VS Code för att skapa en modul och skapa en **containeravbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.  

Du kan använda valfritt Docker-kompatibelt register för den här självstudien. Två populära Docker-registertjänster som finns tillgängliga i molnet är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). I den här kursen använder vi Azure Container Registry. 

Följande Azure CLI-kommando skapar ett register i en resursgrupp med namnet **IoTEdgeResources**. Ersätt **{acr_name}** med ett unikt namn för ditt register. 

   ```azurecli-interactive
   az acr create --resource-group IoTEdgeResources --name {acr_name} --sku Basic --admin-enabled true
   ```

Hämta autentiseringsuppgifterna för ditt register. 

   ```azurecli-interactive
   az acr credential show --name {acr_name}
   ```

Kopiera värdena för **Användarnamn** och ett av lösenorden. Du använder dessa värden senare i självstudien när du publicerar Docker-avbildningen till registret, samt när du lägger till autentiseringsuppgifterna för registret i Edge-körningen. 

## <a name="create-an-iot-edge-module-project"></a>Skapa ett projekt för IoT Edge-modulen
Följande steg visar hur du skapar ett IoT Edge-modulprojekt som baseras på .NET Core 2.0 med Visual Studio Code och Azure IoT Edge-tillägget.
1. I Visual Studio Code väljer du **Visa** > **Integrerad terminal** för att öppna den integrerade Visual Studio Code-terminalen.
2. Välj **Visa** > **Kommandopalett** för att öppna kommandopaletten i VS Code. 
3. Ange och kör kommandot **Azure: Logga in** i kommandopaletten och följ anvisningarna för att logga in med ditt Azure-konto. Om du redan har loggat in kan du hoppa över det här steget.
4. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) i kommandopaletten. Ange följande information i kommandopaletten för att skapa din lösning: 
   1. Välj den mapp där du vill skapa lösningen. 
   2. Ange ett namn för din lösning eller välj standardnamnet **EdgeSolution**.
   3. Välj **C Module** som mall för modulen. 
   4. Ge modulen namnet **CModule**. 
   5. Ange det Azure Container Registry som du skapade i föregående avsnitt som avbildningslagringsplats för din första modul. Ersätt **localhost:5000** med det serverinloggningsvärde som du kopierade. Den slutliga strängen ser ut så här: **\<registernamn\>.azurecr.io/cmodule**.
 
4. VS Code läser in arbetsytan för IoT Edge-lösningen. Det finns en **modules**-mapp, en **.vscode**-mapp, en mallfil för distributionsmanifestet och en **.env**-fil. Standardkoden för modulen implementeras som en pipe-modul. 

5. Om du vill filtrera meddelanden i JSON-format måste du importera ett JSON-bibliotek för C. Du kan välja valfritt JSON-bibliotek eller skriva ett eget för att parsa JSON i C-modulen. I stegen nedan används [Parson](https://github.com/kgabis/parson) som exempel.
   1. Ladda ned **parson.c** och **parson.h** från [Parson Github-lagringsplatsen](https://github.com/kgabis/parson). Kopiera och klistra in dessa två filer i mappen **CModule**.
   2. Öppna **modules** > **CModule** > **CMakeLists.txt**. Importera parson-biblioteket som my_parson genom att lägga till raderna nedan.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Lägg till `my_parson` i `target_link_libraries` i **CMakeLists.txt**.
   4. Öppna **modules** > **CModule** > **main.c**. Lägg till `parson.h` för JSON-stöd genom att lägga till koden nedan sist i include-avsnittet:

      ```c
      #include "parson.h"
      ```

6. Lägg till en global `temperatureThreshold`-variabel efter include-avsnittet. Variabeln anger det värde som den uppmätta temperaturen måste överstiga för att data ska skickas till IoT Hub. 

    ```c
    static double temperatureThreshold = 25;
    ```

7. Ersätt hela `CreateMessageInstance`-funktionen med koden nedan. Den här funktionen allokerar en kontext för återanropet. 

    ```c
    static MESSAGE_INSTANCE* CreateMessageInstance(IOTHUB_MESSAGE_HANDLE message)
    {
        MESSAGE_INSTANCE* messageInstance = (MESSAGE_INSTANCE*)malloc(sizeof(MESSAGE_INSTANCE));
        if (NULL == messageInstance)
        {
            printf("Failed allocating 'MESSAGE_INSTANCE' for pipelined message\r\n");
        }
        else
        {
            memset(messageInstance, 0, sizeof(*messageInstance));

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
        }

        return messageInstance;
    }
    ```

8. Ersätt hela `InputQueue1Callback`-funktionen med koden nedan. Den här funktionen implementerar själva meddelandefiltret. 

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

9. Lägg till en `moduleTwinCallback`-funktion. Metoden tar emot uppdateringar av önskade egenskaper från modultvillingen och uppdaterar variabeln **temperatureThreshold** som ska matchas. Alla moduler har en egen modultvilling, vilket innebär att du kan konfigurera den kod som körs i en modul direkt från molnet.

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

10. Lägg till ett nytt återanrop för `moduleTwinCallback` i funktionen `SetupCallbacksForModule`. Ersätt hela `SetupCallbacksForModule`-funktionen med koden nedan.

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

11. Spara filen.

## <a name="build-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod till CModule som filtrerar ut meddelanden om att temperaturen för den rapporterade datorn ligger under det godkända tröskelvärdet. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister. 

1. Logga in på Docker genom att ange följande kommando i den integrerade terminalen för Visual Studio Code så att du kan push-överföra modulavbildningen till ACR: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Använd användarnamnet, lösenordet och inloggningsservern som du kopierade från Azure Container Registry i det första avsnittet. Eller hämta dem igen från avsnittet **Åtkomstnycklar** i ditt register i Azure Portal.

2. I VS Code-utforskaren öppnar du filen **deployment.template.json** i arbetsytan för IoT Edge-lösningen. Den här filen instruerar `$edgeAgent` att distribuera två moduler: **tempSensor** och **CModule**. Värdet `CModule.image` är inställt på en Linux amd64-version av avbildningen. Läs mer om distributionsmanifest i avsnittet om att [förstå hur IoT Edge-moduler kan användas, konfigureras och återanvändas](module-composition.md).

3. I filen **deployment.template.json** finns det ett avsnitt som heter **registryCredentials** och som lagrar dina autentiseringsuppgifter för Docker-registret. De faktiska paren för användarnamn och lösenord lagras i env-filen, som git ignorerar.

4. Lägg till CModule-modultvillingen till distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet `moduleContent` efter `$edgeHub`-modultvillingen: 
    ```json
        "CModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Spara filen.
5. Högerklicka på filen **deployment.template.json** och välj **Skapa IoT Edge-lösning** i VS Code-utforskaren. 

När du ger Visual Studio Code kommando att skapa din lösning hämtar den först information i distributionsmallen och genererar en `deployment.json`-fil i en ny **config**-mapp. Sedan körs två kommandon i en integrerad terminal: `docker build` och `docker push`. Dessa två kommandon skapar koden, lägger `CModule.dll` i behållare och push-överför till det containerregister som du angav när du initierade lösningen. 

Den fullständiga adressen med tagg för containeravbildningen finns i den integrerade VS Code-terminalen. Avbildningsadressen skapas utifrån information i filen `module.json` med formatet **\<lagringsplats\>:\<version\>-\<plattform\>**. I den här självstudien bör den se ut så här: **registryname.azurecr.io/cmodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Distribuera och kör lösningen

1. Konfigurera Azure IoT Toolkit-tillägget med anslutningssträngen för din IoT-hubb: 
    1. Öppna VS Code-utforskaren genom att välja **Visa** > **Utforskaren**. 
    2. I utforskaren klickar du på **AZURE IOT HUB-ENHETER** och sedan på **...**. Klicka på **Select IoT Hub** (Välj IoT Hub). Följ instruktionerna för att logga in på Azure-kontot och välja din IoT-hubb. 
       Observera att du även kan konfigurera genom att klicka på **Set IoT Hub Connection String** (Ange anslutningssträng för IoT-hubb). Ange anslutningssträngen för den IoT-hubb som din IoT Edge-enhet ansluter till i popup-fönstret.

2. I Azure IoT Hub Devices-utforskaren högerklickar du på din IoT Edge-enhet och klickar sedan på **Create Deployment for IoT Edge device** (Skapa distribution för IoT Edge-enhet). Välj filen **deployment.json** i mappen **config** och klicka sedan på **Select Edge Deployment Manifest** (Välj distributionsmanifest för Edge).

3. Klicka på uppdateringsknappen. Den nya **CModule**-modulen visas och körs tillsammans med **TempSensor**-modulen och **$edgeAgent** och **$edgeHub**. 

## <a name="view-generated-data"></a>Visa genererade data

1. Om du vill övervaka data som inkommer till IoT Hub klickar du på **...** och väljer **Starta övervakning av D2C-meddelanden**.
2. Om du vill övervaka D2C-meddelandet för en specifik enhet högerklickar du på enheten i listan och väljer **Starta övervakning av D2C-meddelanden**.
3. Om du vill stoppa dataövervakningen kör du kommandot **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: Sluta övervaka D2C-meddelande) i kommandopaletten. 
4. Om du vill visa eller uppdatera modultvillingen högerklickar du på modulen i listan och väljer **Redigera modultvilling**. Om du vill uppdatera modultvillingen sparar du tvilling-JSON-filen och högerklickar på redigeringsområdet och väljer **Uppdatera modultvilling**.
5. Om du vill visa Docker-loggar kan du installera [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) för VS Code och se moduler som körs lokalt i Docker-utforskaren. I kontextmenyn klickar du på **Visa loggar** för att visa i den integrerade terminalen.
 
## <a name="clean-up-resources"></a>Rensa resurser 

Om du fortsätter till nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du redan har skapat och återanvända dem.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som skapats i den här artikeln om du vill undvika kostnader. 

> [!IMPORTANT]
> Det går inte att ångra borttagningen av Azure-resursgrupper. När resursgruppen och alla resurser som ingår i den tas bort är de borta permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat IoT Hub:en inuti en befintlig resursgrupp som innehåller resurser som du vill behålla, ta bara bort själva IoT Hub-resursen i stället för att ta bort resursgruppen.
>

Om du endast vill ta bort körningen av IoT Hub kan du använda följande kommando med namnet på hubben och resursgruppens namn:

```azurecli-interactive
az iot hub delete --name {hub_name} --resource-group IoTEdgeResources
```


Ta bort hela resursgruppen med namnet:

   ```azurecli-interactive
   az group delete --name IoTEdgeResources 
   ```



## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att skapa egna moduler kan du läsa mer om hur du [utvecklar en C-modul med Azure IoT Edge för Visual Studio Code](how-to-develop-c-module.md). Du kan fortsätta med någon av följande självstudier om du vill veta mer hur Azure IoT Edge kan hjälpa dig att omvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Lagra data på gränsen med SQL Server-databaser](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
