---
title: "Azure IoT-enhet SDK för C - serialiseraren | Microsoft Docs"
description: "Hur du använder serialiseraren biblioteket i Azure IoT-enhet SDK för C för att skapa appar för enheter som kommunicerar med en IoT-hubb."
services: iot-hub
documentationcenter: 
author: olivierbloch
manager: timlt
editor: 
ms.assetid: defbed34-de73-429c-8592-cd863a38e4dd
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2016
ms.author: obloch
ms.openlocfilehash: d8b9e147b68d16c6c166e92cbabf5b5b63e23e8d
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Azure IoT-enhet SDK för C – mer information om serialiseraren
Den [först artikel](iot-hub-device-sdk-c-intro.md) i den här serien introduceras de **Azure IoT-enhet SDK för C**. I nästa artikel tillhandahålls en mer detaljerad beskrivning av den [ **IoTHubClient**](iot-hub-device-sdk-c-iothubclient.md). Den här artikeln är klar täckning av SDK genom att tillhandahålla en mer detaljerad beskrivning av återstående komponenten: den **serialiseraren** bibliotek.

Inledande artikeln beskrivs hur du använder den **serialiseraren** biblioteket för att skicka händelser till och ta emot meddelanden från IoT-hubb. I den här artikeln vi utöka denna diskussion genom att tillhandahålla en fullständig förklaring av hur du modellera dina data med den **serialiseraren** makrospråk. Artikeln också innehåller mer information om hur biblioteket Serialiserar meddelanden (och i vissa fall hur du kan styra beteendet serialisering). Vi kommer också att beskriva vissa parametrar som du kan ändra som bestämmer storleken på modeller som du skapar.

Slutligen revisits artikeln vissa avsnitt som beskrivs i föregående artiklar, till exempel meddelande och egenskapen hantering. Som vi ska ta reda dessa funktioner fungerar på samma sätt med hjälp av den **serialiseraren** bibliotek som med den **IoTHubClient** bibliotek.

Allt i den här artikeln är baserad på den **serialiseraren** SDK-exempel. Om du vill följa med finns i **simplesample\_amqp** och **simplesample\_http** program som ingår i Azure IoT-enhet SDK för C.

Du hittar den [ **Azure IoT-enhet SDK för C** ](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen och visa information om API i den [C API-referens för](https://azure.github.io/azure-iot-sdk-c/index.html).

## <a name="the-modeling-language"></a>Modelleringsspråk
Den [inledande artikel](iot-hub-device-sdk-c-intro.md) i den här serien introduceras de **Azure IoT-enhet SDK för C** modeling language genom exempel finns i den **simplesample\_amqp** program:

```
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Som du kan se utifrån modelleringsspråk C makron. Du börjar din definition med alltid **BEGIN\_namnområde** och alltid sluta med **slutet\_namnområde**. Det är vanligt att namnge namnområdet för ditt företag eller, som i följande exempel projektet som du arbetar med.

Vad som ska ingå i namnområdet är definitioner för modellen. I det här fallet finns en modell för en anemometer. Återigen modellen kan vara namn som helst, men detta är vanligtvis namnet för enheten eller typ av data som du vill utbyta med IoT-hubb.  

Innehåller en definition av händelserna som du kan telemetriingång till IoT-hubb (den *data*) samt de meddelanden som du kan få från IoT-hubb (den *åtgärder*). Som du ser i exemplet har händelser en typ och ett namn. åtgärder som har ett namn och valfria parametrar (var och en med en typ).

Vad visas inte i det här exemplet är ytterligare datatyper som stöds av SDK. Vi rapporterar nästa avsnitt.

> [!NOTE]
> IoT-hubb som refererar till de data som en enhet skickar till den som *händelser*, medan modelleringsspråk refererar till den som *data* (definieras med hjälp av **WITH_DATA**). På samma sätt IoT-hubb refererar till de data som du skickar till enheter som *meddelanden*, medan modelleringsspråk refererar till den som *åtgärder* (definieras med hjälp av **WITH_ACTION**). Tänk på att dessa villkor synonymt får användas i den här artikeln.
> 
> 

### <a name="supported-data-types"></a>Datatyper som stöds
Följande datatyper stöds i modeller som skapats med den **serialiseraren** bibliotek:

| Typ | Beskrivning |
| --- | --- |
| dubbla |dubbel precision flyttal |
| int |32-bitars heltal |
| flyttal |flyttal med enkel precision |
| lång |långt heltal |
| int8\_t |8-bitars heltal |
| Int16\_t |16-bitars heltal |
| Int32\_t |32-bitars heltal |
| Int64\_t |64-bitars heltal |
| bool |Booleskt värde |
| ASCII\_char\_ptr |ASCII-sträng |
| EDM\_DATUM\_TID\_FÖRSKJUTNING |tidsförskjutningen för datum |
| EDM\_GUID |GUID |
| EDM\_BINÄRA |Binär |
| DEKLARERA\_STRUKTUR |Komplex datatyp |

Vi börjar med den senaste datatypen. Den **DECLARE\_STRUCT** kan du definiera komplexa datatyper som är grupper med primitiva typer. Grupperingarna ger oss möjlighet att definiera en modell som ser ut så här:

```
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

Vår modell innehåller en enda data händelse av typen **TestType**. **TestType** är en komplex typ som innehåller flera medlemmar som sammantagna visar primitiva typer som stöds av den **serialiseraren** modeling language.

Med en modell som detta skriva vi kod för att skicka data till IoT-hubb som visas på följande sätt:

```
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

Vi i princip, tilldela ett värde för varje medlem i den **Test** struktur och sedan anropa **SendAsync** att skicka den **Test** data händelsen till molnet. **SendAsync** är en hjälpfunktion som skickar en enda data-händelse till IoT-hubb:

```
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

Den här funktionen Serialiserar angivna händelsen och skickar det till IoT-hubb med **IoTHubClient\_SendEventAsync**. Det här är samma kod som beskrivs i föregående artiklar (**SendAsync** kapslar in logiken i en lämplig funktion).

En andra hjälpfunktion som används i föregående kod är **GetDateTimeOffset**. Den här funktionen omvandlar angiven tid till ett värde av typen **EDM\_datum\_tid\_OFFSET**:

```
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

Om du kör den här koden skickas följande meddelande till IoT-hubb:

```
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Observera att serialisering är JSON som är det format som genereras av den **serialiseraren** bibliotek. Observera också att varje medlem i det serialiserade JSON-objektet matchar medlemmarna i den **TestType** som vi har definierat i vår modell. Värdena matchar också exakt de som används i koden. Observera dock att binära data är base64-kodad: ”AQID” är base64-kodning av {0x01, 0x02, 0x03}.

Det här exemplet visar fördelen med att använda den **serialiseraren** -biblioteket kan vi ska skicka JSON till molnet, utan att behöva hantera uttryckligen serialisering i vårt program. Alla vi behöver bekymra sig om inställningsvärden Datahändelser i vår modell och sedan anropa enkla API: er för att skicka händelser till molnet.

Med den här informationen kan vi definiera modeller som innehåller en uppsättning stöds datatyper, inklusive komplexa typer (vi kan även inkludera komplexa typer i andra komplexa typer). Dock han serialiseras JSON som genererats av exemplet ovan öppnar en viktig aspekt. *Hur* vi skickar data med den **serialiseraren** biblioteket anger exakt hur JSON format. Viss är vad vi rapporterar nästa.

## <a name="more-about-serialization"></a>Mer information om serialisering
I föregående avsnitt visar ett exempel på utdata som genererats av den **serialiseraren** bibliotek. I det här avsnittet förklarar vi hur biblioteket Serialiserar data och hur du kan styra beteendet med hjälp av serialisering API: er.

För att kunna gå vidare diskussion om serialisering arbetar vi med en ny modell utifrån en termostat. Först ska vi använda vissa bakgrund på scenariot vi försöker adress.

Vi vill utforma en termostat som mäter temperatur- och fuktighetskonsekvens. Varje typ av data ska skickas till IoT-hubb på olika sätt. Som standard termostat ingresses temperatur-händelse en gång var 2 minuter. en fuktighet händelse är ingressed var 15: e minut. När antingen händelsen ingressed måste den innehålla en tidsstämpel som visar tid att motsvarande temperatur eller fuktighet var mäts.

På det här scenariot ser två olika sätt att modellera data, och förklarar vi effekten att modellering har på serialiserade utdata.

### <a name="model-1"></a>Modell 1
Här är den första versionen av en modell som stöder det föregående scenariot:

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Observera att modellen innehåller två Datahändelser: **temperatur** och **fuktighet**. Till skillnad från föregående exempel är typ av varje händelse en struktur som definieras med hjälp av **DECLARE\_STRUCT**. **TemperatureEvent** innehåller ett mått för temperatur- och en tidsstämpel; **HumidityEvent** innehåller ett fuktighet mått och en tidsstämpel. Den här modellen ger oss en naturlig sätt beskriva data för det scenario som beskrivs ovan. När vi skickar en händelse till molnet, skickar vi antingen en temperatur/tidsstämpel eller ett par fuktighet/timestamp.

Vi kan skicka en händelse för temperatur på molnet med kod till exempel följande:

```
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Vi ska använda hårdkodade värden för temperatur- och fuktighetskonsekvens i exempelkoden men anta att vi faktiskt hämtar dessa värden genom att ta prov på termostat motsvarande sensorerna.

Koden ovan använder den **GetDateTimeOffset** helper som infördes tidigare. Skäl som ska bli Rensa senare skiljer den här koden uttryckligen uppgiften att serialisering och skicka händelsen. Föregående kod Serialiserar temperatur händelsen i en buffert. Sedan **sendMessage** är en hjälpfunktion (ingår i **simplesample\_amqp**) som skickar händelsen till IoT-hubb:

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Den här koden är en delmängd av den **SendAsync** helper som beskrivs i föregående avsnitt, så vi inte kommer gå över den igen.

När vi kör föregående kod för att skicka händelsen temperatur skickas den här serialiserade form av händelsen till IoT-hubb:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Vi skickar en temperatur som är av typen **TemperatureEvent** och att strukturen innehåller en **temperatur** och **tid** medlem. Detta återspeglas direkt i den serialiserade informationen.

På liknande sätt kan vi skicka en fuktighet händelse med den här koden:

```
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Det serialiserade formulär som skickas till IoT-hubb visas på följande sätt:

```
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Detta är igen som förväntat.

Med den här modellen kan du föreställa dig hur ytterligare händelser kan enkelt läggas till. Du kan definiera flera strukturer med **DECLARE\_STRUCT**, och inkludera motsvarande händelse i en modell med hjälp av **WITH\_DATA**.

Nu ska vi ändra modellen så att den innehåller samma data, men med en annan struktur.

### <a name="model-2"></a>Modell 2
Överväg alternativa modellen till det ovan:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

I det här fallet har vi tagit bort den **DECLARE\_STRUCT** makron och bara definierar dataobjekt från vårt scenario med enkla typer från modelleringsspråk.

Bara för tillfället har vi ignorera den **tid** händelse. Här är koden till ingång med att reservera **temperatur**:

```
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Den här koden skickar följande serialiserade händelse till IoT-hubb:

```
{"Temperature":75}
```

Och koden för att skicka händelsen fuktighet visas på följande sätt:

```
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Den här koden skickar detta till IoT-hubb:

```
{"Humidity":45}
```

Så länge finns det inga överraskningar. Nu ska vi ändra hur vi kan använda SERIALISERA makro.

Den **SERIALISERA** makrot kan ta flera Datahändelser som argument. Detta gör det möjligt för oss att serialisera den **temperatur** och **fuktighet** händelse tillsammans och skicka dem till IoT-hubb i ett anrop:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Du kanske tror att resultatet av den här koden är att två händelser skickas till IoT-hubb:

[

{”Temperatur”: 75},

{”Fuktighet”: 45}

]

Med andra ord du tror att den här koden är detsamma som att skicka **temperatur** och **fuktighet** separat. Det är bara i syfte att underlätta vid båda händelser till **SERIALISERA** i samma anropet. Men är som inte fallet. I stället skickar koden ovan enda data händelsen till IoT-hubb:

{”Temperatur”: 75, ”fuktighet”: 45}

Det kan verka underligt eftersom vår modell definierar **temperatur** och **fuktighet** som två *separat* händelser:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Punkt, inte har vi mer modellen dessa händelser där **temperatur** och **fuktighet** finns i samma struktur:

```
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Om vi använder den här modellen, skulle det vara lättare att förstå hur **temperatur** och **fuktighet** skulle skickas i samma serialiserade meddelandet. Men det kanske inte rensa anledningen till att den fungerar på så sätt när du skickar både Datahändelser till **SERIALISERA** med hjälp av modellen 2.

Det här beteendet är lättare att förstå om du känner till antaganden som den **serialiseraren** biblioteket gör. Om du vill vara meningsfullt på detta går vi tillbaka till vår modell:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Tänk på den här modellen i objektorienterad villkor. I det här fallet vi modellering en fysisk enhet (en termostat) och den enheten innehåller attribut som **temperatur** och **fuktighet**.

Vi kan skicka hela tillståndet för vår modell med kod till exempel följande:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Om du värdena för temperatur, fuktighet och tid är inställda, ser vi en händelse inträffar skickas till IoT-hubb:

```
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Ibland kan du bara vill skicka *vissa* egenskaper för modellen till molnet (detta är särskilt viktigt om modellen innehåller ett stort antal Datahändelser). Det är användbart att skicka en delmängd data händelser, som i det tidigare exemplet:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Detta genererar exakt samma serialiserade händelse som om vi har definierat en **TemperatureEvent** med en **temperatur** och **tid** medlem, precis som vi med modellen är 1. I det här fallet kunde vi generera exakt samma serialiserade händelse med hjälp av en annan modell (modell 2) eftersom vi har ringt **SERIALISERA** på olika sätt.

Det viktiga är som om du skickar flera Datahändelser som ska **SERIALISERA,** sedan förutsätts varje händelse är en egenskap i ett enda JSON-objekt.

Det bästa sättet är beroende av du och hur du tycker om din modell. Om du skickar ”händelser” till molnet och varje händelse innehåller en definierad uppsättning egenskaper, är det första tillvägagångssättet mycket bra. I så fall kan du använda **DECLARE\_STRUCT** att definiera strukturen för varje händelse och inkludera dem i din modell med den **WITH\_DATA** makro. Du skickar sedan varje händelse som vi gjorde i det första exemplet ovan. I den här metoden kan du bara skicka en enskild händelse **SERIALISERAREN**.

Om du tycker om din modell objektorienterad sätt kanske sedan den andra metoden passar dig. I det här fallet element definieras med hjälp av **WITH\_DATA** ”egenskaper” för objektet. Du skickar oavsett delmängd av händelser till **SERIALISERA** som du vill, beroende på hur mycket av ditt ”objektets” tillstånd som du vill skicka till molnet.

Nether metod är rätt eller fel. Bara vara medveten om hur **serialiseraren** biblioteket fungerar och välj modellering-metod som bäst passar dina behov.

## <a name="message-handling"></a>Meddelandehantering
Så länge den här artikeln har endast beskrivs skicka händelser till IoT-hubb som åtgärdas inte ta emot meddelanden. Orsaken till detta är som vi behöver känna till att ta emot meddelanden har i stort sett beskrivits i en [tidigare artikel](iot-hub-device-sdk-c-intro.md). Återkalla från artikeln du bearbetar meddelanden genom att registrera en Återanropsfunktionen meddelande:

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Du kan sedan skriva den återanropsfunktion som anropas när ett meddelande tas emot:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
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

Den här implementeringen av **IoTHubMessage** anropar funktionen specifika för varje åtgärd i modellen. Om till exempel din modell definierar den här åtgärden:

```
WITH_ACTION(SetAirResistance, int, Position)
```

Du måste definiera en funktion med den här signaturen:

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** sedan anropas när meddelandet skickas till din enhet.

Vi har inte förklaras ännu är hur den serialiserade versionen av meddelande som ser ut. Med andra ord, om du vill skicka en **SetAirResistance** meddelande till din enhet, vad att se ut?

Om du skickar ett meddelande till en enhet kan göra du det via tjänsten Azure IoT SDK. Du måste veta vilka sträng för att skicka att anropa en viss åtgärd. Allmänna format för att skicka ett meddelande visas på följande sätt:

```
{"Name" : "", "Parameters" : "" }
```

Du skickar ett serialiserat JSON-objekt med två egenskaper: **namn** är namnet på åtgärden (meddelande) och **parametrar** innehåller parametrar för åtgärden.

Till exempel för att anropa **SetAirResistance** du kan skicka det här meddelandet till en enhet:

```
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

Åtgärdsnamn måste exakt matcha en åtgärd som definierats i din modell. Parameternamnet måste matcha samt. Tänk också på skiftlägeskänslighet. **Namnet** och **parametrar** är alltid versaler. Se till att matcha skiftläget för ditt namn och parametrar i din modell. I det här exemplet är namnet på åtgärden ”SetAirResistance” och inte ”setairresistance”.

De två andra åtgärderna **TurnFanOn** och **TurnFanOff** kan anropas genom att skicka meddelanden till en enhet:

```
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

Det här avsnittet beskrivs allt du behöver veta när skicka händelser och ta emot meddelanden med den **serialiseraren** bibliotek. Innan du går vidare vi beskriver vissa parametrar som du kan konfigurera som styr hur stor din modell är.

## <a name="macro-configuration"></a>Makrot-konfiguration
Om du använder den **serialiseraren** biblioteket som en viktig del av att vara medveten om SDK finns i biblioteket för azure-c-delad-verktyget.
Om du har klonas Azure-iot-sdk-c-databasen från GitHub med alternativet – rekursiv, hittar du här delade verktygsbiblioteket:

```
.\\c-utility
```

Om du inte har klona biblioteket, hittar du den [här](https://github.com/Azure/azure-c-shared-utility).

I biblioteket delade verktyg hittar du följande mapp:

```
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Den här mappen innehåller ett Visual Studio-lösning som kallas **makrot\_verktyg för webbplatsuppgradering\_h\_generator.sln**:

  ![](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.PNG)

I den här lösningen skapas den **makrot\_utils.h** fil. Det finns en standardmakrot\_utils.h-fil som medföljer SDK. Den här lösningen kan du ändra vissa parametrar och sedan återskapa huvudfilen baserat på dessa parametrar.

Två viktiga parametrar är berörda med är **nArithmetic** och **nMacroParameters** som definierats i dessa två rader som hittades i makro\_utils.tt:

```
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>

```

Dessa värden är standardparametrar som medföljer SDK. Varje parameter har enligt följande:

* nMacroParameters – styr hur många parametrar som du kan ha i en DECLARE\_makrodefinition i MODELLEN.
* nArithmetic – styr det totala antalet medlemmar som tillåts i en modell.

Dessa parametrar är viktiga orsaken är eftersom de styr hur stor din modell kan vara. Anta till exempel att den här modelldefinitionen:

```
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Som nämnts tidigare **DECLARE\_MODELLEN** är bara ett C makro. Namnen på modellen och **WITH\_DATA** instruktionen (har ett annat makro) har parametrar av **DECLARE\_MODELLEN**. **nMacroParameters** definierar hur många parametrar kan ingå i **DECLARE\_MODELLEN**. Detta definierar effektivt, hur många data händelse och åtgärd deklarationer att du kan ha. Därför med Standardgränsen för 124 innebär detta att du kan definiera en modell med en kombination av om 60 åtgärder och Datahändelser. Om du överskrider den här gränsen, får du Kompilatorfel som ser ut ungefär så här:

  ![](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.PNG)

Den **nArithmetic** parametern är mer om den interna bearbetningen i makrospråk än ditt program.  Den styr det totala antalet medlemmar i din modell, inklusive **DECLARE_STRUCT** makron. Om du börjar Se Kompilatorfel sådana här kommer bör du öka **nArithmetic**:

   ![](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.PNG)

Om du vill ändra dessa parametrar kan du ändra värdena för makrot\_utils.tt fil, kompilera om makrot\_verktyg för webbplatsuppgradering\_h\_generator.sln lösningen och kör kompilerade programmet. När du gör ett nytt makro\_utils.h fil skapas och placeras i det.\\ vanliga\\inc directory.

För att kunna använda den nya versionen av makrot\_utils.h, ta bort den **serialiseraren** NuGet-paketet från din lösning och i dess ställe inkluderar den **serialiseraren** Visual Studio-projekt. Detta gör att din kod ska kompileras mot källkoden för serialiserare-biblioteket. Detta inkluderar uppdaterade makrot\_utils.h. Om du vill göra detta för **simplesample\_amqp**, starta genom att ta bort NuGet-paket för serialisering biblioteket från lösningen:

   ![](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.PNG)

Lägg sedan till det här projektet i Visual Studio-lösningen:

> . \\c\\serialiseraren\\skapa\\windows\\serializer.vcxproj
> 
> 

När du är klar bör din lösning se ut så här:

   ![](media/iot-hub-device-sdk-c-serializer/05-serializer-project.PNG)

Nu när du kompilerar din lösning uppdaterade makrot\_utils.h ingår i din binary.

Observera att öka dessa värden som är tillräckligt högt kan överstiga kompileraren gränser. Så här långt den **nMacroParameters** är den viktigaste parameter som berördes. C99-specifikationen anger att minst 127 parametrar tillåts i en makrodefinition. Microsoft-kompilatorn följer specifikationen exakt (och är begränsad till 127), så du kan inte öka **nMacroParameters** bortom standardvärdet. Andra kompilerare kan tillåta dig att göra det (till exempel GNU kompilatorn stöder en högre gräns).

Hittills har vi omfattas nästan allt du behöver veta om hur du skriva kod med den **serialiseraren** bibliotek. Innan du att vi Ångra vissa avsnitt från föregående artiklar som du kanske undrar om.

## <a name="the-lower-level-apis"></a>Lågnivå-API: er
Exempelprogrammet som den här artikeln fokuserar är **simplesample\_amqp**. Det här exemplet använder den högre nivån (den icke-”lla”) API: er för att skicka händelser och ta emot meddelanden. Om du använder dessa API: er körs en bakgrundstråd som tar hand om både skicka händelser och ta emot meddelanden. Du kan dock använda API: er för lägre (alla) för att ta bort den här bakgrundstråd och dra explicit kontroll över när du skickar händelser eller ta emot meddelanden från molnet.

Enligt beskrivningen i en [föregående artikel](iot-hub-device-sdk-c-iothubclient.md), det finns en uppsättning funktioner som består av att API: er:

* IoTHubClient\_CreateFromConnectionString
* IoTHubClient\_SendEventAsync
* IoTHubClient\_SetMessageCallback
* IoTHubClient\_förstör

Dessa API: er visas i **simplesample\_amqp**.

Det finns också en liknande uppsättning API: er på lägre nivå.

* IoTHubClient\_lla\_CreateFromConnectionString
* IoTHubClient\_lla\_SendEventAsync
* IoTHubClient\_lla\_SetMessageCallback
* IoTHubClient\_lla\_förstör

Observera att på lägre nivå-API: er fungerar på samma sätt som beskrivs i föregående artiklar. Du kan använda den första uppsättningen av API: er om du vill att en bakgrundstråd som hanterar skicka händelser och ta emot meddelanden. Du använder den andra uppsättningen av API: er om du vill ha explicit kontroll över när du skickar och tar emot data från IoT-hubb. Antingen uppsättning API: er fungerar lika bra med den **serialiseraren** bibliotek.

Ett exempel på hur du använder API: er för lägre nivå med den **serialiseraren** biblioteket finns det **simplesample\_http** program.

## <a name="additional-topics"></a>Ytterligare information
Några andra avsnitt värt att nämna igen är egenskapen hantering, med autentiseringsuppgifter för en annan enhet och konfigurationsalternativ. Dessa är alla avsnitt som beskrivs i en [föregående artikel](iot-hub-device-sdk-c-iothubclient.md). Utgångspunkten är att alla dessa funktioner fungerar på samma sätt med den **serialiseraren** bibliotek som med den **IoTHubClient** bibliotek. Till exempel om du vill bifoga egenskaper till en händelse från din modell kan du använda **IoTHubMessage\_egenskaper** och **kartan**\_**AddorUpdate**, på samma sätt som beskrivits tidigare:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Om händelsen skapades från den **serialiseraren** bibliotek eller skapas manuellt med hjälp av den **IoTHubClient** biblioteket spelar ingen roll.

För annan enhet autentiseringsuppgifterna, med **IoTHubClient\_lla\_skapa** fungerar lika bra som **IoTHubClient\_CreateFromConnectionString** för allokera en **IOTHUB\_klienten\_hantera**.

Slutligen, om du använder den **serialiseraren** bibliotek, kan du ange konfigurationsalternativ med **IoTHubClient\_lla\_SetOption** precis som du gjorde när du använder **IoTHubClient** bibliotek.

En funktion som är unik för den **serialiseraren** biblioteket är initieringen API: er. Innan du kan börja arbeta med biblioteket, måste du anropa **serialiseraren\_init**:

```
serializer_init(NULL);
```

Detta görs precis innan du anropar **IoTHubClient\_CreateFromConnectionString**.

På liknande sätt, när du är klar arbetar med biblioteket, det senaste anropet ska du göra är att **serialiseraren\_deinit**:

```
serializer_deinit();
```

Annars alla andra funktioner som anges ovan fungerar på samma sätt den **serialiseraren** biblioteket som i den **IoTHubClient** bibliotek. Mer information om något av de här ämnena finns i [föregående artikel](iot-hub-device-sdk-c-iothubclient.md) i den här serien.

## <a name="next-steps"></a>Nästa steg
Den här artikeln beskrivs i detalj unika aspekter av den **serialiseraren** biblioteket finns i den **Azure IoT-enhet SDK för C**. Med informationen som du bör ha en god förståelse av hur du använder modeller att skicka händelser och ta emot meddelanden från IoT-hubb.

Detta avslutar också på hur du utvecklar program med tre delar serien i **Azure IoT-enhet SDK för C**. Detta bör vara tillräckligt med information för att inte bara komma igång utan att ge goda kunskaper om hur de API: er fungerar. Mer information finns några exempel i SDK beskrivs inte här. I annat fall den [SDK-dokumentationen](https://github.com/Azure/azure-iot-sdk-c) är en bra resurs för ytterligare information.

Mer information om hur du utvecklar för IoT-hubb finns i [Azure IoT SDK][lnk-sdks].

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Distribuera AI till enheter med Azure IoT kant][lnk-iotedge]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
