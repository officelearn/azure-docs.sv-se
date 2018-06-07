---
title: Azure IoT-enhet SDK för C | Microsoft Docs
description: Komma igång med Azure IoT-enhet SDK för C och lär dig hur du skapar appar för enheter som kommunicerar med en IoT-hubb.
author: yzhong94
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: yizhon
ms.openlocfilehash: 78cd8b2d6afe98e34c33ed3c841c8023d9dab764
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635278"
---
# <a name="azure-iot-device-sdk-for-c"></a>Azure IoT-enhet SDK för C

Den **Azure IoT-enhet SDK** är en uppsättning bibliotek som har utformats för att förenkla processen att skicka meddelanden till och ta emot meddelanden från den **Azure IoT Hub** service. Det finns olika varianter av SDK, målobjekt för en viss plattform, men den här artikeln beskriver den **Azure IoT-enhet SDK för C**.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Azure IoT-enhet SDK för C sparas i ANSI C (C99) för att maximera portability. Den här funktionen gör biblioteken väl lämpade för fungerar på flera plattformar och enheter, särskilt om minimera disk och minneskrav är en prioritet.

Det finns en mängd olika plattformar som SDK har testats (finns i [Azure certifierad för IoT-enhet katalogen](https://catalog.azureiotsuite.com/) information). Även om den här artikeln innehåller genomgångar av exempelkod som körs på Windows-plattformen, är koden som beskrivs i den här artikeln identiska mellan olika plattformar som stöds.

Följande videoklipp visar en översikt över Azure IoT-SDK för C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Den här artikeln ger en introduktion till Azure IoT-enhet SDK-arkitektur för C. Den visar hur du starta enheten biblioteket, skicka data till IoT-hubb och ta emot meddelanden från den. Informationen i den här artikeln bör vara tillräckligt för att komma igång med SDK, men innehåller också länkar till ytterligare information om bibliotek.

## <a name="sdk-architecture"></a>SDK-arkitektur

Du hittar den [ **Azure IoT-enhet SDK för C** ](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen och visa information om API i den [C API-referens för](https://azure.github.io/azure-iot-sdk-c/index.html).

Den senaste versionen av biblioteken kan hittas i den **master** gren av databasen:

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

* Core-implementeringen av SDK finns i den **iothub\_klienten** mapp som innehåller implementeringen av lägsta API-lagret i SDK: den **IoTHubClient** bibliotek. Den **IoTHubClient** biblioteket innehåller API: er som implementerar raw-meddelanden för att skicka meddelanden till IoT-hubb och ta emot meddelanden från IoT-hubb. När du använder det här biblioteket kan du ansvarar för att implementera meddelandet serialisering, men andra detaljer för kommunikation med IoT-hubb hanteras åt dig.
* Den **serialiseraren** mappen innehåller hjälpfunktioner och exempel som visar hur du serialisera data innan du skickar till Azure IoT-hubb med hjälp av klientbiblioteket. Användning av serialiseraren är inte obligatoriskt och tillhandahålls bekvämlighets skull. Att använda den **serialiseraren** bibliotek, definierar du en modell som anger vilka data som ska skickas till IoT-hubb och meddelanden som du förväntar dig att ta emot från den. När modellen har definierats ger i SDK dig en API-yta som gör att du enkelt att arbeta med enhet till moln och moln till enhet utan att oroa serialisering information. Biblioteket är beroende av andra bibliotek med öppen källkod som implementerar transport med hjälp av protokoll som MQTT och AMQP.
* Den **IoTHubClient** biblioteket är beroende av andra bibliotek med öppen källkod:
  * Den [Azure C delade verktyget](https://github.com/Azure/azure-c-shared-utility) biblioteket, som innehåller vanliga funktioner för grundläggande uppgifter (till exempel strängar, lista bearbetning och -i/o) som krävs för över flera Azure-relaterade C SDK: er.
  * Den [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) biblioteket, som är en implementering för klientsidan av AMQP som är optimerade för resursen begränsad enheter.
  * Den [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) biblioteket, vilket är ett allmänna implementering av protokollet MQTT-bibliotek och optimeras för resursen begränsad enheter.

Användning av dessa bibliotek är lättare att förstå genom att titta på exempelkod. I följande avsnitt för att gå igenom flera exempelprogram som ingår i SDK. Den här genomgången bör ge dig en bra känsla för olika funktioner i arkitekturen lager av SDK och en introduktion till hur fungerar de API: er.

## <a name="before-you-run-the-samples"></a>Innan du kör

Innan du kan köra exemplen i Azure IoT-enhet SDK för C, måste du [skapa en instans av tjänsten IoT-hubb](iot-hub-create-through-portal.md) i din Azure-prenumeration. Slutför följande uppgifter:

* Förbereda utvecklingsmiljön
* Hämta autentiseringsuppgifter för enheten.

### <a name="prepare-your-development-environment"></a>Förbereda utvecklingsmiljön

Paket har angetts för vanliga plattformar (till exempel NuGet för Windows eller apt_get för Debian och Ubuntu) och exemplen använder dessa paket när det är tillgängligt. I vissa fall kan behöva du kompilera SDK för eller på din enhet. Om du behöver kompileras SDK finns [förbereda din utvecklingsmiljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) i GitHub-lagringsplatsen.

Exempelkod för program, hämtas en kopia av SDK från GitHub. Hämta en kopia av källa från den **master** grenen av den [GitHub-lagringsplatsen](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Skaffa autentiseringsuppgifter för enhet

Nu när du har källa exempelkoden är nästa du behöver göra att hämta en uppsättning autentiseringsuppgifter för enheten. För en enhet för att kunna komma åt en IoT-hubb, måste du först lägga till enheten till identitetsregistret IoT-hubb. När du lägger till en enhet kan du hämta en uppsättning autentiseringsuppgifter för enheten som du behöver för enheten för att kunna ansluta till IoT-hubben. Exempelprogram som beskrivs i nästa avsnitt räknar autentiseringsuppgifterna i form av en **enheten anslutningssträngen**.

Det finns flera öppen källkod verktyg som hjälper dig att hantera din IoT-hubb.

* Ett Windows-program som kallas [enheten explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).
* Ett plattformsoberoende Python CLI-verktyg kallas [IoT-tillägget för Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

Den här kursen använder den grafiska *enheten explorer* verktyget. Du kan också använda den *IoT-tillägget för Azure CLI 2.0* verktyget om du föredrar att använda ett CLI-verktyg.

Verktyget enheten explorer använder bibliotek för Azure IoT-tjänsten för att utföra olika funktioner i IoT Hub, inklusive lägga till enheter. Om du använder verktyget enheten explorer lägga till en enhet får du en anslutningssträng för enheten. Du behöver den här anslutningssträngen för att köra exempelprogrammen.

Om du inte är bekant med verktyget enheten explorer beskriver följande procedur hur du lägger till en enhet och få en anslutningssträng för enheten.

Om du vill installera verktyget enheten explorer [hur du använder Utforskaren enhet för IoT Hub-enheter](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

När du kör programmet visas det här gränssnittet:

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Ange din **IoT-hubb anslutningssträngen** i det första fältet och klickar på **uppdatering**. Det här steget konfigurerar verktyget så att den kan kommunicera med IoT-hubb.

När IoT-hubb anslutningssträngen har konfigurerats klickar du på den **Management** fliken:

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

Den här fliken är där du hanterar enheter som har registrerats i din IoT-hubb.

Du skapar en enhet genom att klicka på den **skapa** knappen. En dialogruta visas med en uppsättning i förväg nycklar (primär eller sekundär). Ange en **enhets-ID** och klicka sedan på **skapa**.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

När enheten har skapats listas enheterna uppdateringar med alla registrerade enheter, inklusive det som du nyss skapade. Om du högerklickar på den nya enheten visas den här menyn:

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Om du väljer **kopiera anslutningssträngen för vald enhet**, anslutningssträngen enhet kopieras till Urklipp. Spara en kopia av anslutningssträngen för enheten. Du behöver den när du kör exempelprogram som beskrivs i följande avsnitt.

När du har slutfört stegen ovan, är du redo att börja köra kod. Båda exempel har en konstant överst i filen huvudsakliga källan som gör att du kan ange en anslutningssträng. Till exempel motsvarande rad från den **iothub\_klienten\_exempel\_mqtt** programmet visas på följande sätt.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Använd IoTHubClient-biblioteket

Inom den **iothub\_klienten** mapp i den [azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) databasen, det finns en **exempel** mapp som innehåller ett program kallas **iothub\_klienten\_exempel\_mqtt**.

Windows-versionen av den **iothub\_klienten\_exempel\_mqtt** program innehåller följande Visual Studio-lösningen:

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-mqtt.PNG)

> [!NOTE]
> Om du öppnar det här projektet i Visual Studio 2017 accepterar du anvisningarna för att nytt mål projektet till den senaste versionen.

Den här lösningen innehåller ett projekt. Det finns fyra NuGet-paket i den här lösningen:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

Du måste alltid den **Microsoft.Azure.C.SharedUtility** när du arbetar med SDK-paketet. Det här exemplet använder protokollet MQTT, därför måste du inkludera den **Microsoft.Azure.umqtt** och **Microsoft.Azure.IoTHub.MqttTransport** paket (det finns motsvarande paket för AMQP och HTTPS). Eftersom den används av **IoTHubClient** bibliotek, måste du också inkludera den **Microsoft.Azure.IoTHub.IoTHubClient** paketet i din lösning.

Du kan hitta implementeringen för exempelprogrammet i den **iothub\_klienten\_exempel\_mqtt.c** källfilen.

Det här exempelprogrammet använder du följande steg leder dig igenom vilka krävs för att använda den **IoTHubClient** bibliotek.

### <a name="initialize-the-library"></a>Initiera biblioteket

> [!NOTE]
> Innan du börjar arbeta med bibliotek som du kan behöva utföra vissa plattformsspecifika initiering. Om du planerar att använda AMQP på Linux måste du initiera OpenSSL-bibliotek. Exemplen i den [GitHub-lagringsplatsen](https://github.com/Azure/azure-iot-sdk-c) anropa verktygsfunktionen **plattform\_init** när klienten startar och anropa den **plattform\_deinit**funktionen innan du avslutar. Dessa funktioner är deklarerad i huvudfilen platform.h. Granska definitionerna av dessa funktioner för målplattformen i den [databasen](https://github.com/Azure/azure-iot-sdk-c) att avgöra om du behöver lägga till alla plattformsspecifika initieringskod på klienten.

När du börjar arbeta med bibliotek, tilldelar du en IoT-hubb klienten referens:

```c
if ((iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

Du skickar en kopia av anslutningssträngen för enheten som du fick från explorer-verktyget enheten till den här funktionen. Du kan också ange kommunikationsprotokoll ska användas. Det här exemplet använder MQTT men AMQP och HTTPS är också alternativ.

När du har en giltig **IOTHUB\_klienten\_hantera**, kan du starta anropar API: er för att skicka och ta emot meddelanden till och från IoT-hubb.

### <a name="send-messages"></a>Skicka meddelanden

Exempelprogrammet ställer in en loop att skicka meddelanden till din IoT-hubb. Följande utdrag:

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

Varje gång du skickar ett meddelande kan ange du en referens till en återanropsfunktion som anropas när data skickas. I det här exemplet Återanropsfunktionen kallas **SendConfirmationCallback**. Följande utdrag visar Återanropsfunktionen:

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

Observera anropet till den **IoTHubMessage\_förstöra** fungera när du är klar med meddelandet. Funktionen frigör resurser som tilldelas när du har skapat meddelandet.

### <a name="receive-messages"></a>Ta emot meddelanden

Ett meddelande är en asynkron åtgärd. Först måste registrera du återanropet att anropa när enheten tar emot ett meddelande:

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

Den sista parametern är en ogiltig pekare till vad du vill. Det finns en pekare till ett heltal i det här exemplet men det kan vara en pekare till en mer komplex datastruktur. Den här parametern aktiverar Återanropsfunktionen att använda delade tillståndet med anroparen av den här funktionen.

När enheten tar emot ett meddelande, har registrerade Återanropsfunktionen anropats. Den här Återanropsfunktionen hämtar:

* Meddelande-id och Korrelations-id från meddelandet.
* Innehållet i meddelandet.
* Eventuella anpassade egenskaper från meddelandet.

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

Använd den **IoTHubMessage\_GetByteArray** funktion för att hämta meddelandet som i det här exemplet är en sträng.

### <a name="uninitialize-the-library"></a>Uninitialize i biblioteket

När du är klar skickar händelser och ta emot meddelanden, kan du uninitialize IoT-biblioteket. Gör du genom att utfärda funktionsanropet följande:

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Det här anropet frigör resurser som tidigare har allokerats av den **IoTHubClient\_CreateFromConnectionString** funktion.

Som du ser är det enkelt att skicka och ta emot meddelanden med den **IoTHubClient** bibliotek. Biblioteket hanterar information om kommunikation med IoT-hubb, inklusive vilka protokoll som ska användas (från utvecklaren perspektiv, detta är ett enkelt konfigurationsalternativ).

Den **IoTHubClient** biblioteket innehåller också exakt kontroll över hur du serialisera data enheten skickar till IoT-hubb. I vissa fall denna behörighet är en fördel, men i andra är att du inte vill är mest intresserad av. Om så är fallet kan du använda den **serialiseraren** biblioteket, som beskrivs i nästa avsnitt.

## <a name="use-the-serializer-library"></a>Använd serialiserare-biblioteket

Begreppsmässigt den **serialiseraren** biblioteket placeras ovanpå det **IoTHubClient** bibliotek i SDK. Den använder den **IoTHubClient** bibliotek för underliggande kommunikation med IoT-hubb, men det lägger till modellering funktioner som tar bort belastningen för att behandla meddelande serialisering från utvecklaren. Hur framgår fungerar det här biblioteket bästa av ett exempel.

I den **serialiseraren** mapp i den [azure-iot-sdk-c databasen](https://github.com/Azure/azure-iot-sdk-c), är en **exempel** mapp som innehåller ett program kallas **simplesample\_mqtt**. Windows-versionen av det här exemplet innehåller följande Visual Studio-lösningen:

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_mqtt.PNG)

> [!NOTE]
> Om du öppnar det här projektet i Visual Studio 2017 accepterar du anvisningarna för att nytt mål projektet till den senaste versionen.

Precis som med föregående exempel, innehåller den här flera NuGet-paket:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

Du har sett de flesta av dessa paket i föregående exempel, men **Microsoft.Azure.IoTHub.Serializer** är ny. Det här paketet måste anges när du använder den **serialiseraren** bibliotek.

Du kan hitta implementeringen av exempelprogrammet i den **simplesample\_mqtt.c** fil.

I följande avsnitt vägleder dig igenom de viktigaste delarna i det här exemplet.

### <a name="initialize-the-library"></a>Initiera biblioteket

Att arbeta med den **serialiseraren** bibliotek, anropa initieringen API: er:

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

Anropet till den **serialiseraren\_init** funktionen är en enstaka anrop och initierar underliggande biblioteket. Sedan kan du anropa den **IoTHubClient\_lla\_CreateFromConnectionString** funktion, vilket är samma API som i den **IoTHubClient** exempel. Det här anropet anger anslutningssträngen enhet (det här anropet är också där du kan välja protokollen du vill använda). Det här exemplet används MQTT som transport, men kan använda AMQP eller HTTPS.

Slutligen anropa den **skapa\_MODELLEN\_instans** funktion. **WeatherStation** är namnrymd för modellen och **ContosoAnemometer** är namnet på modellen. När modellen-instansen har skapats kan använda du den för att börja skicka och ta emot meddelanden. Det är dock viktigt att förstå vilka modellen.

### <a name="define-the-model"></a>Definiera modellen

En modell i den **serialiseraren** biblioteket definierar de meddelanden som enheten kan skicka till IoT-hubb och meddelanden, som kallas *åtgärder* i modelleringsspråk, vilket kan ta emot. Du definierar en modell med en uppsättning C makron som i den **simplesample\_mqtt** exempelprogrammet:

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

Den **börjar\_namnområde** och **END\_namnområde** makron båda tar namnområdet för modellen som argument. Det förväntas att något mellan dessa makron är definitionen av din modell eller modeller och datastrukturer som använder modeller.

I det här exemplet är en modell kallas **ContosoAnemometer**. Den här modellen definierar två typer av data som enheten kan skicka till IoT-hubb: **DeviceId** och **vindhastigheten**. Den definierar även tre åtgärder (meddelanden) som kan ta emot din enhet: **TurnFanOn**, **TurnFanOff**, och **SetAirResistance**. Alla dataposter har en typ och varje åtgärd har ett namn (och eventuellt en uppsättning parametrar).

Definiera en API-yta som du kan använda för att skicka meddelanden till IoT-hubb och svara på meddelanden som skickas till enheten data och åtgärder som definierats i modellen. Användning av den här modellen är bäst att förstå genom ett exempel.

### <a name="send-messages"></a>Skicka meddelanden

Modellen definierar de data som du kan skicka till IoT-hubb. I det här exemplet definieras en innebär två dataobjekt med hjälp av den **WITH_DATA** makro. Det finns flera steg som krävs för att skicka **DeviceId** och **vindhastigheten** värden till en IoT-hubb. Först är att ange de data som du vill skicka:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

Den modell som du angav tidigare kan du ange värdena genom att ange medlemmar i en **struct**. Därefter serialisera du vill skicka meddelandet:

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

Den här koden Serialiserar det enhet till molnet till en buffert (refereras av **mål**). Koden anropar sedan den **sendMessage** funktion för att skicka meddelandet till IoT-hubb:

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


Näst sista parametern för **IoTHubClient\_lla\_SendEventAsync** är en referens till en callback-funktion som anropas när data har skickats. Här är Återanropsfunktionen i exemplet:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Den andra parametern är en pekare till användarkontext; samma pekaren som skickades till **IoTHubClient\_lla\_SendEventAsync**. I det här fallet kontexten är en enkel räknare, men det kan vara vad du vill.

Nu är det inte att skicka meddelanden från enhet till moln. Det enda som återstår för att täcka är ta emot meddelanden.

### <a name="receive-messages"></a>Ta emot meddelanden

Ta emot ett meddelande fungerar på samma sätt som sätt meddelanden fungerar i de **IoTHubClient** bibliotek. Först måste registrera du ett meddelande Återanropsfunktionen:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
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

Den här koden är formaterad – det är samma för alla lösningar. Den här funktionen tar emot meddelandet och tar hand om routning till önskad funktion via anrop till **kör\_kommandot**. Den anropade funktionen beror nu på definitionen av åtgärder i din modell.

När du definierar en åtgärd i din modell kan måste du implementera en funktion som anropas när enheten tar emot ett meddelande. Om till exempel din modell definierar den här åtgärden:

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

Observera hur namnet på funktionen matchar namnet på åtgärden i modellen och att parametrar i funktionen matchar de angivna parametrarna för åtgärden. Den första parametern krävs alltid och innehåller en pekare till instansen av din modell.

När enheten tar emot ett meddelande som matchar denna signatur, anropas motsvarande funktionen. Därför utöver att lägga till standardiserade koden från **IoTHubMessage**, ta emot meddelanden är bara en fråga för att definiera en enkel funktion för varje åtgärd som definierats i din modell.

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

Dessa tre funktioner överensstämmer med de tre initiering funktionerna som beskrivs ovan. Anropar API: erna säkerställer du frigöra tidigare allokerade resurser.

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs grunderna i biblioteken i de **Azure IoT-enhet SDK för C**. Det medföljer tillräckligt med information för att förstå vad som ingår i SDK, dess arkitektur och hur du kommer igång med Windows-exempel. I nästa artikel fortsätter beskrivning av SDK genom att förklara [mer om biblioteket IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Mer information om hur du utvecklar för IoT-hubb finns i [Azure IoT SDK][lnk-sdks].

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iotedge]

[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
