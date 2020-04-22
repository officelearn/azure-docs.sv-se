---
title: Azure IoT-enheten SDK för C | Microsoft-dokument
description: Kom igång med Azure IoT-enheten SDK för C och lär dig hur du skapar enhetsappar som kommunicerar med en IoT-hubb.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: b9b27bb142cb729536a3b7a561ed8b8ff5e0ccf5
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731307"
---
# <a name="azure-iot-device-sdk-for-c"></a>Azure IoT-enhet SDK för C

**Azure IoT-enheten SDK** är en uppsättning bibliotek som utformats för att förenkla processen att skicka meddelanden till och ta emot meddelanden från **Azure IoT** Hub-tjänsten. Det finns olika varianter av SDK, var och en inriktning på en viss plattform, men den här artikeln beskriver **Azure IoT-enheten SDK för C**.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Azure IoT-enheten SDK för C är skriven i ANSI C (C99) för att maximera portabiliteten. Den här funktionen gör biblioteken väl lämpade att fungera på flera plattformar och enheter, särskilt där minimera disk och minne fotavtryck är en prioritet.

Det finns ett brett utbud av plattformar där SDK har testats (se [Azure Certified for IoT-enhetskatalogen](https://catalog.azureiotsolutions.com/) för mer information). Även om den här artikeln innehåller genomgångar av exempelkod som körs på Windows-plattformen, är koden som beskrivs i den här artikeln identisk i olika plattformar som stöds.

Följande video innehåller en översikt över Azure IoT SDK för C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Den här artikeln introducerar dig till arkitekturen för Azure IoT-enheten SDK för C. Den visar hur du initierar enhetsbiblioteket, skickar data till IoT Hub och får meddelanden från det. Informationen i den här artikeln bör vara tillräckligt för att komma igång med SDK, men ger också pekare till ytterligare information om biblioteken.

## <a name="sdk-architecture"></a>SDK-arkitektur

Du hittar [**Azure IoT-enheten SDK för C**](https://github.com/Azure/azure-iot-sdk-c) GitHub-databasen och visa information om API i [C API-referensen](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

Den senaste versionen av biblioteken finns i **databasens huvudgren:**

  ![Skärmbild av databasens huvudgren](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* Kärnimplementeringen av SDK finns i **\_iothub-klientmappen** som innehåller implementeringen av det lägsta API-lagret i SDK: **IoTHubClient-biblioteket.** **IoTHubClient-biblioteket** innehåller API:er som implementerar råmeddelanden för att skicka meddelanden till IoT Hub och ta emot meddelanden från IoT Hub. När du använder det här biblioteket ansvarar du för att implementera meddelandeseriering, men andra detaljer om kommunikation med IoT Hub hanteras åt dig.

* **Serializer-mappen** innehåller hjälpfunktioner och exempel som visar hur du serialiserar data innan du skickar till Azure IoT Hub med hjälp av klientbiblioteket. Användningen av serialiseraren är inte obligatorisk och tillhandahålls som en bekvämlighet. Om du vill använda **serialiseringsbiblioteket** definierar du en modell som anger vilka data som ska skickas till IoT Hub och de meddelanden som du förväntar dig att ta emot från det. När modellen har definierats ger SDK dig en API-yta som gör att du enkelt kan arbeta med meddelanden från enhet till moln och från molnet till enheten utan att behöva oroa dig för serialiseringsinformationen. Biblioteket är beroende av andra bibliotek med öppen källkod som implementerar transport med hjälp av protokoll som MQTT och AMQP.

* **IoTHubClient-biblioteket** är beroende av andra bibliotek med öppen källkod:

  * [Det delade verktygsbiblioteket i Azure C,](https://github.com/Azure/azure-c-shared-utility) som tillhandahåller vanliga funktioner för grundläggande uppgifter (till exempel strängar, listmanipulering och IO) som behövs i flera Azure-relaterade C SDK:er.

  * [Azure uAMQP-biblioteket,](https://github.com/Azure/azure-uamqp-c) som är en implementering på klientsidan av AMQP som är optimerad för resursbegränsade enheter.

  * [Azure uMQTT-biblioteket,](https://github.com/Azure/azure-umqtt-c) som är ett allmänt bibliotek som implementerar MQTT-protokollet och optimeras för resursbegränsade enheter.

Användning av dessa bibliotek är lättare att förstå genom att titta på exempelkod. I följande avsnitt går du igenom flera av de exempelprogram som ingår i SDK. Denna genomgång bör ge dig en bra känsla för de olika funktionerna i de arkitektoniska lagren av SDK och en introduktion till hur API: er fungerar.

## <a name="before-you-run-the-samples"></a>Innan du kör proverna

Innan du kan köra exemplen i Azure IoT-enheten SDK för C måste du [skapa en instans av IoT Hub-tjänsten](iot-hub-create-through-portal.md) i din Azure-prenumeration. Slutför sedan följande uppgifter:

* Förbereda utvecklingsmiljön
* Hämta enhetsuppgifter.

### <a name="prepare-your-development-environment"></a>Förbereda utvecklingsmiljön

Paket tillhandahålls för vanliga plattformar (som NuGet för Windows eller apt_get för Debian och Ubuntu) och exemplen använder dessa paket när de är tillgängliga. I vissa fall måste du kompilera SDK för eller på enheten. Om du behöver kompilera SDK läser du [Förbereda din utvecklingsmiljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) i GitHub-databasen.

Hämta en kopia av SDK från GitHub om du vill hämta exempelprogramkoden. Hämta ditt exemplar av källan från **huvudgrenen** i [GitHub-databasen](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Hämta enhetsautentiseringsuppgifterna

Nu när du har exempel källkoden, nästa sak att göra är att få en uppsättning enhetsautentiseringsuppgifter. För att en enhet ska kunna komma åt en IoT-hubb måste du först lägga till enheten i IoT Hub-identitetsregistret. När du lägger till enheten får du en uppsättning enhetsautentiseringsuppgifter som du behöver för att enheten ska kunna ansluta till IoT-hubben. Exempelprogrammen som beskrivs i nästa avsnitt förväntar sig att dessa autentiseringsuppgifter i form av en **enhetsanslutningssträng**.

Det finns flera verktyg med öppen källkod som hjälper dig att hantera din IoT-hubb.

* Ett Windows-program som heter [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer).

* Ett Visual Studio-kodtillägg över flera plattformar som kallas [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

* En Python CLI som inte är plattformsoberoende kallas [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

Den här självstudien använder det grafiska *verktyget för utforskare* av enheten. Du kan använda *Azure IoT Tools för VS-kod* om du utvecklar i VS-kod. Du kan också använda *IoT-tillägget för Azure CLI 2.0-verktyget* om du föredrar att använda ett CLI-verktyg.

Verktyget för enhetsutforskaren använder Azure IoT-tjänstbiblioteken för att utföra olika funktioner på IoT Hub, inklusive att lägga till enheter. Om du använder verktyget för enhetsutforskare för att lägga till en enhet får du en anslutningssträng för enheten. Du behöver den här anslutningssträngen för att köra exempelprogrammen.

Om du inte är bekant med verktyget för enhetsutforskare beskriver följande procedur hur du använder den för att lägga till en enhet och hämta en enhetsanslutningssträng.

1. Information om hur du installerar verktyget för enhetsutforskaren finns i [Så här använder du Enhetsutforskaren för IoT Hub-enheter](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

1. När du kör programmet visas det här gränssnittet:

   ![Skärmbild av Twin för Enhetsutforskare](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Ange **anslutningssträngen för IoT Hub** i det första fältet och klicka på **Uppdatera**. Det här steget konfigurerar verktyget så att det kan kommunicera med IoT Hub. 

**Anslutningssträngen** finns under **IoT Hub Service** > **Settings** > **Shared Access Policy** > **iothubowner**.

1. När anslutningssträngen för IoT Hub är konfigurerad klickar du på fliken **Hantering:**

   ![Skärmbild av Device Explorer Twin / Management](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

På den här fliken hanterar du de enheter som är registrerade i din IoT-hubb.

1. Du skapar en enhet genom att klicka på knappen **Skapa.** En dialogruta visas med en uppsättning förifyllda nycklar (primära och sekundära). Ange ett **enhets-ID** och klicka sedan på **Skapa**.

   ![Skapa skärmbild av enhet](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. När enheten skapas uppdateras listan Enheter med alla registrerade enheter, inklusive den du just skapade. Om du högerklickar på den nya enheten visas den här menyn:

   ![Högerklicksresultat för Enhetsutforskaren](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. Om du väljer **Kopiera anslutningssträng för vald enhet**kopieras enhetsanslutningssträngen till Urklipp. Behåll en kopia av enhetens anslutningssträng. Du behöver det när du kör de exempelprogram som beskrivs i följande avsnitt.

När du har slutfört stegen ovan är du redo att börja köra en kod. De flesta exempel har en konstant högst upp i huvudkällfilen som gör att du kan ange en anslutningssträng. Motsvarande rad från **\_iothub_client-exempel\_iothub_convenience_sample** program visas till exempel på följande sätt.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Använda IoTHubClient-biblioteket

I **\_iothub-klientmappen** i [azure-iot-sdk-c-databasen](https://github.com/azure/azure-iot-sdk-c) finns en **exempelmapp** som innehåller ett program som kallas **iothub-klientexempel\_\_\_mqtt**.

Windows-versionen av **iothub_client\_exempel\_iothub_convenience_sample** program innehåller följande Visual Studio-lösning:

  ![Utforskaren för Visual Studio-lösning](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Om Visual Studio ber dig att rikta om projektet till den senaste versionen godkänner du uppmaningen.

Den här lösningen innehåller ett enda projekt. Det finns fyra NuGet-paket installerade i den här lösningen:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

Du behöver alltid **Microsoft.Azure.C.SharedUtility-paketet** när du arbetar med SDK. Det här exemplet använder MQTT-protokollet, därför måste du inkludera **paketen Microsoft.Azure.umqtt** och **Microsoft.Azure.IoTHub.MqttTransport** (det finns likvärdiga paket för AMQP och HTTPS). Eftersom exemplet använder **IoTHubClient-biblioteket** måste du även inkludera **Microsoft.Azure.IoTHub.IoTHubClient-paketet** i din lösning.

Du hittar implementeringen för exempelprogrammet i **\_iothub_client\_exempel iothub_convenience_sample** källfilen.

I följande steg används det här exempelprogrammet för att gå igenom vad som krävs för att använda **IoTHubClient-biblioteket.**

### <a name="initialize-the-library"></a>Initiera biblioteket

> [!NOTE]
> Innan du börjar arbeta med biblioteken kan du behöva utföra en viss plattformsspecifik initiering. Om du till exempel planerar att använda AMQP på Linux måste du initiera OpenSSL-biblioteket. Exemplen i [GitHub-databasen](https://github.com/Azure/azure-iot-sdk-c) anropar verktygsfunktionsplattformen **\_init** när klienten startar och anropar **plattformsininitfunktionen\_** innan du avslutar. Dessa funktioner deklareras i filen platform.h.These functions are declared in the platform.h header file. Undersök definitionerna av dessa funktioner för målplattformen i [databasen](https://github.com/Azure/azure-iot-sdk-c) för att avgöra om du behöver inkludera någon plattformsspecifik initieringskod i klienten.

Om du vill börja arbeta med biblioteken allokerar du först en IoT Hub-klientreferens:

```c
if ((iotHubClientHandle = 
  IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

Du skickar en kopia av enhetsanslutningssträngen som du fick från enhetsutforskaren till den här funktionen. Du anger också det kommunikationsprotokoll som ska användas. I det här exemplet används MQTT, men AMQP och HTTPS är också alternativ.

När du har en giltig **IOTHUB-klientreferens\_\_** kan du börja ringa API:erna för att skicka och ta emot meddelanden till och från IoT Hub.

### <a name="send-messages"></a>Skicka meddelanden

Exempelprogrammet konfigurerar en loop för att skicka meddelanden till din IoT-hubb. Följande utdrag:

- Skapar ett meddelande.
- Lägger till en egenskap i meddelandet.
- Skickar ett meddelande.

Skapa först ett meddelande:

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

Varje gång du skickar ett meddelande anger du en referens till en motringningsfunktion som anropas när data skickas. I det här exemplet kallas motringningsfunktionen **SendConfirmationCallback**. Följande kodavsnitt visar den här motringningsfunktionen:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Observera anropet till **funktionen IoTHubMessage\_Destroy** när du är klar med meddelandet. Den här funktionen frigör de resurser som allokerats när du skapade meddelandet.

### <a name="receive-messages"></a>Ta emot meddelanden

Att ta emot ett meddelande är en asynkron åtgärd. Först registrerar du motringningen för att anropa när enheten tar emot ett meddelande:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
    ...
```

Den sista parametern är en ogiltig pekare till vad du vill. I exemplet är det en pekare till ett heltal, men det kan vara en pekare till en mer komplex datastruktur. Med den här parametern kan motringningsfunktionen fungera på delat tillstånd med anroparen av den här funktionen.

När enheten tar emot ett meddelande anropas den registrerade motringningsfunktionen. Den här motringningsfunktionen hämtar:

* Meddelande-ID och korrelations-ID från meddelandet.
* Meddelandets innehåll.
* Alla anpassade egenskaper från meddelandet.

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Använd funktionen **IoTHubMessage\_GetByteArray** för att hämta meddelandet, som i det här exemplet är en sträng.

### <a name="uninitialize-the-library"></a>Uninitialize biblioteket

När du är klar med att skicka händelser och ta emot meddelanden kan du uninitialize IoT-biblioteket. Det gör du genom att utfärda följande funktionsanrop:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Det här anropet frigör de resurser som tidigare allokerats av **funktionen IoTHubClient\_CreateFromConnectionString.**

Som du kan se är det enkelt att skicka och ta emot meddelanden med **IoTHubClient-biblioteket.** Biblioteket hanterar information om kommunikation med IoT Hub, inklusive vilket protokoll som ska användas (ur utvecklarens perspektiv är detta ett enkelt konfigurationsalternativ).

**IoTHubClient-biblioteket** ger också exakt kontroll över hur du serialiserar de data som enheten skickar till IoT Hub. I vissa fall är denna kontrollnivå en fördel, men i andra är det en implementeringsdetalj som du inte vill bry dig om. Om så är fallet kan du överväga att använda **serialiserarbiblioteket,** som beskrivs i nästa avsnitt.

## <a name="use-the-serializer-library"></a>Använda serialiserarbiblioteket

Begreppsmässigt sitter **serializerbiblioteket** ovanpå **IoTHubClient-biblioteket** i SDK. Den använder **IoTHubClient-biblioteket** för den underliggande kommunikationen med IoT Hub, men lägger till modelleringsfunktioner som tar bort bördan av att hantera meddelandeseriering från utvecklaren. Hur det här biblioteket fungerar visas bäst genom ett exempel.

Inuti **serializer** mappen i [azure-iot-sdk-c-arkivet](https://github.com/Azure/azure-iot-sdk-c), är en **exempelmapp** som innehåller ett program som kallas **simplesample\_mqtt**. Windows-versionen av det här exemplet innehåller följande Visual Studio-lösning:

  ![Visual Studio-lösning för mqtt-exempel](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Om Visual Studio ber dig att rikta om projektet till den senaste versionen godkänner du uppmaningen.

Precis som med föregående prov innehåller det här flera NuGet-paket:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.serialiserare
* Microsoft.Azure.umqtt

Du har sett de flesta av dessa paket i föregående exempel, men **Microsoft.Azure.IoTHub.Serializer** är ny. Det här paketet krävs när du använder **serialiserarbiblioteket.**

Du hittar implementeringen av exempelprogrammet i **\_iothub_client\_exempel iothub_convenience_sample** filen.

I följande avsnitt går du igenom de viktigaste delarna i det här exemplet.

### <a name="initialize-the-library"></a>Initiera biblioteket

Om du vill börja arbeta med **serialiseringsbiblioteket** anropar du initierings-API:erna:

```c
if (serializer_init(NULL) != SERIALIZER_OK)
{
    (void)printf("Failed on serializer_init\r\n");
}
else
{
    IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol);
    srand((unsigned int)time(NULL));
    int avgWindSpeed = 10;

    if (iotHubClientHandle == NULL)
    {
        (void)printf("Failed on IoTHubClient_LL_Create\r\n");
    }
    else
    {
        ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
        if (myWeather == NULL)
        {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
        }
        else
        {
...
```

Anropet till **serializer\_init-funktionen** är ett engångsanrop och initierar det underliggande biblioteket. Sedan anropar du **funktionen IoTHubClient\_\_LL CreateFromConnectionString,** vilket är samma API som i **IoTHubClient-exemplet.** Det här anropet anger enhetens anslutningssträng (det här anropet är också där du väljer det protokoll du vill använda). Det här exemplet använder MQTT som transport, men kan använda AMQP eller HTTPS.

Anropa slutligen **funktionen\_\_SKAPA MODELLINSTANS.** **WeatherStation** är namnet på modellen och **ContosoAnemometer** är namnet på modellen. När modellinstansen har skapats kan du använda den för att börja skicka och ta emot meddelanden. Det är dock viktigt att förstå vad en modell är.

### <a name="define-the-model"></a>Definiera modellen

En modell i **serializerbiblioteket** definierar de meddelanden som enheten kan skicka till IoT Hub och meddelandena, så kallade *åtgärder* på modelleringsspråket, som den kan ta emot. Du definierar en modell med hjälp av en uppsättning C-makron som i **iothub_client\_exempel\_iothub_convenience_sample** exempelprogram:

```c
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(int, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

**Makrona\_BEGIN NAMESPACE** och **\_END NAMESPACE** tar båda modellens namnområde som ett argument. Det förväntas att allt mellan dessa makron är definitionen av din modell eller modeller, och de datastrukturer som modellerna använder.

I det här exemplet finns det en enda modell som heter **ContosoAnemometer**. Den här modellen definierar två datadelar som enheten kan skicka till IoT Hub: **DeviceId** och **WindSpeed**. Den definierar också tre åtgärder (meddelanden) som enheten kan ta emot: **TurnFanOn,** **TurnFanOff**och **SetAirResistance**. Varje dataelement har en typ och varje åtgärd har ett namn (och eventuellt en uppsättning parametrar).

De data och åtgärder som definieras i modellen definierar en API-yta som du kan använda för att skicka meddelanden till IoT Hub och svara på meddelanden som skickas till enheten. Användning av denna modell förstås bäst genom ett exempel.

### <a name="send-messages"></a>Skicka meddelanden

Modellen definierar de data som du kan skicka till IoT Hub. I det här exemplet innebär det ett av de två dataobjekt som definierats med hjälp av **WITH_DATA** makro. Det finns flera steg som krävs för att skicka **DeviceId-** och **WindSpeed-värden** till en IoT-hubb. Den första är att ange de data du vill skicka:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

Modellen som du definierade tidigare gör att du kan ange värden genom att ange medlemmar i en **struktur**. Därefter serialisera meddelandet som du vill skicka:

```c
unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, myWeather->DeviceId, myWeather->WindSpeed) != CODEFIRST_OK)
{
    (void)printf("Failed to serialize\r\n");
}
else
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
    free(destination);
}
```

Den här koden serialiserar enheten till molnet till en buffert (refererad av **mål**). Koden anropar sedan **funktionen sendMessage** för att skicka meddelandet till IoT Hub:

```c
static void sendMessage(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle == NULL)
    {
        printf("unable to create a new IoTHubMessage\r\n");
    }
    else
    {
        if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }
        IoTHubMessage_Destroy(messageHandle);
    }
    messageTrackingId++;
}
```

Den näst sista parametern **för IoTHubClient\_LL\_SendEventAsync** är en referens till en motringningsfunktion som anropas när data har skickats. Här är motringningsfunktionen i exemplet:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Den andra parametern är en pekare till användarkontext. samma pekare gick till **IoTHubClient\_LL\_SendEventAsync**. I det här fallet är sammanhanget en enkel räknare, men det kan vara vad du vill.

Det är allt som finns för att skicka meddelanden från enhet till moln. Det enda som återstår att täcka är hur man får meddelanden.

### <a name="receive-messages"></a>Ta emot meddelanden

Att ta emot ett meddelande fungerar på samma sätt som meddelanden fungerar i **IoTHubClient-biblioteket.** Först registrerar du en meddelandeåterringsfunktion:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, 
  IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

Sedan skriver du den motringningsfunktion som anropas när ett meddelande tas emot:

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = IOTHUBMESSAGE_ABANDONED;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = IOTHUBMESSAGE_ABANDONED;
        }
        else
        {
            (void)memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Denna kod är standard - det är samma för alla lösningar. Den här funktionen tar emot meddelandet och tar hand om routning det till lämplig funktion via anropet till **EXECUTE\_COMMAND**. Vilken funktion som anropas vid denna punkt beror på definitionen av åtgärderna i modellen.

När du definierar en åtgärd i modellen måste du implementera en funktion som anropas när enheten tar emot motsvarande meddelande. Om modellen till exempel definierar den här åtgärden:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Definiera en funktion med den här signaturen:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Observera hur namnet på funktionen matchar namnet på åtgärden i modellen och att parametrarna för funktionen matchar de parametrar som angetts för åtgärden. Den första parametern krävs alltid och innehåller en pekare till förekomsten av din modell.

När enheten tar emot ett meddelande som matchar signaturen anropas motsvarande funktion. Därför, bortsett från att behöva inkludera standardkoden från **IoTHubMessage,** är mottagande av meddelanden bara en fråga om att definiera en enkel funktion för varje åtgärd som definieras i din modell.

### <a name="uninitialize-the-library"></a>Uninitialize biblioteket

När du är klar med att skicka data och ta emot meddelanden kan du uninitialize IoT-biblioteket:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Var och en av dessa tre funktioner överensstämmer med de tre initieringsfunktionerna som beskrivits tidigare. Att anropa dessa API:er säkerställer att du frigör tidigare allokerade resurser.

## <a name="next-steps"></a>Efterföljande moment

Den här artikeln omfattade grunderna för att använda biblioteken i **Azure IoT-enheten SDK för C**. Det gav dig tillräckligt med information för att förstå vad som ingår i SDK, dess arkitektur och hur du kommer igång med Windows-exemplen. Nästa artikel fortsätter beskrivningen av SDK genom att förklara [mer om IoTHubClient-biblioteket](iot-hub-device-sdk-c-iothubclient.md).

Mer information om hur du utvecklar för IoT Hub finns i [Azure IoT SDK:er](iot-hub-devguide-sdks.md).

Mer information om hur du utforskar funktionerna i IoT Hub finns i:

* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
