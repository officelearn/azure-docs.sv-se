---
title: Azure IoT-enhet SDK för C - Serializer | Microsoft-dokument
description: Så här använder du Serializer-biblioteket i Azure IoT-enheten SDK för C för att skapa enhetsappar som kommunicerar med en IoT-hubb.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: d4916d651638f0d1dbb4f10e0e0732f5c330d300
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767024"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Azure IoT-enhet SDK för C – mer om serialiserare

Den första artikeln i den här serien introducerade [Introduktion till Azure IoT-enhet SDK för C](iot-hub-device-sdk-c-intro.md). Nästa artikel gav en mer detaljerad beskrivning av [Azure IoT-enheten SDK för C -- IoTHubClient](iot-hub-device-sdk-c-iothubclient.md). Den här artikeln slutför täckningen av SDK genom att ge en mer detaljerad beskrivning av den återstående komponenten: **serializerbiblioteket.**

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

I den inledande artikeln beskrivs hur du använder **serialiserarbiblioteket** för att skicka händelser till och ta emot meddelanden från IoT Hub. I den här artikeln utökar vi den diskussionen genom att ge en mer fullständig förklaring av hur du modellerar dina data med **serialiserarens** makrospråk. Artikeln innehåller också mer information om hur biblioteket serialiserar meddelanden (och i vissa fall hur du kan styra serialiseringsbeteendet). Vi beskriver också några parametrar som du kan ändra som avgör storleken på de modeller du skapar.

Slutligen återbesöker artikeln några ämnen som behandlats i tidigare artiklar, till exempel meddelande- och egenskapshantering. Som vi får reda på, dessa funktioner fungerar på samma sätt med **serialiserare** biblioteket som de gör med **IoTHubClient** biblioteket.

Allt som beskrivs i den här artikeln är baserat på **serialiseraren** SDK-exempel. Om du vill följa med, se **simplesample\_amqp** och **simplesample\_http-program** som ingår i Azure IoT-enheten SDK för C.

Du hittar [**Azure IoT-enheten SDK för C**](https://github.com/Azure/azure-iot-sdk-c) GitHub-databasen och visa information om API i [C API-referensen](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-modeling-language"></a>Modelleringsspråket

[Azure IoT-enheten SDK för C-artikeln](iot-hub-device-sdk-c-intro.md) i den här serien introducerade Azure **IoT-enheten SDK för C-modellering** via exemplet i **simplesample\_amqp-programmet:**

```C
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

Som du kan se är modelleringsspråket baserat på C-makron. Du börjar alltid din definition med **BEGIN\_NAMESPACE** och slutar alltid med **\_END NAMESPACE**. Det är vanligt att namnge namnområdet för ditt företag eller, som i det här exemplet, det projekt som du arbetar med.

Det som finns i namnområdet är modelldefinitioner. I det här fallet finns det en enda modell för en anemometer. Återigen kan modellen namnges vad som helst, men vanligtvis är modellen uppkallad efter den enhet eller typ av data som du vill utbyta med IoT Hub.  

Modeller innehåller en definition av de händelser som du kan gå in på IoT Hub *(data)* samt de meddelanden du kan ta emot från IoT Hub *(åtgärderna*). Som du kan se i exemplet har händelser en typ och ett namn. åtgärder har ett namn och valfria parametrar (var och en med en typ).

Det som inte visas i det här exemplet är ytterligare datatyper som stöds av SDK. Vi tar det nästa gång.

> [!NOTE]
> IoT Hub refererar till de data som en enhet skickar till den som *händelser,* medan modelleringsspråket refererar till dem som *data* (definierat med **WITH_DATA**). På samma sätt refererar IoT Hub till de data du skickar till enheter som *meddelanden*, medan modelleringsspråket refererar till dem som *åtgärder* (definieras med **WITH_ACTION**). Tänk på att dessa termer kan användas omväxlande i den här artikeln.
> 
> 

## <a name="supported-data-types"></a>Datatyper som stöds

Följande datatyper stöds i modeller som skapats med **serialiseringsbiblioteket:**

| Typ | Beskrivning |
| --- | --- |
| double |flyttalsnummer med dubbel precision |
| int |32-bitars heltal |
| float |flyttalsnummer med en precision |
| long |långt heltal |
| int8\_t |8-bitars heltal |
| int16\_t |16-bitars heltal |
| int32\_t |32-bitars heltal |
| int64\_t |64-bitars heltal |
| bool |boolean |
| ascii\_\_röding ptr |ASCII-sträng |
| EDM-DATUM\_\_TID\_FÖRSKJUTNING |datum tidsförskjutning |
| EDM\_GUID |GUID |
| EDM\_BINÄR |binary |
| DEKLARERA\_STRUKTUR |komplex datatyp |

Låt oss börja med den sista datatypen. **Med\_DECLARE STRUCT** kan du definiera komplexa datatyper som grupperar av andra primitiva typer. Dessa grupperingar tillåter oss att definiera en modell som ser ut så här:

```C
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

Vår modell innehåller en enda datahändelse av typen **TestType**. **TestType** är en komplex typ som innehåller flera medlemmar, som tillsammans visar de primitiva typer som stöds av **serialiserarens** modelleringsspråk.

Med en modell som denna kan vi skriva kod för att skicka data till IoT Hub som visas på följande sätt:

```C
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

I grund och botten tilldelar vi ett värde till varje medlem i **teststrukturen** och anropar sedan **SendAsync** för att skicka **testdatahändelsen** till molnet. **SendAsync** är en hjälpfunktion som skickar en enda datahändelse till IoT Hub:

```C
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

Den här funktionen serialiserar den angivna datahändelsen och skickar den till IoT Hub med **IoTHubClient\_SendEventAsync**. Detta är samma kod som diskuterats i tidigare artiklar (**SendAsync** kapslar in logiken i en bekväm funktion).

En annan hjälpfunktion som används i den tidigare koden är **GetDateTimeOffset**. Den här funktionen omvandlar den angivna tiden till ett värde av typen **EDM\_DATE\_TIME\_OFFSET:**

```C
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

Om du kör den här koden skickas följande meddelande till IoT Hub:

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Observera att serialiseringen finns i JSON, vilket är det format som genereras av **serialiserarbiblioteket.** Observera också att varje medlem i det serialiserade JSON-objektet matchar medlemmarna i **TestType** som vi definierade i vår modell. Värdena matchar också exakt de som används i koden. Observera dock att binära data är base64-kodade: "AQID" är base64-kodningen av {0x01, 0x02, 0x03}.

Det här exemplet visar fördelen med att använda **serialiserarbiblioteket** - det gör det möjligt för oss att skicka JSON till molnet, utan att uttryckligen behöva hantera serialisering i vårt program. Allt vi behöver oroa oss för är att ange värden för datahändelser i vår modell och sedan ringa enkla API:er för att skicka dessa händelser till molnet.

Med den här informationen kan vi definiera modeller som innehåller olika datatyper som stöds, inklusive komplexa typer (vi kan även inkludera komplexa typer inom andra komplexa typer). Den serialiserade JSON som genereras av exemplet ovan tar dock upp en viktig punkt. *Hur* vi skickar data med **serialiserarbiblioteket** avgör exakt hur JSON bildas. Just den punkten är vad vi ska täcka härnäst.

## <a name="more-about-serialization"></a>Mer om serialisering

I föregående avsnitt beskrivs ett exempel på utdata som genereras av **serialiserarbiblioteket.** I det här avsnittet förklarar vi hur biblioteket serialiserar data och hur du kan styra det beteendet med hjälp av serialiserings-API:erna.

För att föra diskussionen om serialisering, kommer vi att arbeta med en ny modell baserad på en termostat. Låt oss först ge lite bakgrund om det scenario vi försöker ta itu med.

Vi vill modellera en termostat som mäter temperatur och luftfuktighet. Varje del av data kommer att skickas till IoT Hub på olika sätt. Som standard inträder termostaten en temperaturhändelse en gång varannan minut. en fuktighetshändelse inträder en gång var 15:e minut. När någon av händelser är ingående måste den innehålla en tidsstämpel som visar den tid då motsvarande temperatur eller luftfuktighet mättes.

Med tanke på det här scenariot visar vi två olika sätt att modellera data, och vi kommer att förklara vilken effekt modelleringen har på den serialiserade utdata.

### <a name="model-1"></a>Modell 1

Här är den första versionen av en modell som stöder föregående scenario:

```C
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

Observera att modellen innehåller två datahändelser: **Temperatur** och **luftfuktighet**. Till skillnad från tidigare exempel är typen av varje händelse en struktur som definieras med **DECLARE\_STRUCT**. **TemperatureEvent** innehåller en temperaturmätning och en tidsstämpel; **HumidityEvent** innehåller en fuktmätning och en tidsstämpel. Denna modell ger oss ett naturligt sätt att modellera data för det scenario som beskrivs ovan. När vi skickar en händelse till molnet skickar vi antingen en temperatur/tidsstämpel eller ett fuktighets-/tidsstämpelpar.

Vi kan skicka en temperaturhändelse till molnet med hjälp av kod som följande:

```C
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

Vi använder hårdkodade värden för temperatur och fuktighet i exempelkoden, men föreställ dig att vi faktiskt hämtar dessa värden genom att prova motsvarande sensorer på termostaten.

Koden ovan använder **GetDateTimeOffset-hjälpen** som introducerades tidigare. Av skäl som kommer att bli tydliga senare, separerar den här koden uttryckligen uppgiften att serialisera och skicka händelsen. Den tidigare koden serialiserar temperaturhändelsen till en buffert. Sedan är **sendMessage** en hjälpfunktion (ingår i **simplesample\_amqp)** som skickar händelsen till IoT Hub:

```C
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

Den här koden är en delmängd av **SendAsync-hjälpen** som beskrivs i föregående avsnitt, så vi går inte igenom den igen här.

När vi kör den tidigare koden för att skicka händelsen Temperatur skickas den här serialiserade formen av händelsen till IoT Hub:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Vi skickar en temperatur, som är av typen **TemperatureEvent**, och den konstruktionen innehåller en **temperatur-** och **tidsmedlem.** Detta återspeglas direkt i serialiserade data.

På samma sätt kan vi skicka en fuktighetshändelse med den här koden:

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Det serialiserade formulär som skickas till IoT Hub visas på följande sätt:

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Återigen, detta är som förväntat.

Med den här modellen kan du föreställa dig hur ytterligare händelser enkelt kan läggas till. Du definierar fler strukturer med **DEKLARERA\_STRUCT**och inkluderar motsvarande händelse i modellen med **MED DATA\_**.

Nu ska vi ändra modellen så att den innehåller samma data men med en annan struktur.

### <a name="model-2"></a>Modell 2

Tänk på denna alternativa modell till en ovan:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

I det här fallet har vi eliminerat **DECLARE\_STRUCT-makrona** och definierar helt enkelt dataobjekten från vårt scenario med enkla typer från modelleringsspråket.

Bara för tillfället, ignorera **Time** händelsen. Med det åt sidan, här är koden för att ingående **temperatur:**

```C
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

Den här koden skickar följande serialiserade händelse till IoT Hub:

```C
{"Temperature":75}
```

Och koden för att skicka händelsen Luftfuktighet visas på följande sätt:

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Den här koden skickar detta till IoT Hub:

```C
{"Humidity":45}
```

Än så länge finns det inga överraskningar. Nu ska vi ändra hur vi använder SERIALIZE makro.

**Serialize-makrot** kan ta flera datahändelser som argument. Detta gör det möjligt **Temperature** för oss att serialisera temperatur **och luftfuktighet** händelsen tillsammans och skicka dem till IoT Hub i ett samtal:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Du kan gissa att resultatet av den här koden är att två datahändelser skickas till IoT Hub:

[ {"Temperatur":75}, {"Luftfuktighet":45} ]

Med andra ord kan du förvänta dig att den här koden är densamma som att skicka **temperatur** **och luftfuktighet** separat. Det är bara en bekvämlighet att skicka båda händelserna till **SERIALIZE** i samma samtal. Men så är inte fallet. I stället skickar koden ovan den här enskilda datahändelsen till IoT Hub:

{"Temperatur":75, "Luftfuktighet":45}

Detta kan verka konstigt eftersom vår modell definierar **temperatur** och **luftfuktighet** som två *separata* händelser:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Mer till den punkt, vi har inte modellera dessa händelser där **temperatur** och **luftfuktighet** är i samma struktur:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Om vi använde den här modellen skulle det vara lättare att förstå hur **temperatur** och **luftfuktighet** skulle skickas i samma serialiserade meddelande. Men det kanske inte är klart varför det fungerar på det sättet när du skickar båda datahändelserna till **SERIALIZE** med hjälp av modell 2.

Detta är lättare att förstå om du vet de antaganden som **serialiserarbiblioteket** gör. För att förstå detta, låt oss gå tillbaka till vår modell:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Tänk på den här modellen i objektorienterade termer. I det här fallet modellerar vi en fysisk enhet (en termostat) och den enheten innehåller attribut som **temperatur** och **luftfuktighet**.

Vi kan skicka hela tillståndet i vår modell med kod som följande:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Om du antar att värdena för temperatur, luftfuktighet och tid är inställda, skulle vi se en händelse som denna skickas till IoT Hub:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Ibland kanske du bara vill skicka *vissa* egenskaper för modellen till molnet (detta gäller särskilt om din modell innehåller ett stort antal datahändelser). Det är användbart att bara skicka en delmängd av datahändelser, till exempel i vårt tidigare exempel:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Detta genererar exakt samma serialiserade händelse som om vi hade definierat en **TemperatureEvent** med en **Temperatur** och **Tid** medlem, precis som vi gjorde med modell 1. I det här fallet kunde vi generera exakt samma serialiserade händelse med hjälp av en annan modell (modell 2) eftersom vi kallade **SERIALIZE** på ett annat sätt.

Den viktiga punkten är att om du skickar flera datahändelser till **SERIALIZE,** antar den att varje händelse är en egenskap i ett enda JSON-objekt.

Det bästa tillvägagångssättet beror på dig och hur du tänker om din modell. Om du skickar "händelser" till molnet och varje händelse innehåller en definierad uppsättning egenskaper, gör den första metoden mycket mening. I så fall använder du **DECLARE\_STRUCT** för att definiera strukturen för varje händelse och sedan inkludera dem i din modell med makrot **\_MED DATA.** Sedan skickar du varje händelse som vi gjorde i det första exemplet ovan. I den här metoden skulle du bara skicka en enda datahändelse till **SERIALIZER**.

Om du tänker på din modell på ett objektorienterat sätt, då den andra metoden kan passa dig. I det här fallet är de element som definieras med **MED DATA\_** objektets "egenskaper". Du skickar den delmängd av händelser **som** du vill, beroende på hur mycket av ditt "objekts" tillstånd du vill skicka till molnet.

Nedre tillvägagångssätt är rätt eller fel. Tänk bara på hur **serialiserarbiblioteket** fungerar och välj den modelleringsmetod som bäst passar dina behov.

## <a name="message-handling"></a>Meddelandehantering

Hittills har den här artikeln bara diskuterat att skicka händelser till IoT Hub och har inte adresserat mottagningsmeddelanden. Anledningen till detta är att det vi behöver veta om att ta emot meddelanden till stor del har tagits upp i artikeln [Azure IoT-enhet SDK för C](iot-hub-device-sdk-c-intro.md). Återkalla från den artikeln att du bearbetar meddelanden genom att registrera en meddelandeåterringsfunktion:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Du skriver sedan den motringningsfunktion som anropas när ett meddelande tas emot:

```C
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

Den här implementeringen av **IoTHubMessage anropar** den specifika funktionen för varje åtgärd i din modell. Om modellen till exempel definierar den här åtgärden:

```C
WITH_ACTION(SetAirResistance, int, Position)
```

Du måste definiera en funktion med den här signaturen:

```C
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** anropas sedan när meddelandet skickas till enheten.

Vad vi inte har förklarat ännu är vad den serialiserade versionen av meddelandet ser ut. Med andra ord, om du vill skicka ett **SetAirResistance-meddelande** till din enhet, hur ser det ut?

Om du skickar ett meddelande till en enhet gör du det via Azure IoT-tjänsten SDK. Du behöver fortfarande veta vilken sträng du ska skicka för att anropa en viss åtgärd. Det allmänna formatet för att skicka ett meddelande visas på följande sätt:

```C
{"Name" : "", "Parameters" : "" }
```

Du skickar ett serialiserat JSON-objekt med två egenskaper: **Namn** är namnet på åtgärden (meddelandet) och **Parametrar** innehåller parametrarna för den åtgärden.

Om du till exempel vill anropa **SetAirResistance** kan du skicka det här meddelandet till en enhet:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

Åtgärdsnamnet måste exakt matcha en åtgärd som definierats i modellen. Parameternamnen måste också matchas. Notera även skiftlägeskänslighet. **Namn** och **parametrar** är alltid versaler. Se till att matcha fallet med ditt åtgärdsnamn och parametrar i din modell. I det här exemplet är åtgärdsnamnet "SetAirResistance" och inte "setairresistance".

De två andra åtgärderna **TurnFanOn** och **TurnFanOff** kan anropas genom att skicka dessa meddelanden till en enhet:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

I det här avsnittet beskrivs allt du behöver veta när du skickar händelser och tar emot meddelanden med **serialiserarbiblioteket.** Innan vi går vidare ska vi täcka några parametrar som du kan konfigurera som styr hur stor din modell är.

## <a name="macro-configuration"></a>Makrokonfiguration

Om du använder **Serializer-biblioteket** finns en viktig del av SDK för att vara medveten om i azure-c-shared-utility-biblioteket.

Om du har klonat Azure-iot-sdk-c-databasen från GitHub och utfärdat `git submodule update --init` kommandot hittar du det här delade verktygsbiblioteket här:

```C
.\\c-utility
```

Om du inte har klonat biblioteket hittar du det [här.](https://github.com/Azure/azure-c-shared-utility)

I det delade verktygsbiblioteket hittar du följande mapp:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Den här mappen innehåller en Visual Studio-lösning som kallas **macro\_utils\_h\_generator.sln**:

  ![Skärmbild av Visual Studio-lösningen maco_utils_h_generator](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

Programmet i den här lösningen genererar **filen macro\_utils.h.** Det finns en\_standardfil i makroutils.h som ingår i SDK. Med den här lösningen kan du ändra vissa parametrar och sedan återskapa huvudfilen baserat på dessa parametrar.

De två nyckelparametrar som ska behandlas är **nAritmetiska** och **nMacroParametrar**\_, som definieras i dessa två rader som finns i makro utils.tt:

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Dessa värden är standardparametrarna som ingår i SDK. Varje parameter har följande betydelse:

* nMacroParameters – Styr hur många parametrar\_du kan ha i en DECLARE MODELL makrodefinition.
* nAritmetisk – Styr det totala antalet medlemmar som tillåts i en modell.

Anledningen till att dessa parametrar är viktiga är att de styr hur stor din modell kan vara. Tänk dig till exempel den här modelldefinitionen:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Som tidigare **nämnts,\_DECLARE MODELL** är bara ett C-makro. Namnen på modellen och **\_WITH DATA-satsen** (ännu ett makro) är parametrar **för DECLARE\_MODEL**. **nMacroParameters** definierar hur många parametrar som kan inkluderas i **\_DECLARE MODEL**. Effektivt definierar detta hur många datahändelse- och åtgärdsdeklarationer du kan ha. Med standardgränsen 124 innebär det därför att du kan definiera en modell med en kombination av cirka 60 åtgärder och datahändelser. Om du försöker överskrida den här gränsen visas kompilatorfel som liknar följande:

  ![Skärmbild av kompilatorfel för makroparametrar](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

Den **nAritmetiska** parametern handlar mer om makrospråkets interna funktion än ditt program.  Den styr det totala antalet medlemmar du kan ha i din modell, inklusive **DECLARE_STRUCT** makron. Om du börjar se kompilatorfel som detta, bör du försöka öka **nAritmetiska:**

   ![Skärmbild av aritmetiska kompilatorfel](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Om du vill ändra dessa parametrar ändrar\_du värdena i makrot\_utils.tt\_filen, kompilerar om makroutils h\_generator.sln-lösningen och kör det kompilerade programmet. När du gör det\_genereras en ny makroutils.h-fil och placeras i . \\gemensam\\inkl-katalog.

För att kunna använda den\_nya versionen av macro utils.h tar du bort **serialiseraren** NuGet-paketet från din lösning och i dess ställe inkluderar **serialiseraren** Visual Studio-projektet. Detta gör att koden kan kompileras mot källkoden för serialiserarbiblioteket. Detta inkluderar det\_uppdaterade makroutils.h. Om du vill göra detta för **simplesample\_amqp**, börja med att ta bort NuGet paketet för serialiserare biblioteket från lösningen:

   ![Skärmbild av Ta bort NuGet-paketet för serialiserarbiblioteket](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Lägg sedan till det här projektet i Visual Studio-lösningen:

> . \\c\\\\serializer\\\\bygga windows serializer.vcxproj
> 
> 

När du är klar bör din lösning se ut så här:

   ![Skärmbild av simplesample_amqp Visual Studio-lösningen](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Nu när du kompilerar\_din lösning, den uppdaterade makro utils.h ingår i din binära.

Observera att en ökning av dessa värden tillräckligt högt kan överskrida kompilatorgränserna. Till denna punkt är **nMacroParameters** den viktigaste parametern som ska berörs. C99-specifikationen anger att minst 127 parametrar tillåts i en makrodefinition. Microsoft-kompilatorn följer spec exakt (och har en gräns på 127), så att du inte kommer att kunna öka **nMacroParameters** utöver standard. Andra kompilatorer kan tillåta dig att göra det (till exempel stöder GNU-kompilatorn en högre gräns).

Hittills har vi täckt nästan allt du behöver veta om hur man skriver kod med **serialiserare** biblioteket. Innan du avslutar, låt oss återkomma till några ämnen från tidigare artiklar som du kanske undrar över.

## <a name="the-lower-level-apis"></a>Api:erna på lägre nivå
Exempelprogrammet som den här artikeln fokuserade på är **simplesample\_amqp**. Det här exemplet använder API:er på högre nivå **(icke-LL)** för att skicka händelser och ta emot meddelanden. Om du använder dessa API:er körs en bakgrundstråd som tar hand om både skicka händelser och ta emot meddelanden. Du kan dock använda API:er på lägre nivå (LL) för att eliminera den här bakgrundstråden och ta explicit kontroll över när du skickar händelser eller tar emot meddelanden från molnet.

Som beskrivs i en [tidigare artikel](iot-hub-device-sdk-c-iothubclient.md)finns det en uppsättning funktioner som består av API:er på högre nivå:

* IoTHubClient\_skapafrånConnectionString
* IoTHubClient\_SendEventAsync
* IoTHubClient\_SetMessageCallback
* IoTHubClient\_förstör

Dessa API:er visas i **simplesample\_amqp**.

Det finns också en liknande uppsättning api:er på lägre nivå.

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_\_LL Förstör

Observera att api:erna på lägre nivå fungerar på exakt samma sätt som beskrivs i föregående artiklar. Du kan använda den första uppsättningen API:er om du vill ha en bakgrundstråd för att hantera sändningshändelser och ta emot meddelanden. Du använder den andra uppsättningen API:er om du vill ha explicit kontroll över när du skickar och ta emot data från IoT Hub. Båda uppsättning API:er fungerar lika bra med **serialiseringsbiblioteket.**

Ett exempel på hur api:er på lägre nivå används med **serialiserarbiblioteket** finns i programmet **simplesample\_http.**

## <a name="additional-topics"></a>Ytterligare information
Några andra ämnen som är värda att nämna igen är egenskapshantering, med alternativa enhetsautentiseringsuppgifter och konfigurationsalternativ. Dessa är alla ämnen som behandlas i en [tidigare artikel](iot-hub-device-sdk-c-iothubclient.md). Den viktigaste punkten är att alla dessa funktioner fungerar på samma sätt med **serialiserarbiblioteket** som de gör med **IoTHubClient-biblioteket.** Om du till exempel vill koppla egenskaper till en händelse från din modell använder du **egenskaper för\_IoTHubMessage** och **Map**\_**AddorUpdate**, på samma sätt som tidigare beskrivits:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Om händelsen har genererats från **serialiserarbiblioteket** eller skapades manuellt med **IoTHubClient-biblioteket** spelar ingen roll.

För alternativa enhetsautentiseringsuppgifter fungerar **ioTHubClient\_LL\_Create** lika bra som **IoTHubClient\_CreateFromConnectionString** för allokering av en **IOTHUB-klientreferens\_\_**.

Slutligen, om du använder **serializer-biblioteket,** kan du ange konfigurationsalternativ med **\_IoTHubClient LL\_SetOption** precis som du gjorde när du använder **IoTHubClient-biblioteket.**

En funktion som är unik för **serializerbiblioteket** är initierings-API:erna. Innan du kan börja arbeta med biblioteket måste du anropa **serialiseraren\_init:**

```C
serializer_init(NULL);
```

Detta görs precis innan du anropar **IoTHubClient\_CreateFromConnectionString**.

På samma sätt, när du är klar med att arbeta med biblioteket, det sista samtalet du gör är att **serialiserare\_deinit:**

```C
serializer_deinit();
```

Annars fungerar alla andra funktioner som anges ovan på samma sätt i **serialiserarbiblioteket** som de gör i **IoTHubClient-biblioteket.** Mer information om något av dessa avsnitt finns i [föregående artikel](iot-hub-device-sdk-c-iothubclient.md) i den här serien.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs i detalj de unika aspekterna av **serialiserarbiblioteket** i **Azure IoT-enheten SDK för C**. Med den information som ges bör du ha en god förståelse för hur man använder modeller för att skicka händelser och ta emot meddelanden från IoT Hub.

Detta avslutar också serien i tre delar om hur du utvecklar program med **Azure IoT-enheten SDK för C**. Detta bör vara tillräckligt med information för att inte bara komma igång utan ger dig en grundlig förståelse för hur API: er fungerar. För ytterligare information finns det några exempel i SDK som inte omfattas här. Annars är [Azure IoT SDK-dokumentationen](https://github.com/Azure/azure-iot-sdk-c) en bra resurs för ytterligare information.

Mer information om hur du utvecklar för IoT Hub finns i [Azure IoT SDK:er](iot-hub-devguide-sdks.md).

Mer information om hur du utforskar funktionerna i IoT Hub finns i [Distribuera AI till kantenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).