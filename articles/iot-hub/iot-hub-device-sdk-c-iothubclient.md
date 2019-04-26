---
title: Azure IoT-enhetens SDK för C - IoTHubClient | Microsoft Docs
description: Hur du använder IoTHubClient-biblioteket i Azure IoT-enhetens SDK för C för att skapa appar för enheter som kommunicerar med en IoT-hubb.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: yizhon
ms.openlocfilehash: dd3b693271326c85688a275a65b67ad6257220e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60400702"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Azure IoT-enhetens SDK för C – mer om IoTHubClient

[Azure IoT-enhetens SDK för C](iot-hub-device-sdk-c-intro.md) är den första artikeln i den här serien introduktion till den **Azure IoT-enhetens SDK för C**. Den här artikeln beskrivs att det finns två arkitektoniska lager i SDK. Till basberäkningspris är den **IoTHubClient** bibliotek som hanterar direkt kommunikation med IoT Hub. Det finns också i **serialiserare** bibliotek som bygger ovanpå för att tillhandahålla tjänster för serialisering. I den här artikeln får du ytterligare information på den **IoTHubClient** biblioteket.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Föregående artikeln beskrivs hur du använder den **IoTHubClient** -biblioteket för att skicka händelser till IoT Hub och ta emot meddelanden. Den här artikeln utökar denna diskussion genom att förklara hur du hanterar mer exakt *när* du skicka och ta emot data, introducerar du den **lågnivå-API: er**. Vi förklarar också hur du ansluter egenskaper på händelser (och hämtar dem från meddelanden) med hjälp av egenskapen hantering av funktioner i den **IoTHubClient** biblioteket. Dessutom ger vi ytterligare förklaring av olika sätt att hantera meddelanden från IoT Hub.

Artikeln avslutas genom som omfattar ett antal olika ämnen, t.ex. mer om enhetens autentiseringsuppgifter och hur du ändrar den **IoTHubClient** via konfigurationsalternativ.

Vi använder den **IoTHubClient** SDK-exempel som förklarar dessa avsnitt. Om du vill följa med kan se den **iothub\_klienten\_exempel\_http** och **iothub\_klienten\_exempel\_amqp**program som ingår i Azure IoT-enhetens SDK för C. allt som beskrivs i följande avsnitt visas i dessa exempel.

Du hittar den [ **Azure IoT-enhetens SDK för C** ](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplats och visa information om API: et i den [C API-referens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-lower-level-apis"></a>På lägre nivå-API: er

Föregående artikel beskrivs grundläggande driften av den **IotHubClient** inom ramen för den **iothub\_klienten\_exempel\_amqp** program. Till exempel beskrivs det hur du initierar i biblioteket som använder den här koden.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Här beskrivs också hur man skickar händelser med hjälp av den här funktionsanrop.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

I artikeln beskrivs också hur du tar emot meddelanden genom att registrera en återanropsfunktion.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Artikeln visar också hur du frigör resurser med hjälp av koden, till exempel följande.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

Det finns tillhörande funktioner för var och en av dessa API: er:

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

Dessa funktioner som innehåller alla **lla** i API-namn. Andra den **lla** en del av namnet, parametrarna för var och en av dessa funktioner är identiska med deras icke lla-motsvarigheter. Beteendet för dessa funktioner är dock olika på ett sätt som viktiga.

När du anropar **IoTHubClient\_CreateFromConnectionString**, de underliggande biblioteken skapa en ny tråd som körs i bakgrunden. Den här tråden skickar händelser till och tar emot meddelanden från IoT Hub. Ingen sådan tråd skapas när du arbetar med den **lla** API: er. Skapandet av bakgrundstråden är att underlätta för utvecklaren. Du behöver bekymra dig om explicit skicka händelser och ta emot meddelanden från IoT Hub--det sker automatiskt i bakgrunden. Däremot den **lla** API: er ger dig explicit kontroll över kommunikation med IoT Hub om du behöver den.

För att bättre förstå detta begrepp, nu ska vi titta på ett exempel:

När du anropar **IoTHubClient\_SendEventAsync**, vad du faktiskt gör är att placera händelsen i en buffert. Bakgrundstråden som skapas när du anropar **IoTHubClient\_CreateFromConnectionString** ständigt övervakar bufferten och skickar data som den innehåller till IoT Hub. Detta sker i bakgrunden på samma gång att huvudtråden utför annat arbete.

På samma sätt när du registrerar en återanropsfunktion för meddelanden med **IoTHubClient\_SetMessageCallback**, du instruera SDK om du vill ha bakgrundstråden anropa Återanropsfunktionen när ett meddelande mottagna, oberoende av huvudtråden.

Den **lla** API: er inte skapa en bakgrundstråd. I stället måste ett nytt API anropas för att skicka och ta emot data från IoT Hub uttryckligen. Detta visas i följande exempel.

Den **iothub\_klienten\_exempel\_http** program som ingår i SDK: N visar på lägre nivå-API: er. I det här exemplet skicka vi händelser till IoT Hub med kod, till exempel följande:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

De tre första raderna skapa meddelandet och den sista raden skickas händelsen. Som tidigare nämnts, skickas händelsen innebär att data är helt enkelt placeras i en buffert. Inget överförs i nätverket när vi kallar **IoTHubClient\_lla\_SendEventAsync**. För att faktiskt inkommande data till IoT Hub måste du anropa **IoTHubClient\_lla\_DoWork**, som i det här exemplet:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Den här koden (från den **iothub\_klienten\_exempel\_http** program) upprepade gånger **IoTHubClient\_lla\_DoWork**. Varje gång **IoTHubClient\_lla\_DoWork** är anropas den skickar vissa händelser från bufferten till IoT Hub och den hämtar ett meddelande som skickas till enheten. Det senare fallet innebär att om vi har registrerat en återanropsfunktion för meddelanden, sedan motringningen startas (förutsatt att alla meddelanden i kö). Vi skulle har registrerat en återanropsfunktion med kod, till exempel följande:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Orsaken som **IoTHubClient\_lla\_DoWork** kallas ofta en slinga är att varje gång den anropas, skickar den *vissa* buffras händelser till IoT Hub och hämtar *nästa* meddelandet i kö för enheten. Varje anrop är inte garanterad att skicka alla buffrade händelser eller om du vill hämta alla meddelanden i kön. Om du vill skicka alla händelser i bufferten och fortsätt sedan med annan bearbetning kan du ersätta den här loopen med koden, till exempel följande:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Den här koden anropar **IoTHubClient\_lla\_DoWork** förrän alla händelser i bufferten har skickats till IoT Hub. Observera att detta också inte innebär att alla köade meddelanden har mottagits. En del av anledningen är att söka efter ”alla”-meddelanden är inte deterministisk som en åtgärd. Vad händer om du hämtar ”alla” av meddelanden, men en annan skickas sedan till enheten omedelbart efter? Ett bättre sätt att hantera som är med en tidsgräns som är programmerade. Återanropsfunktionen meddelande kunde återställa en timer varje gång den anropas. Du kan sedan skriva logik för att fortsätta bearbetningen om till exempel inga meddelanden har tagits emot under senaste *X* sekunder.

När du är klar ingressing händelser och ta emot meddelanden, ska du anropa funktionen motsvarande för att rensa resurser.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Det är i princip bara en uppsättning API: er för att skicka och ta emot data med en bakgrundstråd och en annan uppsättning API: er som gör samma sak utan bakgrundstråden. Många utvecklare kanske föredrar icke - lla API: erna, men på lägre nivå-API: er är användbara när utvecklare vill explicit kontroll över nätverksöverföringar. Till exempel vissa enheter samla in data över tid och endast ingångshändelser vid angivna intervall (till exempel en gång en timme eller en gång om dagen). På lägre nivå-API: er ger dig möjlighet att uttryckligen kontroll när du skickar och tar emot data från IoT Hub. Andra kommer helt enkelt föredrar enkelheten med API: er för på lägre nivå. Allt som händer på huvudtråden i stället för vissa work händer i bakgrunden.

Oavsett vilken modell som du väljer, måste du vara konsekvent med vilka API: er som du använder. Om du startar genom att anropa **IoTHubClient\_lla\_CreateFromConnectionString**, se till att du bara använda motsvarande lågnivå-API: er för all Uppföljnings:

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy
* IoTHubClient\_LL\_DoWork

Motsatt gäller även. Om du startar med **IoTHubClient\_CreateFromConnectionString**, sedan använda icke - lla API: erna för alla ytterligare bearbetning.

I den Azure IoT-enheten SDK för C, ser de **iothub\_klienten\_exempel\_http** för ett komplett exempel på lägre nivå API: er. Den **iothub\_klienten\_exempel\_amqp** kan referera till programmet för ett fullständigt exempel av icke - lla API: erna.

## <a name="property-handling"></a>Egenskapen hantering

Så här långt när vi har beskrivit skickar data, har vi refererar till meddelandets brödtext. Anta exempelvis att den här koden:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Det här exemplet skickar ett meddelande till IoT Hub med texten ”Hello World”. IoT Hub kan dock också egenskaper som ska kopplas till varje meddelande. Egenskaperna är namn/värde-par som kan bifogas i meddelandet. Vi kan till exempel ändra den tidigare koden om du vill bifoga en egenskap i meddelandet:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Vi börjar genom att anropa **IoTHubMessage\_egenskaper** och skicka referensen till vårt meddelande. Vad vi får tillbaka är en **KARTAN\_hantera** referens som gör att vi kan börja lägga till egenskaper. Åstadkoms genom att anropa **kartan\_AddOrUpdate**, som tar en referens till en KARTA\_REFERENSEN och egenskapsnamnet egenskapsvärdet. Med detta API kan vi lägga till så många egenskaper som vi vill.

När händelsen läses från **Händelsehubbar**, mottagaren kan räkna upp egenskaper och hämta motsvarande värden. Till exempel i .NET detta kan uppnås genom att öppna den [egenskapssamlingen EventData-objektet](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

I exemplet ovan vi kopplar egenskaper till en händelse som vi skickar till IoT Hub. Egenskaper kan också kopplas till meddelanden från IoT Hub. Om vi vill hämta egenskaper från ett meddelande kan vi använda koden, till exempel följande i vårt meddelande återanropsfunktion:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

Anropet till **IoTHubMessage\_egenskaper** returnerar den **KARTAN\_hantera** referens. Vi sedan skicka som refererar till **kartan\_GetInternals** att hämta en referens till en matris med namn/värde-par (samt ett antal egenskaper). I det här läget är det bara några enkla räkna upp egenskaperna för att komma till värdena som vi vill.

Du behöver använda egenskaper i ditt program. Men om du behöver ange dem på händelser eller hämta dem från meddelanden, den **IoTHubClient** biblioteket är det enkelt.

## <a name="message-handling"></a>Meddelandehantering

Som nämnts tidigare när meddelanden tas emot från IoT Hub den **IoTHubClient** biblioteket svarar genom att aktivera en registrerad återanropsfunktion. Det finns en returparameter av den här funktionen som är värd att vissa ytterligare förklaring. Här är ett utdrag ur återanropsfunktion i den **iothub\_klienten\_exempel\_http** exempelprogrammet:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Observera att returtypen **IOTHUBMESSAGE\_DISPOSITION\_resultatet** och i detta fall returnerar vi **IOTHUBMESSAGE\_godkända**. Det finns andra värden som vi kan returnera från den här funktionen, som ändrar hur **IoTHubClient** biblioteket reagerar på meddelandets återanrop. Här är alternativen.

* **IOTHUBMESSAGE\_godkända** – meddelandet har bearbetats. Den **IoTHubClient** biblioteket anropar inte återanropsfunktion igen med samma meddelande.

* **IOTHUBMESSAGE\_Avvisad** – meddelandet bearbetades inte och det finns inga önskan att göra det i framtiden. Den **IoTHubClient** biblioteket ska inte anropa Återanropsfunktionen igen med samma meddelande.

* **IOTHUBMESSAGE\_ABANDONED** – meddelandet kunde inte bearbetas, men **IoTHubClient** biblioteket ska anropa återanropsfunktion igen med samma meddelande.

För de första två returkoder den **IoTHubClient** biblioteket skickar ett meddelande till IoT Hub som anger att meddelandet har tagits bort från enheten kön och levereras inte igen. Nettoeffekten är samma (meddelandet tas bort från kön enhet), men om meddelandet godkännas eller avvisas registreras fortfarande.  Spela in denna skillnad är bra att avsändare för meddelandet som kan lyssna efter feedback och ta reda på om en enhet har godkännas eller avvisas ett visst meddelande.

För det sista fallet skickas även ett meddelande till IoT Hub, men det anger att meddelandet ska levereras. Normalt ska du lämna ett meddelande om du stöter på ett fel men vill prova att bearbeta meddelandet igen. Däremot är avvisar ett meddelande som lämplig när det uppstår ett oåterkalleligt fel (eller om du helt enkelt välja att du inte vill att bearbeta meddelandet).

Dock vara medveten om de olika returkoderna så att du kan framkalla önskat beteende från de **IoTHubClient** biblioteket.

## <a name="alternate-device-credentials"></a>Alternativa enhetens autentiseringsuppgifter

Enligt beskrivningen ovan, det första du ska göra när du arbetar med den **IoTHubClient** biblioteket är att hämta en **IOTHUB\_klienten\_hantera** med ett anrop till exempel följande:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Argument till **IoTHubClient\_CreateFromConnectionString** är enhetens anslutningssträng och en parameter som anger det protokoll som vi använder för att kommunicera med IoT Hub. Enhetens anslutningssträng har ett format som visas på följande sätt:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Det finns fyra typer av information i den här strängen: Namnet på IoT-hubb, IoT Hub-suffix, enhets-ID och nyckel för delad åtkomst. Du hämta det fullständigt kvalificerade domännamnet (FQDN) för en IoT-hubb när du skapar din IoT hub-instansen i Azure portal – detta ger dig IoT hub-namn (den första delen av det fullständiga Domännamnet) och IoT hub-suffix (resten av det fullständiga Domännamnet). Du får enhets-ID och nyckel för delad åtkomst när du registrerar din enhet med IoT Hub (enligt beskrivningen i den [föregående artikel](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** ger dig ett sätt att initiera i biblioteket. Om du vill kan du skapa en ny **IOTHUB\_klienten\_hantera** med hjälp av dessa enskilda parametrar i stället för enhetens anslutningssträng. Detta uppnås med följande kod:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Detta åstadkommer samma sak som **IoTHubClient\_CreateFromConnectionString**.

Det kan verka uppenbart att du kan använda **IoTHubClient\_CreateFromConnectionString** i stället för den här mer utförlig metoden av initieringen. Kom dock ihåg att när du registrerar en enhet i IoT Hub vad du får är ett enhets-ID och enhetsnyckel (inte en anslutningssträng). Den *enhetsutforskare* SDK-verktyg som introducerades i den [föregående artikel](iot-hub-device-sdk-c-intro.md) använder biblioteken i de **Azure IoT service SDK** skapa enhetens anslutningssträng från enhets-ID , enhetsnyckel och IoT Hub-värdnamnet. Så att anropa **IoTHubClient\_lla\_skapa** kan det vara bättre eftersom du steg att generera en anslutningssträng. Använda oavsett vilken metod som är lämpligt.

## <a name="configuration-options"></a>Konfigurationsalternativ

Så här långt allt beskrivs om hur den **IoTHubClient** biblioteket fungerar visar dess standardbeteende. Det finns dock några alternativ som du kan ange för att ändra hur det fungerar i biblioteket. Detta åstadkoms genom att utnyttja den **IoTHubClient\_lla\_SetOption** API. Överväg det här exemplet:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Det finns ett par alternativ som är vanliga:

* **SetBatching** (bool) – om **SANT**, och sedan data som skickas till IoT Hub skickas i batchar. Om **FALSKT**, och sedan skickas ett individuellt. Standardvärdet är **FALSKT**. Observera att den **SetBatching** alternativet gäller endast HTTPS-protokollet och inte MQTT eller AMQP-protokoll.

* **Tidsgräns för** (osignerade int) – det här värdet anges i millisekunder. Om du skickar en HTTPS-begäran eller ta emot ett svar tar längre tid än den angivna tiden och sedan tidsgränsen uppnås för anslutningen.

Alternativet batchbearbetningen är viktigt. Som standard biblioteket ingresses händelser individuellt (en enskild händelse är vad du skickar till **IoTHubClient\_lla\_SendEventAsync**). Om alternativet batchbearbetningen är **SANT**, biblioteket samlar in så många händelser som möjligt från bufferten (upp till den maximala meddelandestorleken som accepterar IoT Hub).  Batch händelse skickas till IoT Hub i ett enda HTTPS-anrop (enskilda händelser slås ihop till en JSON-matris). Aktivera batchbearbetning vanligtvis resulterar i stora prestandavinster eftersom du är att minska nätverk turer. Det minskar också avsevärt bandbredd eftersom du skickar en uppsättning HTTPS-huvuden med en händelse batch i stället för en uppsättning rubriker för varje enskild händelse. Om du inte har en särskild anledning att göra något annat, bör vanligtvis du aktivera Batchbearbetning.

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs i detalj hur den **IoTHubClient** biblioteket finns i den **Azure IoT-enhetens SDK för C**. Med den här informationen bör du ha en god förståelse av funktionerna i den **IoTHubClient** biblioteket. Den andra artikeln i den här serien är [Azure IoT-enhetens SDK för C - serialiseraren](iot-hub-device-sdk-c-serializer.md), som innehåller liknande information på den **serialiserare** biblioteket.

Mer information om hur du utvecklar för IoT Hub finns det [Azure IoT SDK: er](iot-hub-devguide-sdks.md).

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se [distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).