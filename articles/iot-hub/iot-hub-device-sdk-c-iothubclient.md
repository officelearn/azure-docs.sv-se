---
title: Azure IoT-enhet SDK för C - IoTHubClient | Microsoft-dokument
description: Så här använder du IoTHubClient-biblioteket i Azure IoT-enheten SDK för C för att skapa enhetsappar som kommunicerar med en IoT-hubb.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: robinsh
ms.openlocfilehash: fd3e02101f206ebdb183da87089eadcbc9619b33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883180"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Azure IoT-enhet SDK för C – mer om IoTHubClient

[Azure IoT-enhet SDK för C](iot-hub-device-sdk-c-intro.md) är den första artikeln i den här serien som introducerar **Azure IoT-enheten SDK för C**. I den artikeln förklaras att det finns två arkitektoniska lager i SDK. Vid basen finns **IoTHubClient-biblioteket** som direkt hanterar kommunikation med IoT Hub. Det finns också **serializer** biblioteket som bygger på toppen av att tillhandahålla serialisering tjänster. I den här artikeln kommer vi att ge ytterligare information om **IoTHubClient-biblioteket.**

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

I föregående artikel beskrivs hur du använder **IoTHubClient-biblioteket** för att skicka händelser till IoT Hub och ta emot meddelanden. Den här artikeln utökar den diskussionen genom att förklara hur du mer exakt hanterar *när* du skickar och ta emot data, vilket introducerar dig till **api:erna på lägre nivå**. Vi förklarar också hur du kopplar egenskaper till händelser (och hämtar dem från meddelanden) med hjälp av egenskapshanteringsfunktionerna i **IoTHubClient-biblioteket.** Slutligen ger vi ytterligare förklaring av olika sätt att hantera meddelanden som tagits emot från IoT Hub.

Artikeln avslutas med att täcka ett par diverse ämnen, inklusive mer om enhetsautentiseringsuppgifter och hur du ändrar beteendet hos **IoTHubClient** genom konfigurationsalternativ.

Vi använder **IoTHubClient** SDK-exemplen för att förklara dessa ämnen. Om du vill följa med **iothub-klientexemplet\_\_\_http** och **iothub-klientexempel\_\_\_amqp-program** som ingår i Azure IoT-enheten SDK för C. Allt som beskrivs i följande avsnitt visas i dessa exempel.

Du hittar [**Azure IoT-enheten SDK för C**](https://github.com/Azure/azure-iot-sdk-c) GitHub-databasen och visa information om API i [C API-referensen](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-lower-level-apis"></a>Api:erna på lägre nivå

I föregående artikel beskrevs den grundläggande driften av **IotHubClient** inom ramen för **iothub-klientens\_\_exempel\_amqp-program.** Till exempel förklarades hur du initierar biblioteket med den här koden.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Den beskrev också hur du skickar händelser med det här funktionsanropet.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

I artikeln beskrivs också hur du får meddelanden genom att registrera en motringningsfunktion.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Artikeln visade också hur man frigör resurser med hjälp av kod som följande.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

Det finns kompletterande funktioner för var och en av dessa API:er:

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_\_LL Förstör

Dessa funktioner inkluderar alla **LL** i API-namnet. Andra **LL-delen** av namnet, parametrarna för var och en av dessa funktioner är identiska med sina icke-LL motsvarigheter. Beteendet hos dessa funktioner är dock annorlunda på ett viktigt sätt.

När du anropar **IoTHubClient\_CreateFromConnectionString**skapar de underliggande biblioteken en ny tråd som körs i bakgrunden. Den här tråden skickar händelser till och tar emot meddelanden från IoT Hub. Ingen sådan tråd skapas när du arbetar med **LL** API:er. Skapandet av bakgrundstråden är en bekvämlighet för utvecklaren. Du behöver inte oroa dig för att uttryckligen skicka händelser och ta emot meddelanden från IoT Hub – det sker automatiskt i bakgrunden. Ll **API:er** ger dig däremot tydlig kontroll över kommunikationen med IoT Hub, om du behöver den.

För att förstå detta begrepp bättre, låt oss titta på ett exempel:

När du ringer **IoTHubClient\_SendEventAsync**är det du faktiskt gör att du placerar händelsen i en buffert. Bakgrundstråden som skapas när du anropar **IoTHubClient\_CreateFromConnectionString** övervakar kontinuerligt den här bufferten och skickar alla data som den innehåller till IoT Hub. Detta händer i bakgrunden samtidigt som huvudtråden utför annat arbete.

När du registrerar en motringningsfunktion för meddelanden med **IoTHubClient\_SetMessageCallback**instruerar du SDK att ha bakgrundstråden anropa motringningsfunktionen när ett meddelande tas emot, oberoende av huvudtråden.

**LL-API:erna** skapar ingen bakgrundstråd. I stället måste ett nytt API anropas för att uttryckligen skicka och ta emot data från IoT Hub. Detta visas i följande exempel.

**Iothub-klientexempelet\_\_\_http** som ingår i SDK visar api:erna på lägre nivå. I det exemplet skickar vi händelser till IoT Hub med kod som följande:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

De tre första raderna skapar meddelandet och den sista raden skickar händelsen. Men som tidigare nämnts innebär att skicka händelsen att data helt enkelt placeras i en buffert. Ingenting överförs i nätverket när vi anropar **IoTHubClient\_LL\_SendEventAsync**. För att faktiskt kunna gå in på data till IoT Hub måste du anropa **IoTHubClient\_\_LL DoWork**, som i det här exemplet:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Den här koden (från **iothub-klientexempelet\_\_\_http-programmet)** anropar upprepade gånger **IoTHubClient\_LL\_DoWork**. Varje gång **IoTHubClient\_\_LL DoWork** anropas skickas vissa händelser från bufferten till IoT Hub och ett köat meddelande skickas till enheten. Det senare fallet innebär att om vi registrerade en motringningsfunktion för meddelanden, anropas motringningen (förutsatt att alla meddelanden köas). Vi skulle ha registrerat en sådan motringningsfunktion med kod som följande:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Anledningen till att **IoTHubClient\_\_LL DoWork** ofta anropas i en loop är att varje gång den anropas skickas *vissa* buffrade händelser till IoT Hub och *hämtar nästa* meddelande som står i kö för enheten. Varje samtal är inte garanterat att skicka alla buffrade händelser eller att hämta alla meddelanden i kö. Om du vill skicka alla händelser i bufferten och sedan fortsätta med annan bearbetning kan du ersätta den här loopen med kod som följande:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Den här koden anropar **IoTHubClient\_\_LL DoWork** tills alla händelser i bufferten har skickats till IoT Hub. Observera att detta inte också innebär att alla köade meddelanden har tagits emot. En del av orsaken till detta är att söka efter "alla" meddelanden är inte lika deterministisk en åtgärd. Vad händer om du hämtar "alla" av meddelandena, men sedan skickas en annan till enheten direkt efter? Ett bättre sätt att hantera det är med en programmerad timeout. Meddelandeåtringsfunktionen kan till exempel återställa en timer varje gång den anropas. Du kan sedan skriva logik för att fortsätta bearbetningen om till exempel inga meddelanden har tagits emot under de senaste *X* sekunderna.

När du är klar med inträngningshändelser och ta emot meddelanden måste du ringa motsvarande funktion för att rensa resurser.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

I grund och botten finns det bara en uppsättning API:er för att skicka och ta emot data med en bakgrundstråd och en annan uppsättning API:er som gör samma sak utan bakgrundstråden. Många utvecklare kanske föredrar api:er som inte är LL, men API:erna på lägre nivå är användbara när utvecklaren vill ha explicit kontroll över nätverksöverföringar. Vissa enheter samlar till exempel in data över tid och endast inträngningshändelser med angivna intervall (till exempel en gång i timmen eller en gång om dagen). Api:erna på lägre nivå ger dig möjlighet att uttryckligen styra när du skickar och ta emot data från IoT Hub. Andra kommer helt enkelt att föredra den enkelhet som de lägre API:erna ger. Allt händer på huvudtråden snarare än en del arbete som händer i bakgrunden.

Oavsett vilken modell du väljer, se till att vara konsekvent i vilka API:er du använder. Om du börjar med att anropa **IoTHubClient\_LL\_CreateFromConnectionString**måste du bara använda motsvarande API:er på lägre nivå för uppföljningsarbete:

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_\_LL Förstör
* IoTHubClient\_LL\_DoWork

Det motsatta gäller också. Om du börjar med **IoTHubClient\_CreateFromConnectionString**använder du API:erna som inte är LL för ytterligare bearbetning.

I Azure IoT-enheten SDK för C läser du **\_iothub-klientexemplet\_\_http-programmet** för ett fullständigt exempel på API:er på lägre nivå. **Iothub-klientens\_\_\_exempelamqp-program** kan refereras till för ett fullständigt exempel på API:er som inte är LL.

## <a name="property-handling"></a>Fastighetshantering

Hittills när vi har beskrivit att skicka data, har vi hänvisat till brödtexten i meddelandet. Tänk dig till exempel den här koden:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Det här exemplet skickar ett meddelande till IoT Hub med texten "Hello World". IoT Hub gör det dock också möjligt att koppla egenskaper till varje meddelande. Egenskaper är namn-/värdepar som kan kopplas till meddelandet. Vi kan till exempel ändra den tidigare koden för att koppla en egenskap till meddelandet:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Vi börjar med att anropa **\_IoTHubMessage Properties** och skicka det handtaget på vårt meddelande. Vad vi får tillbaka är en **MAP\_HANDLE** referens som gör det möjligt för oss att börja lägga till egenskaper. Det senare åstadkoms genom att anropa **Map\_AddOrUpdate**, som tar en referens till en MAP\_HANDLE, egenskapsnamnet och egenskapsvärdet. Med detta API kan vi lägga till så många egenskaper som vi vill.

När händelsen läs **avstÃ¤r**från Event Hubs kan mottagaren räkna upp egenskaperna och hämta motsvarande värden. I .NET skulle detta till exempel åstadkommas genom att åtkomst till [egenskapssamlingen i EventData-objektet](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

I föregående exempel kopplar vi egenskaper till en händelse som vi skickar till IoT Hub. Egenskaper kan också kopplas till meddelanden som tagits emot från IoT Hub. Om vi vill hämta egenskaper från ett meddelande kan vi använda kod som följande i vår meddelandeåterringsfunktion:

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

Anropet till **IoTHubMessage-egenskaperna\_** returnerar **MAP HANDLE-referensen.\_** Vi skickar sedan den referensen till **Map\_GetInternals** för att få en referens till en matris med namn/värdepar (samt en räkning av egenskaperna). Då är det en enkel sak att räkna upp egenskaperna för att komma till de värden vi vill ha.

Du behöver inte använda egenskaper i ditt program. Men om du behöver ställa in dem på händelser eller hämta dem från meddelanden, gör **IoTHubClient-biblioteket** det enkelt.

## <a name="message-handling"></a>Meddelandehantering

Som tidigare nämnts, när meddelanden anländer från IoT Hub svarar **IoTHubClient-biblioteket** genom att anropa en registrerad motringningsfunktion. Det finns en returparameter för den här funktionen som förtjänar ytterligare förklaring. Här är ett utdrag av motringningsfunktionen i **\_iothub-klientens\_\_exempel http** exempelprogram:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Observera att returtypen är **IOTHUBMESSAGE\_\_DISPOSITION RESULTAT** och i detta fall returnerar vi **IOTHUBMESSAGE\_ACCEPTERAS**. Det finns andra värden som vi kan returnera från den här funktionen som ändrar hur **IoTHubClient-biblioteket** reagerar på meddelandets motringning. Här är alternativen.

* **IOTHUBMESSAGE\_ACCEPTERAD** – Meddelandet har bearbetats. **IoTHubClient-biblioteket** anropar inte motringningsfunktionen igen med samma meddelande.

* **IOTHUBMESSAGE\_AVVISADE** – Meddelandet behandlades inte och det finns ingen önskan att göra det i framtiden. **IoTHubClient-biblioteket** bör inte anropa motringningsfunktionen igen med samma meddelande.

* **IOTHUBMESSAGE\_ÖVERGIVEN** – Meddelandet har inte bearbetats, men **IoTHubClient-biblioteket** bör anropa motringningsfunktionen igen med samma meddelande.

För de två första returkoderna skickar **IoTHubClient-biblioteket** ett meddelande till IoT Hub som anger att meddelandet ska tas bort från enhetskön och inte levereras igen. Nettoeffekten är densamma (meddelandet tas bort från enhetskön), men om meddelandet accepterades eller avvisades registreras fortfarande.  Att spela in den här skillnaden är användbart för avsändare av meddelandet som kan lyssna efter feedback och ta reda på om en enhet har accepterat eller avvisat ett visst meddelande.

I det sista fallet skickas ett meddelande också till IoT Hub, men det anger att meddelandet ska levereras om. Vanligtvis överger du ett meddelande om du stöter på något fel men vill försöka bearbeta meddelandet igen. Att avvisa ett meddelande är däremot lämpligt när du stöter på ett oåterkalleligt fel (eller om du helt enkelt bestämmer dig för att du inte vill bearbeta meddelandet).

Var i vilket fall som helst medveten om de olika returkoderna så att du kan framkalla det beteende du vill ha från **IoTHubClient-biblioteket.**

## <a name="alternate-device-credentials"></a>Alternativa enhetsautentiseringsuppgifter

Som tidigare förklarats är det första du ska göra när du arbetar med **IoTHubClient-biblioteket** att skaffa en **IOTHUB-klientreferens\_\_** med ett anrop som följande:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Argumenten till **IoTHubClient\_CreateFromConnectionString** är enhetsanslutningssträngen och en parameter som anger det protokoll vi använder för att kommunicera med IoT Hub. Enhetsanslutningssträngen har ett format som visas på följande sätt:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Det finns fyra informationsdelar i den här strängen: IoT Hub-namn, IoT Hub-suffix, enhets-ID och delad åtkomstnyckel. Du får det fullständigt kvalificerade domännamnet (FQDN) för en IoT-hubb när du skapar din IoT-hubbin i Azure-portalen – detta ger dig IoT-hubbnamnet (den första delen av FQDN) och IoT-hubbsuffixet (resten av FQDN). Du får enhets-ID och delad åtkomstnyckel när du registrerar enheten med IoT Hub (som beskrivs i [föregående artikel).](iot-hub-device-sdk-c-intro.md)

**IoTHubClient\_CreateFromConnectionString** ger dig ett sätt att initiera biblioteket. Om du vill kan du skapa en ny **IOTHUB-klientreferens\_\_** med hjälp av dessa enskilda parametrar i stället för enhetsanslutningssträngen. Detta uppnås med följande kod:

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

Det kan verka uppenbart att du vill använda **IoTHubClient\_CreateFromConnectionString** snarare än denna mer utförliga metod för initiering. Tänk dock på att när du registrerar en enhet i IoT Hub vad du får är ett enhets-ID och enhetsnyckel (inte en anslutningssträng). *SDK-verktyget för enhetsutforskaren* som introducerades i [föregående artikel](iot-hub-device-sdk-c-intro.md) använder bibliotek i **Azure IoT-tjänsten SDK** för att skapa enhetsanslutningssträngen från enhets-ID:n, enhetsnyckeln och IoT Hub-värdnamnet. Så ringa **IoTHubClient\_\_LL Skapa** kan vara att föredra eftersom det sparar dig steget att generera en anslutningssträng. Använd vilken metod som är praktisk.

## <a name="configuration-options"></a>Konfigurationsalternativ

Hittills allt som beskrivs om hur **IoTHubClient** biblioteket fungerar återspeglar dess standardbeteende. Det finns dock några alternativ som du kan ange för att ändra hur biblioteket fungerar. Detta åstadkoms genom att utnyttja **IoTHubClient\_\_LL SetOption** API. Tänk på det här exemplet:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Det finns ett par alternativ som ofta används:

* **SetBatching** (bool) – Om **sant**skickas data som skickas till IoT Hub i batchar. Om **det är falskt**skickas meddelanden individuellt. Standardvärdet är **falskt**. Batching över AMQP / AMQP-WS, samt lägga till systemegenskaper på D2C-meddelanden, stöds.

* **Timeout** (osignerad int) – Det här värdet representeras i millisekunder. Om det tar längre tid att skicka en HTTPS-begäran eller ta emot ett svar än den här gången, så tar anslutningen time out.

Batching-alternativet är viktigt. Som standard inträder biblioteket händelser individuellt (en enda händelse är vad du skickar till **IoTHubClient\_LL\_SendEventAsync**). Om batchningsalternativet är **sant**samlar biblioteket in så många händelser som det kan från bufferten (upp till den maximala meddelandestorleken som IoT Hub accepterar).  Händelsebatchen skickas till IoT Hub i ett enda HTTPS-anrop (de enskilda händelserna buntas in i en JSON-matris). Om du aktiverar batchbearbetning leder det vanligtvis till stora prestandavinster eftersom du minskar nätverksrundningsresor. Det minskar också bandbredden avsevärt eftersom du skickar en uppsättning HTTPS-huvuden med en händelsebatch i stället för en uppsättning rubriker för varje enskild händelse. Om du inte har en specifik anledning att göra något annat, vanligtvis vill du aktivera batchbearbetning.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs i detalj beteendet för **IoTHubClient-biblioteket** som finns i **Azure IoT-enheten SDK för C**. Med den här informationen bör du ha en god förståelse för funktionerna i **IoTHubClient-biblioteket.** Den andra artikeln i den här serien är [Azure IoT-enheten SDK för C - Serializer](iot-hub-device-sdk-c-serializer.md), som innehåller liknande detaljer på **serialiserarbiblioteket.**

Mer information om hur du utvecklar för IoT Hub finns i [Azure IoT SDK:er](iot-hub-devguide-sdks.md).

Mer information om hur du utforskar funktionerna i IoT Hub finns i [Distribuera AI till kantenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
