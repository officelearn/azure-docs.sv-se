---
title: Azure IoT-enhetens SDK för C | Microsoft Docs
description: Kom igång med Azure IoT-enhetens SDK för C och lär dig hur du skapar appar för enheter som kommunicerar med en IoT-hubb.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: yizhon
ms.openlocfilehash: 7734a273f6e07290ec7124def5cb3b095f7f067b
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674258"
---
# <a name="azure-iot-device-sdk-for-c"></a>Azure IoT-enhetens SDK för C

Den **Azure IoT-enhetens SDK** är en uppsättning klientbibliotek som utformats för att förenkla processen att skicka meddelanden till och ta emot meddelanden från den **Azure IoT Hub** service. Det finns olika varianter av SDK, varje riktar in sig på en viss plattform, men den här artikeln beskriver den **Azure IoT-enhetens SDK för C**.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Azure IoT-enhetens SDK för C är skriven i ANSI C (C99) för maximal portabilitet. Den här funktionen gör bibliotek som passar bra för att arbeta med flera plattformar och enheter, särskilt om minimerar disk och minnesfotavtryck är en prioritet.

Det finns en mängd olika plattformar där SDK har testats (se den [Azure Certified för IoT-enhetskatalog](https://catalog.azureiotsuite.com/) information). Även om den här artikeln innehåller genomgångar av exempelkod som körs på Windows-plattformen, är koden som beskrivs i den här artikeln identiska över olika plattformar som stöds.

Följande videoklipp visar en översikt över Azure IoT SDK för C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Den här artikeln ger en introduktion till arkitekturen i Azure IoT-enhetens SDK för C. Den visar hur du initiera enhet-biblioteket, skicka data till IoT Hub och ta emot meddelanden från den. Informationen i den här artikeln bör räcka för att komma igång med SDK: N, men innehåller också länkar till ytterligare information om bibliotek.

## <a name="sdk-architecture"></a>SDK-arkitektur

Du hittar den [ **Azure IoT-enhetens SDK för C** ](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplats och visa information om API: et i den [C API-referens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

Den senaste versionen av biblioteken kan hittas i den **master** gren av databasen:

  ![Skärmbild av huvuddelen av databasen](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* Core-implementeringen av SDK: N finns i den **iothub\_klienten** mapp som innehåller implementeringen av det lägsta API-lagret i SDK: den **IoTHubClient** biblioteket. Den **IoTHubClient** -bibliotek innehåller API: er som implementerar raw-meddelanden för att skicka meddelanden till IoT Hub och ta emot meddelanden från IoT Hub. När du använder det här biblioteket kan du ansvarar för att implementera meddelande serialisering, men andra uppgifter för att kommunicera med IoT Hub hanteras åt dig.

* Den **serialiserare** mappen innehåller hjälpfunktioner och exempel som visar hur du serialisera data innan du skickar till Azure IoT Hub med hjälp av klientbiblioteket. Användning av serialiserare är inte obligatoriskt och tillhandahålls bekvämlighets skull. Du använder den **serialiserare** biblioteket du definiera en modell som anger vilka data du skickar till IoT Hub och meddelanden som du förväntar dig att ta emot från den. När modellen har definierats, tillhandahåller SDK en API-yta som gör att du enkelt kan arbeta med enhet till moln och från moln till enhet-meddelanden utan att behöva bekymra dig om serialisering. Biblioteket är beroende av andra bibliotek med öppen källkod som implementerar transport med hjälp av protokoll, till exempel MQTT och AMQP.

* Den **IoTHubClient** biblioteket är beroende av andra bibliotek med öppen källkod:

  * Den [Azure C delade verktyget](https://github.com/Azure/azure-c-shared-utility) biblioteket, som tillhandahåller vanliga funktioner för grundläggande uppgifter (till exempel strängar, manipulering av listan och i/o) behövs över flera Azure-relaterade C SDK: er.

  * Den [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) biblioteket, som är en klientsidan implementering av AMQP optimerat för resursen begränsad enheter.

  * Den [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) biblioteket, vilket är ett allmänt bibliotek som implementerar MQTT-protokollet och optimerat för resursen begränsad enheter.

Användning av dessa bibliotek är lättare att förstå genom att studera exempelkoden. I följande avsnitt vägleder dig igenom flera av de exempelprogram som ingår i SDK. Den här genomgången bör du få en bra design för de olika funktionerna i arkitekturen lager av SDK och en introduktion till hur API: er fungerar.

## <a name="before-you-run-the-samples"></a>Innan du kör exemplen

Innan du kan köra exemplen i Azure IoT-enhetens SDK för C, måste du [skapa en instans av tjänsten IoT Hub](iot-hub-create-through-portal.md) i Azure-prenumerationen. Slutför följande uppgifter:

* Förbereda utvecklingsmiljön
* Hämta enhetens autentiseringsuppgifter.

### <a name="prepare-your-development-environment"></a>Förbereda utvecklingsmiljön

Paket som har angetts för vanliga plattformar (till exempel NuGet för Windows eller apt_get för Debian och Ubuntu) och exempel som använder dessa paket när det är tillgängligt. I vissa fall kan behöva du kompilera SDK för eller på din enhet. Om du behöver kompilera SDK finns i [Förbered din utvecklingsmiljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) i GitHub-lagringsplatsen.

Hämta en kopia av SDK: N för att hämta programmet exempelkoden från GitHub. Hämta din kopia av källa från den **master** grenen av den [GitHub-lagringsplatsen](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Hämta enhetens autentiseringsuppgifter

Nu när du har källa exempelkoden är nästa du ska göra att få en uppsättning autentiseringsuppgifter för enheten. För en enhet för att kunna få åtkomst till en IoT-hubb, måste du först lägga till enheten till IoT Hub-identitetsregistret. När du lägger till din enhet kan du få en uppsättning enhetens autentiseringsuppgifter som ska användas för enheten för att kunna ansluta till IoT hub. Exempelprogram som beskrivs i nästa avsnitt förväntar sig autentiseringsuppgifterna i form av en **enhetsanslutningssträngen**.

Det finns flera verktyg för öppen källkod för att hantera din IoT-hubb.

* Ett Windows-program som kallas [enhetsutforskare](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

* Ett plattformsoberoende Visual Studio Code-tillägg kallas [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

* En plattformsoberoende Python CLI kallas [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

Den här självstudien används den grafiska *enhetsutforskare* verktyget. Du kan använda den *Azure IoT-verktyg för VS Code* om du utvecklar i VS Code. Du kan också använda den *IoT-tillägget för Azure CLI 2.0* verktyget om du föredrar att använda ett CLI-verktyg.

Device explorer-verktyg använder Azure IoT service-bibliotek för att utföra olika funktioner på IoT-hubben, som att lägga till enheter. Om du använder device explorer-verktyg för att lägga till en enhet, får du en anslutningssträng för din enhet. Du behöver den här anslutningssträngen för körs.

Om du inte är bekant med device explorer-verktyg, beskriver följande procedur hur du använder det för att lägga till en enhet och få en anslutningssträng för enheten.

1. Om du vill installera device explorer-verktyg, se [hur du använder Device Explorer för IoT Hub-enheter](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

1. När du kör programmet visas det här gränssnittet:

  ![Device Explorer Twin skärmbild](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Ange din **IoT Hub-anslutningssträngen** i första fältet och klickar på **uppdatering**. Det här steget konfigurerar verktyget så att den kan kommunicera med IoT Hub. 

Den **Connection String** finns under **IoT Hub-tjänsten** > **inställningar** > **Policy för delad åtkomst**  >  **iothubowner**.

1. När IoT Hub-anslutningssträngen konfigureras, klickar du på den **Management** fliken:

  ![Device Explorer Twin / hantering av skärmbild](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

Den här fliken är där du hanterar enheter som är registrerade i IoT hub.

1. Du skapar en enhet genom att klicka på den **skapa** knappen. En dialogruta visas med en uppsättning förifyllda nycklar (primär eller sekundär). Ange en **enhets-ID** och klicka sedan på **skapa**.

  ![Skapa enhet skärmbild](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. När enheten har skapats, lista enheterna uppdateringar med alla registrerade enheter, inklusive den som du nyss skapade. Om du högerklickar på den nya enheten kan se du den här menyn:

  ![Device Explorer Twin högerklickar du på resultatet](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. Om du väljer **kopiera anslutningssträngen för vald enhet**, enhetens anslutningssträng har kopierats till Urklipp. Behåll en kopia av enhetens anslutningssträng. Du behöver den när du kör exempelprogram som beskrivs i följande avsnitt.

När du har slutfört stegen ovan, är du redo att börja köra kod. De flesta exempel har en konstant överst i filen huvudsakliga källan som gör att du kan ange en anslutningssträng. Till exempel motsvarande rad från den **iothub_client\_exempel\_iothub_convenience_sample** program visas på följande sätt.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Använda IoTHubClient klientbiblioteket

I den **iothub\_klienten** mapp i den [azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) lagringsplatsen, det finns en **exempel** mapp som innehåller ett program kallas **iothub\_klienten\_exempel\_mqtt**.

Windows-versionen av den **iothub_client\_exempel\_iothub_convenience_sample** programmet innehåller följande Visual Studio-lösningen:

  ![Visual Studio Solution Explorer](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Om du öppnar det här projektet i Visual Studio 2017, accepterar du anvisningarna för att omdirigera projektet till den senaste versionen.

Den här lösningen innehåller ett enda projekt. Det finns fyra NuGet-paket i den här lösningen:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

Du behöver alltid den **Microsoft.Azure.C.SharedUtility** paketera när du arbetar med SDK: N. Det här exemplet används av MQTT-protokollet, därför måste du inkludera den **Microsoft.Azure.umqtt** och **Microsoft.Azure.IoTHub.MqttTransport** paket (det finns motsvarande paket för AMQP- och HTTPS). Eftersom i exemplet används den **IoTHubClient** biblioteket, måste du också inkludera den **Microsoft.Azure.IoTHub.IoTHubClient** paketet i din lösning.

Du kan hitta implementeringen för exempelprogrammet i den **iothub_client\_exempel\_iothub_convenience_sample** källfilen.

Följande steg använder det här exempelprogrammet som leder dig genom vad krävs för att använda den **IoTHubClient** biblioteket.

### <a name="initialize-the-library"></a>Initiera i biblioteket

> [!NOTE]
> Innan du börjar arbeta med bibliotek som kan du behöva utföra viss plattformsspecifika initiering. Om du planerar att använda AMQP på Linux måste du initiera OpenSSL-bibliotek. Exemplen i den [GitHub-lagringsplatsen](https://github.com/Azure/azure-iot-sdk-c) anropa verktygsfunktionen **plattform\_init** när klienten startar och anropa den **plattform\_deinit**funktionen innan du avslutar. Dessa funktioner har deklarerats i huvudfilen platform.h. Granska definitionerna av dessa funktioner för din målplattform i den [databasen](https://github.com/Azure/azure-iot-sdk-c) att avgöra om du behöver inkludera plattformsspecifika initieringskod i din klient.

Om du vill börja arbeta med bibliotek, tilldelar du en referens för IoT Hub-klienten:

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

Du skickar en kopia av enhetens anslutningssträng du fick från device explorer-verktyg till den här funktionen. Du kan också ange kommunikationsprotokoll som ska användas. Det här exemplet använder MQTT, men AMQP och HTTPS är också alternativ.

När du har en giltig **IOTHUB\_klienten\_hantera**, du kan börja med att anropa API: er för att skicka och ta emot meddelanden till och från IoT Hub.

### <a name="send-messages"></a>Skicka meddelanden

Exempelprogrammet ställer in en loop för att skicka meddelanden till din IoT-hubb. Följande utdrag:

- Skapar ett meddelande.
- Lägger till en egenskap i meddelandet.
- Skickar ett meddelande.

Börja med att skapa ett meddelande:

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

Varje gång du skickar ett meddelande, kan du ange en referens till en återanropsfunktion som anropas när data skickas. I det här exemplet är Återanropsfunktionen kallas **SendConfirmationCallback**. Följande utdrag visar den här återanropsfunktion:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Observera anropet till den **IoTHubMessage\_ta bort** fungerar när du är klar med meddelandet. Den här funktionen frigör resurser tilldelas när du har skapat meddelandet.

### <a name="receive-messages"></a>Ta emot meddelanden

Ta emot ett meddelande är en asynkron åtgärd. Först måste registrera du motringningen ska anropas när enheten tar emot ett meddelande:

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

Den sista parametern är en ogiltig pekare till vad som helst. I det här exemplet att det finns en pekare till ett heltal men det kan vara en pekare till en mer komplex datastruktur. Den här parametern kan återanropsfunktion att arbeta med delade tillstånd med anroparen av den här funktionen.

När enheten tar emot ett meddelande, anropas den registrerade Återanropsfunktionen. Den här återanropsfunktion hämtar:

* Meddelande-id och Korrelations-id från meddelandet.
* Meddelandeinnehåll.
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

Använd den **IoTHubMessage\_GetByteArray** funktionen för att hämta meddelandet som i det här exemplet är en sträng.

### <a name="uninitialize-the-library"></a>Uninitialize i biblioteket

När du är klar skickar händelser och mottagande meddelanden uninitialize du IoT-biblioteket. Utfärda följande funktionsanropet gör du:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Det här anropet frigör resurser som tidigare har allokerats av den **IoTHubClient\_CreateFromConnectionString** funktion.

Som du kan se är det enkelt att skicka och ta emot meddelanden med den **IoTHubClient** biblioteket. Biblioteket sköter för att kommunicera med IoT-hubb, inklusive vilka protokoll som ska användas (ur utvecklaren, är detta ett konfigurationsalternativ för enkel).

Den **IoTHubClient** -biblioteket finns också exakt kontroll över hur du serialisera data som enheten skickar till IoT Hub. I vissa fall är den här kontrollnivån en fördel, men andra det är att du inte vill är mest intresserad av. Om så är fallet kan du använda den **serialiserare** biblioteket, som beskrivs i nästa avsnitt.

## <a name="use-the-serializer-library"></a>Använd serialiserare-biblioteket

Begreppsmässigt den **serialiserare** biblioteket är placerad ovanpå den **IoTHubClient** bibliotek i SDK. Den använder den **IoTHubClient** bibliotek för underliggande kommunikation med IoT Hub, men det lägger till funktioner för modellering som tar bort bördan med att hantera meddelande serialisering från utvecklaren. Hur framgår fungerar det här biblioteket bästa av ett exempel.

I den **serialiserare** mapp i den [lagringsplatsen för azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c), är en **exempel** mappen som innehåller ett program **simplesample\_mqtt**. Windows-versionen av det här exemplet innehåller följande Visual Studio-lösningen:

  ![Visual Studio-lösning för mqtt-exemplet](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Om du öppnar det här projektet i Visual Studio 2017, accepterar du anvisningarna för att omdirigera projektet till den senaste versionen.

Precis som med i föregående exempel innehåller den här flera NuGet-paket:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

Du har sett de flesta av dessa paket i föregående exempel, men **Microsoft.Azure.IoTHub.Serializer** är ny. Det här paketet måste anges när du använder den **serialiserare** biblioteket.

Du kan hitta implementeringen av exempelprogrammet i den ***iothub_client\_exempel\_iothub_convenience_sample** fil.

I följande avsnitt vägleder dig genom de viktigaste delarna av det här exemplet.

### <a name="initialize-the-library"></a>Initiera i biblioteket

Att börja arbeta med den **serialiserare** biblioteket, anropa initieringen API: er:

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

Anropet till den **serialiserare\_init** funktionen är ett enstaka anrop och initierar underliggande biblioteket. Sedan kan du anropa den **IoTHubClient\_lla\_CreateFromConnectionString** funktionen, som är samma API som i den **IoTHubClient** exemplet. Det här anropet anger enhetens anslutningssträng (det här anropet är också där du kan välja protokollet som du vill använda). Det här exemplet använder MQTT som transport, men kan använda AMQP eller HTTPS.

Anropa slutligen den **skapa\_MODELLEN\_instans** funktion. **WeatherStation** är namnområdet för modellen och **ContosoAnemometer** är namnet på modellen. När modellen instans har skapats kan använda du den för att börja skicka och ta emot meddelanden. Det är dock viktigt att förstå vilka en modell är.

### <a name="define-the-model"></a>Definiera modellen

En modell i den **serialiserare** biblioteket definierar de meddelanden som enheten kan skicka till IoT Hub och meddelanden som kallas *åtgärder* i modelleringsspråk, vilket kan ta emot. Du definierar en modell med en uppsättning C makron som i den **iothub_client\_exempel\_iothub_convenience_sample** exempelprogrammet:

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

Den **börjar\_namnområde** och **slutet\_namnområde** makron båda ta namnområdet för modellen som ett argument. Det förväntas att något mellan dessa makron är definitionen av din modell eller modeller och datastrukturer som använder modeller.

I det här exemplet har en enda modell heter **ContosoAnemometer**. Den här modellen definierar två typer av data som din enhet kan skicka till IoT Hub: **DeviceId** och **vindhastigheten**. Den definierar också tre åtgärder (meddelanden) som enheten kan ta emot: **TurnFanOn**, **TurnFanOff**, och **SetAirResistance**. Alla dataposter har en typ, och varje åtgärd har ett namn (och eventuellt en uppsättning parametrar).

Data och åtgärder som definierats i modellen kan du definiera en API-yta som du kan använda för att skicka meddelanden till IoT Hub och svara på meddelanden som skickas till enheten. Användning av den här modellen är bäst tolkas igenom ett exempel.

### <a name="send-messages"></a>Skicka meddelanden

Modellen definierar de data som du kan skicka till IoT Hub. I det här exemplet definieras innebär det att en av de två dataobjekt med hjälp av den **WITH_DATA** makro. Det finns flera steg som krävs för att skicka **DeviceId** och **vindhastigheten** värden till en IoT hub. Först är att ange de data som du vill skicka:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

Modellen som du angav tidigare kan du ange värdena genom att ange medlemmar i en **struct**. Serialisera sedan det meddelande du vill skicka:

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

Den här koden Serialiserar det enhet-till-molnet till en buffert (refereras av **mål**). Koden anropar sedan den **sendMessage** funktionen för att skicka meddelandet till IoT Hub:

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

Näst sista parametern för **IoTHubClient\_lla\_SendEventAsync** är en referens till en återanropsfunktion som anropas när data skickades. Här är Återanropsfunktionen i det här exemplet:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Den andra parametern är en pekare till användarkontext; samma visaren skickades till **IoTHubClient\_lla\_SendEventAsync**. I det här fallet kontexten är en enkel räknare, men det kan vara allt du vill.

Det är allt det inte att skicka meddelanden från enheten till molnet. Det enda som återstår för att täcka är att ta emot meddelanden.

### <a name="receive-messages"></a>Ta emot meddelanden

Ta emot ett meddelande fungerar på samma sätt till sätt meddelanden fungerar i de **IoTHubClient** biblioteket. Först måste registrera du en återanropsfunktion för meddelandet:

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

Sedan kan skriva du den återanropsfunktion som anropas när ett meddelande tas emot:

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

Den här koden är formaterad – det är samma för alla lösningar. Den här funktionen tar emot meddelandet och tar hand om att skicka det till önskad funktion via anrop till **kör\_kommandot**. Det beror nu den anropade funktionen på definitionen av åtgärder i din modell.

När du definierar en åtgärd i din modell, måste så du implementera en funktion som anropas när enheten tar emot ett meddelande. Exempel: om din modell definierar den här åtgärden:

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

Observera hur namnet på funktionen matchar namnet på åtgärden i modellen och att parametrarna för funktionen matchar de angivna parametrarna för åtgärden. Den första parametern krävs alltid och innehåller en pekare till instansen av din modell.

När enheten tar emot ett meddelande som matchar den här signaturen, anropas motsvarande funktionen. Därför, förutom att lägga till den formaterade exempelkoden från **IoTHubMessage**, ta emot meddelanden är bara att definiera en enkel funktion för varje åtgärd som definierats i din modell.

### <a name="uninitialize-the-library"></a>Uninitialize i biblioteket

När du är klar skickar data och ta emot meddelanden, kan du uninitialize IoT-biblioteket:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Var och en av dessa tre funktioner överensstämmer med de tre initiering funktionerna som beskrivs ovan. Anropar dessa API: er säkerställer att du frigöra tidigare tilldelade resurser.

## <a name="next-steps"></a>Nästa steg

Den här artikeln visar grunderna i att använda biblioteken i de **Azure IoT-enhetens SDK för C**. Det har försett dig med tillräckligt med information för att förstå vad som ingår i SDK, dess arkitektur och hur du kommer igång med att arbeta med Windows-exemplen. Nästa artikel om du fortsätter beskrivning av SDK: N genom att förklara [mer om IoTHubClient biblioteket](iot-hub-device-sdk-c-iothubclient.md).

Mer information om hur du utvecklar för IoT Hub finns det [Azure IoT SDK: er](iot-hub-devguide-sdks.md).

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
