---
title: Azure IoT-enhet SDK för C - IoTHubClient | Microsoft Docs
description: Hur du använder IoTHubClient biblioteket i Azure IoT-enhet SDK för C för att skapa appar för enheter som kommunicerar med en IoT-hubb.
services: iot-hub
documentationcenter: ''
author: yzhong94
manager: timlt
editor: ''
ms.assetid: 828cf2bf-999d-4b8a-8a28-c7c901629600
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: yizhon
ms.openlocfilehash: 0fa07b2a785bd7d1a26924918e4428fd420e1547
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Azure IoT-enhet SDK för C – mer information om IoTHubClient
Den [först artikel](iot-hub-device-sdk-c-intro.md) i den här serien introduceras de **Azure IoT-enhet SDK för C**. Artikeln förklaras att det finns två arkitektur lager i SDK. I grunden är den **IoTHubClient** bibliotek som hanterar direkt kommunikation med IoT-hubb. Det finns också i **serialiseraren** bibliotek som bygger som för att tillhandahålla tjänster för serialisering. I den här artikeln ska vi ge ytterligare information om den **IoTHubClient** bibliotek.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Föregående artikel beskrivs hur du använder den **IoTHubClient** biblioteket för att skicka händelser till IoT-hubb och ta emot meddelanden. Den här artikeln utökar den beskrivning som förklarar hur du hanterar mer exakt *när* du skicka och ta emot data, introducerar du den **lågnivå-API: er**. Vi förklarar också hur du ansluter egenskaper på händelser (och hämta dem från meddelanden) med hjälp av egenskapen hantering av funktioner i den **IoTHubClient** bibliotek. Slutligen ska vi ger ytterligare förklaring av olika sätt att hantera meddelanden som tagits emot från IoT-hubb.

Artikeln avslutas genom som omfattar ett antal olika ämnen, t.ex. mer om autentiseringsuppgifter för enheten och hur du ändrar beteendet för den **IoTHubClient** via konfigurationsalternativ.

Vi använder den **IoTHubClient** SDK-exempel som förklarar dessa avsnitt. Om du vill följa med finns i **iothub\_klienten\_exempel\_http** och **iothub\_klienten\_exempel\_amqp**program som ingår i Azure IoT-enhet SDK för C. allt beskrivs i följande avsnitt visas i exemplen.

Du hittar den [ **Azure IoT-enhet SDK för C** ](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen och visa information om API i den [C API-referens för](https://azure.github.io/azure-iot-sdk-c/index.html).

## <a name="the-lower-level-apis"></a>Lågnivå-API: er
Föregående artikel beskrivs grundläggande funktioner i den **IotHubClient** inom ramen för den **iothub\_klienten\_exempel\_amqp** program. Till exempel förklaras den hur du initierar biblioteket med följande kod.

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Det beskrivs även hur du skickar händelser med hjälp av den här funktionsanrop.

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Artikeln beskrivs även ta emot meddelanden genom att registrera en callback-funktion.

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Artikeln visade också hur du frigör resurser med hjälp av koden till exempel följande.

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Det finns dock tillhörande funktioner för var och en av dessa API: er:

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

Dessa funktioner som alla inkludera ”lla” i API-namnet. Parametrarna för var och en av dessa funktioner är identiska med deras motsvarigheter i icke-lla än det som. Beteendet för dessa funktioner är dock olika på ett sätt som viktiga.

När du anropar **IoTHubClient\_CreateFromConnectionString**, underliggande bibliotek skapa en ny tråd som körs i bakgrunden. Den här tråden skickar händelser till och tar emot meddelanden från IoT-hubb. Ingen sådan tråd skapas när du arbetar med ”lla” API: er. Bakgrundstråden skapas i syfte att underlätta till utvecklaren. Du behöver inte bry dig om uttryckligen att skicka händelser och ta emot meddelanden från IoT-hubb – det sker automatiskt i bakgrunden. Däremot ger ”lla” API: er dig explicit kontroll över kommunikation med IoT-hubb, om det behövs.

För att förstå det bättre ska vi titta på ett exempel:

När du anropar **IoTHubClient\_SendEventAsync**, vad du faktiskt gör är att placera händelsen i en buffert. Bakgrundstråden som skapas när du anropar **IoTHubClient\_CreateFromConnectionString** kontinuerligt övervakar bufferten och skickar data som den innehåller IoT-hubben. Detta sker i bakgrunden på samma gång huvudtråden utförs annat arbete.

På liknande sätt, när du registrerar en Återanropsfunktionen för meddelanden med **IoTHubClient\_SetMessageCallback**, du instruerar SDK om du vill ha bakgrundstråd anropa Återanropsfunktionen när ett meddelande är mottagna, oberoende av huvudtråden.

”DU” API: er skapa inte en bakgrundstråd. I stället måste ett nytt API anropas för att skicka och ta emot data från IoT-hubb explicit. Detta visas i följande exempel.

Den **iothub\_klienten\_exempel\_http** program som ingår i SDK visar på lägre nivå-API: er. I det här exemplet skicka vi händelser till IoT-hubb med kod till exempel följande:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

De tre första raderna skapa meddelandet och den sista raden skickar händelsen. Dock som tidigare nämnts skicka ”” händelsen innebär att data bara placeras i en buffert. Ingenting överförs i nätverket när vi kallar **IoTHubClient\_lla\_SendEventAsync**. Du måste anropa för att faktiskt ingång data till IoT-hubb **IoTHubClient\_lla\_DoWork**, som i det här exemplet:

```
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Den här koden (från den **iothub\_klienten\_exempel\_http** program) upprepade gånger **IoTHubClient\_lla\_DoWork**. Varje gång **IoTHubClient\_lla\_DoWork** är anropas den skickar vissa händelser från bufferten för IoT-hubb och hämtar en köade meddelandet som skickas till enheten. Det senare fallet innebär att om vi registrerat ett Återanropsfunktionen för meddelanden, sedan återanropet anropas (förutsatt att alla meddelanden i kö). Vi skulle har registrerat en Återanropsfunktionen med kod till exempel följande:

```
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Orsaken som **IoTHubClient\_lla\_DoWork** kallas ofta en slinga är att varje gång den anropas, skickar *vissa* buffras händelser som IoT-hubb och hämtar *nästa* meddelandet i kö för enheten. Varje anrop är inte säkert att skicka alla buffertlagrade händelser eller om du vill hämta alla meddelanden i kön. Om du vill skicka alla händelser i bufferten och fortsätt sedan med annan bearbetning ersätta du den här loop med kod till exempel följande:

```
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Den här koden anropar **IoTHubClient\_lla\_DoWork** tills alla händelser i bufferten har skickats till IoT-hubb. Observera att detta inte också innebär att alla köade meddelanden har tagits emot. En del av anledningen till detta är att kontrollera om ”alla” meddelanden inte deterministisk som en åtgärd. Vad händer om du hämtar ”alla” meddelanden, men en annan skickas sedan till enheten omedelbart efter? Ett bättre sätt att åtgärda som med programmerade timeout. Återanropsfunktionen meddelande kunde återställa en timer varje gång den startas. Du kan sedan skriva logik för att fortsätta bearbetningen om till exempel inga meddelanden har tagits emot under senaste *X* sekunder.

När du är klar ingressing händelser och ta emot meddelanden, måste du anropa funktionen motsvarande Rensa resurser.

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Det finns i princip bara en uppsättning API: er för att skicka och ta emot data med en bakgrundstråd och en annan uppsättning API: er som gör samma sak utan bakgrundstråden. Många utvecklare kan också icke - lla API: erna, men på lägre nivå-API: er är användbara när utvecklare vill explicit kontroll över nätverksöverföringar. Till exempel vissa enheter samla in data över tid och endast ingångshändelser vid angivna intervall (till exempel när en timme eller en gång om dagen). Lågnivå-API: er ger dig möjlighet att styra explicit när du skickar och tar emot data från IoT-hubb. Andra kommer bara att föredra enkelheten med lägre nivå-API: er. Allt som händer på huvudtråden i stället för vissa arbete sker i bakgrunden.

Oavsett vilken modell som du väljer måste du ha ett enhetligt vilka API: er som du använder. Om du startar genom att anropa **IoTHubClient\_lla\_CreateFromConnectionString**, bör du bara använda API: er för motsvarande på lägre nivå för all uppföljning:

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy
* IoTHubClient\_LL\_DoWork

Motsatt gäller också. Om du börjar med **IoTHubClient\_CreateFromConnectionString**, sedan använda icke - lla API: erna för några andra processer.

I Azure IoT enheten SDK för C, visas den **iothub\_klienten\_exempel\_http** för en komplett exempel på lägre nivå-API: er. Den **iothub\_klienten\_exempel\_amqp** program kan refereras till en fullständig exempel av icke - lla API: erna.

## <a name="property-handling"></a>Egenskapen hantering
Hittills när vi har beskrivs skickar data, har vi hänvisar till postmeddelandets brödtext. Anta till exempel att den här koden:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Det här exemplet skickar ett meddelande till IoT-hubb med texten ”Hello World”. IoT-hubb kan också egenskaper som ska kopplas till varje meddelande. Egenskaperna är namn/värde-par som kan bifogas i meddelandet. Vi kan till exempel ändra föregående kod om du vill bifoga en egenskap i meddelandet:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Vi börjar genom att anropa **IoTHubMessage\_egenskaper** och passerar den referensen till vår meddelandet. Vad vi komma är en **KARTAN\_hantera** referens som gör att vi kan börja lägga till egenskaper. Görs genom att anropa **kartan\_AddOrUpdate**, som tar en referens till en KARTA\_REFERENSEN och egenskapsnamnet egenskapens värde. Vi kan detta API för att lägga till alla egenskaper som vi gärna.

När händelsen läses från **Händelsehubbar**, mottagaren kan räkna upp egenskaper och hämta motsvarande värden. Till exempel i .NET detta kan åstadkommas genom att öppna den [egenskapssamlingen på objektet EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

I exemplet ovan vi kopplar egenskaper till en händelse som vi skickar till IoT-hubb. Egenskaper kan också kopplas till meddelanden från IoT-hubb. Om vi vill hämta egenskaper från ett meddelande använda vi kod exempelvis följande i vår Återanropsfunktionen meddelande:

```
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

Anropet till **IoTHubMessage\_egenskaper** returnerar den **KARTAN\_hantera** referens. Vi skickar sedan som refererar till **kartan\_GetInternals** att hämta en referens till en matris med namn/värde-par (och även ett antal egenskaper). Då är det en enkel fråga om att egenskaperna för att hämta värden som vi vill använda.

Du behöver använda egenskaper i ditt program. Men om du behöver ange dem på händelser eller hämta dem från meddelanden, den **IoTHubClient** biblioteket kan du enkelt.

## <a name="message-handling"></a>Meddelandehantering
Som nämnts tidigare anger när meddelanden tas emot från IoT-hubb i **IoTHubClient** biblioteket svarar genom att aktivera registrerade Återanropsfunktionen. Det finns en returparameter av den här funktionen som ska ha vissa ytterligare förklaring. Här är ett utdrag ur Återanropsfunktionen i den **iothub\_klienten\_exempel\_http** exempelprogrammet:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Observera att returtypen **IOTHUBMESSAGE\_DISPOSITION\_resultatet** och i detta fall returnerar vi **IOTHUBMESSAGE\_godkända**. Det finns andra värden returnerar vi från den här funktionen som ändrar hur **IoTHubClient** biblioteket reagerar på meddelandet återanrop. Här är alternativen.

* **IOTHUBMESSAGE\_godkända** – meddelandet har bearbetats. Den **IoTHubClient** biblioteket kommer inte att anropa Återanropsfunktionen igen med samma meddelande.
* **IOTHUBMESSAGE\_Avvisad** – meddelandet kunde inte bearbetas och det finns inga önskan att göra det i framtiden. Den **IoTHubClient** biblioteket ska inte anropa Återanropsfunktionen igen med samma meddelande.
* **IOTHUBMESSAGE\_ABANDONED** – meddelandet har inte bearbetats, men **IoTHubClient** biblioteket ska anropa Återanropsfunktionen igen med samma meddelande.

För de första två returkoder den **IoTHubClient** biblioteket skickar ett meddelande till IoT-hubb som anger att meddelandet ska tas bort från kön enhet och inte levereras igen. Net effekten är samma (meddelandet tas bort från kön enhet), men om meddelandet godkännas eller avvisas registreras fortfarande.  Registrera denna skillnad är bra att avsändare av meddelandet som kan lyssna efter feedback och ta reda på om en enhet har godkänt eller avvisade ett visst meddelande.

I det senaste fallet skickas även ett meddelande till IoT-hubb, men det anger att meddelandet ska levereras. Du kommer normalt Avbryt ett meddelande om du påträffar ett fel men vill att försöka bearbeta meddelandet igen. Däremot är avvisa meddelandet lämplig när det uppstår ett oåterkalleligt fel (eller om du bara väljer du inte vill bearbeta meddelandet).

Dock vara medveten om olika returkoder så att du kan framkalla önskat beteende från den **IoTHubClient** bibliotek.

## <a name="alternate-device-credentials"></a>Autentiseringsuppgifter för annan enhet
Enligt beskrivningen tidigare var det första du ska göra när du arbetar med den **IoTHubClient** biblioteket är att hämta en **IOTHUB\_klienten\_hantera** med ett anrop till exempel följande:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Argumenten för **IoTHubClient\_CreateFromConnectionString** är anslutningssträngen enhet och en parameter som anger det protokoll som vi använder för att kommunicera med IoT-hubb. Anslutningssträngen enheten har ett format som visas på följande sätt:

```
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Det finns fyra typer av information i den här strängen: IoT-hubb namn, IoT-hubb suffix, enhets-ID och nyckeln för delad åtkomst. Du hämta det fullständigt kvalificerade domännamnet (FQDN) för en IoT-hubb när du skapar din IoT hub-instans i Azure portal – detta ger dig IoT-hubbnamnet (den första delen av FQDN) och suffixet IoT-hubb (resten av FQDN). Du får enhets-ID och den delade åtkomstnyckeln när du registrerar din enhet med IoT-hubben (som beskrivs i den [föregående artikel](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** ger dig ett sätt att starta biblioteket. Om du vill kan du skapa en ny **IOTHUB\_klienten\_hantera** med hjälp av dessa enskilda parametrar i stället för anslutningssträngen för enheten. Detta uppnås med följande kod:

```
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Detta åstadkommer samma sak som **IoTHubClient\_CreateFromConnectionString**.

Det kan verka uppenbara som du vill använda **IoTHubClient\_CreateFromConnectionString** i stället för den här mer utförlig metoden för initiering. Kom ihåg att när du registrerar en enhet i IoT-hubb vad du får är en enhets-ID och nyckeln för enheten (inte en anslutningssträng). Den *enheten explorer* SDK-verktyg som introducerades i den [föregående artikel](iot-hub-device-sdk-c-intro.md) använder biblioteken i de **Azure IoT service SDK** att skapa anslutningssträngen för enheten från enhets-ID , enhetsnyckel och IoT Hub-värdnamnet. Anropar så **IoTHubClient\_lla\_skapa** kan det vara bättre eftersom du sparar du steg för att generera en anslutningssträng. Använd metoden som är lämplig.

## <a name="configuration-options"></a>Konfigurationsalternativ
Hittills allt beskrivs hur den **IoTHubClient** biblioteket fungerar visar dess standardbeteendet. Det finns dock några alternativ som du kan ange för att ändra hur biblioteket fungerar. Detta görs genom att utnyttja den **IoTHubClient\_lla\_SetOption** API. Överväg att det här exemplet:

```
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Det finns ett par olika alternativ som är vanliga:

* **SetBatching** (booleskt) – om **SANT**, som sedan skickas data som skickas till IoT-hubb i batchar. Om **FALSKT**, och sedan skickas meddelanden individuellt. Standardvärdet är **FALSKT**. Observera att den **SetBatching** alternativet gäller endast HTTPS-protokollet och inte MQTT eller AMQP-protokoll.
* **Timeout** (osignerade int) – det här värdet anges i millisekunder. Om du skickar en HTTPS-begäran eller svar erhålls tar längre tid än den angivna tiden och sedan anslutningen på grund av timeout.

Alternativet batching är viktigt. Som standard biblioteket ingresses händelser individuellt (en händelse är allt du skickar till **IoTHubClient\_lla\_SendEventAsync**). Om alternativet batching är **SANT**, biblioteket samlar in så många händelser som går från bufferten (upp till den maximala storleken som accepterar IoT-hubb).  Batch händelse skickas till IoT-hubb i ett enda HTTPS-anrop (enskilda händelser slås ihop till en JSON-matris). Aktivera batchbearbetning vanligtvis resulterar i stora prestandavinster eftersom du är att minska nätverket turer. Det minskar också avsevärt bandbredd eftersom du skickar en uppsättning av HTTPS-huvuden med en grupp med händelsen i stället för en uppsättning huvuden för varje enskild händelse. Om du inte har en särskild anledning att göra något annat, vanligtvis vill du aktivera Batchbearbetning.

## <a name="next-steps"></a>Nästa steg
Den här artikeln beskrivs i detalj hur den **IoTHubClient** biblioteket hittades i den **Azure IoT-enhet SDK för C**. Med den här informationen bör du ha en god förståelse av funktionerna i den **IoTHubClient** bibliotek. Den [nästa artikel](iot-hub-device-sdk-c-serializer.md) innehåller liknande information om den **serialiseraren** bibliotek.

Mer information om hur du utvecklar för IoT-hubb finns i [Azure IoT SDK][lnk-sdks].

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iotedge]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
