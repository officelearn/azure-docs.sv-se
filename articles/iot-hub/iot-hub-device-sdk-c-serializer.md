---
title: Azure IoT-enhetens SDK för C - serialiseraren | Microsoft Docs
description: Hur du använder serialiserare-biblioteket i Azure IoT-enhetens SDK för C för att skapa appar för enheter som kommunicerar med en IoT-hubb.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: yizhon
ms.openlocfilehash: 0a7e30be374ae5095e206ce0e519e51bb58f1f00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60399271"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Azure IoT-enhetens SDK för C – mer om serialiserare

Den första artikeln i den här serien som introducerades i [introduktion till Azure IoT-enhetens SDK för C](iot-hub-device-sdk-c-intro.md). Nästa artikel ger en mer detaljerad beskrivning av den [Azure IoT-enhetens SDK för C – IoTHubClient](iot-hub-device-sdk-c-iothubclient.md). Den här artikeln Slutför täckning av SDK: N genom att tillhandahålla en mer detaljerad beskrivning av komponenten återstående: den **serialiserare** biblioteket.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Inledande artikeln beskrivs hur du använder den **serialiserare** biblioteket för att skicka händelser till och ta emot meddelanden från IoT Hub. I den här artikeln har vi utöka denna diskussion genom att tillhandahålla en fullständig förklaring på hur du kan modellera dina data med den **serialiserare** makrospråk. Artikeln också innehåller mer information om hur biblioteket Serialiserar meddelanden (och i vissa fall hur du kan styra beteendet serialisering). Vi kommer också att beskriva vissa parametrar som du kan ändra som avgör storleken på modeller som du skapar.

Slutligen revisits artikeln vissa avsnitt som beskrivs i föregående artiklar som meddelandet och hantering av egenskapen. Eftersom vi ska ta reda på mer, dessa funktioner fungerar på samma sätt med hjälp av den **serialiserare** bibliotek som de gör med den **IoTHubClient** biblioteket.

Allt som beskrivs i den här artikeln är baserad på den **serialiserare** SDK-exempel. Om du vill följa med kan se den **simplesample\_amqp** och **simplesample\_http** program som ingår i Azure IoT-enhetens SDK för C.

Du hittar den [ **Azure IoT-enhetens SDK för C** ](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplats och visa information om API: et i den [C API-referens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-modeling-language"></a>Modelleringsspråk

Den [Azure IoT-enhetens SDK för C](iot-hub-device-sdk-c-intro.md) artikeln i den här serien som introducerades i **Azure IoT-enhetens SDK för C** modellering språk genom exempel i den **simplesample\_ amqp** program:

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

Som du ser baseras modelleringsspråk på C-makron. Du alltid börjar din definition med **BEGIN\_namnområde** och alltid sluta med **slutet\_namnområde**. Det är vanligt att namnge namnområdet för ditt företag eller, som i följande exempel projektet som du arbetar med.

Vad som ingår i namnområdet är modellen definitioner. I det här fallet är det en enda modell för en anemometer. Återigen modellen kan vara namn som helst, men vanligtvis modellen har namnet för enheten eller typ av data som du vill skicka till IoT Hub.  

Innehåller en definition av händelserna kan du telemetriingång till IoT Hub (den *data*) samt de meddelanden som du kan få från IoT Hub (den *åtgärder*). Som du ser i exemplet har händelser en typ och ett namn. åtgärder som har ett namn och valfria parametrar (var och en med en typ).

Vad visas inte i det här exemplet är ytterligare datatyper som stöds av SDK: N. Vi tar upp det snart.

> [!NOTE]
> IoT Hub som refererar till de data som en enhet skickar till den som *händelser*, medan modelleringsspråk refererar till den som *data* (definieras med hjälp av **WITH_DATA**). På samma sätt, IoT Hub refererar till de data du skickar till enheter som *meddelanden*, medan modelleringsspråk refererar till den som *åtgärder* (definieras med hjälp av **WITH_ACTION**). Tänk på att dessa villkor får användas antingen i den här artikeln.
> 
> 

## <a name="supported-data-types"></a>Datatyper som stöds

Följande datatyper som stöds i modeller som skapats med den **serialiserare** bibliotek:

| Typ | Beskrivning |
| --- | --- |
| double |dubbel precision flyttalsnummer |
| int |32-bitars heltal |
| flyt |med enkel precision flyttalsnummer |
| lång |långt heltal |
| int8\_t |8-bitars heltal |
| int16\_t |16-bitars heltal |
| int32\_t |32-bitars heltal |
| int64\_t |64-bitars heltal |
| bool |boolesk |
| ascii\_char\_ptr |ASCII-sträng |
| EDM\_DATE\_TIME\_OFFSET |förskjutning av slutdatum tid |
| EDM\_GUID |GUID |
| EDM\_BINARY |binary |
| DEKLARERA\_STRUCT |Komplex datatyp |

Låt oss börja med den senaste datatypen. Den **DECLARE\_STRUCT** kan du definiera komplexa datatyper som är grupperingar av primitiva typer. Dessa grupperingar att vi kan definiera en modell som ser ut så här:

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

Vår modell innehåller en enda inträffat av typen **TestType**. **TestType** är en komplex typ med flera medlemmar som sammantagna visar primitiva typer som stöds av den **serialiserare** modellering språk.

Med en modell så här kan vi skriva kod för att skicka data till IoT Hub som visas på följande sätt:

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

Vi i princip, tilldela ett värde till alla medlemmar i den **Test** struktur och sedan anropa **SendAsync** att skicka den **Test** data händelse till molnet. **SendAsync** är en helper-funktion som skickar en enda inträffat till IoT Hub:

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

Den här funktionen Serialiserar angivna händelsen och skickar det till IoT Hub med **IoTHubClient\_SendEventAsync**. Det här är samma kod som beskrivs i föregående artiklar (**SendAsync** kapslar in logiken i en praktisk funktion).

En andra hjälpfunktionen som används i den tidigare koden är **GetDateTimeOffset**. Den här funktionen omvandlar angiven tid till ett värde av typen **EDM\_datum\_tid\_förskjutning**:

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

Observera att serialisering är i JSON-filen som är formatet som genereras av den **serialiserare** biblioteket. Tänk också på att varje medlem i det serialiserade JSON-objektet matchar medlemmarna i den **TestType** som vi definierade i vår modell. Värdena matchar också exakt de som används i koden. Observera dock att binärdata är base64-kodad: ”AQID” är base64-kodning av {0x01, 0x02, 0x03}.

Det här exemplet visar fördelen med att använda den **serialiserare** bibliotek – det gör att vi kan skicka JSON till molnet, utan att behöva hantera uttryckligen serialisering i vårt program. Allt vi behöver bekymra dig om anger värdena för data-händelser i vår modell och sedan anropa enkla API: er för att skicka dessa händelser till molnet.

Med den här informationen kan vi definiera modeller som innehåller en uppsättning datatyper som stöds, inklusive komplexa typer (vi kan även omfatta komplexa typer i andra komplexa typer). Men öppnar den serialiserade JSON som genererats av exemplet ovan en viktig aspekt. *Hur* vi skickar data med den **serialiserare** biblioteket anger exakt hur JSON har formaterats. Varje given tidpunkt är vad vi tar upp nästa.

## <a name="more-about-serialization"></a>Mer om serialisering

Föregående avsnitt visar ett exempel på utdata som genereras av den **serialiserare** biblioteket. I det här avsnittet förklarar vi hur biblioteket Serialiserar data och hur du kan styra den beteende med hjälp av serialisering API: er.

För att kunna gå vidare diskussion om serialisering, kommer vi att arbeta med en ny modell som baseras på en termostat. Först ska vi ange viss erfarenhet på vilket scenario som vi försöker adress.

Vi vill modellera en termostat som mäter temperatur och fuktighet. Varje typ av data ska skickas till IoT Hub på olika sätt. Som standard termostat ingresses temperatur-händelse en gång var 2 minuter. en fuktighet händelse är ingressed var 15: e minut. När antingen händelsen ingressed, måste den innehålla en tidsstämpel som visar tiden att motsvarande temperatur och fuktighet har mäts.

Det här scenariot, vi demonstrerar två olika sätt att utforma data, och förklarar vi effekten att modellering har serialiserade-utdata.

### <a name="model-1"></a>Modellen 1

Här är den första versionen av en modell som har stöd för scenariot ovan:

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

Observera att modellen innehåller två Datahändelser: **Temperatur** och **fuktighet**. Till skillnad från föregående exempel kan typen för varje händelse är en struktur som definieras med hjälp av **DECLARE\_STRUCT**. **TemperatureEvent** innehåller ett mått för temperatur och en tidsstämpel; **HumidityEvent** innehåller ett fuktighet mått och en tidsstämpel. Den här modellen ger oss ett naturligt sätt att modellera data för det scenario som beskrivs ovan. När vi skickar en händelse till molnet, skickar vi antingen en temperatur/tidsstämpel eller ett par för fuktighet/timestamp.

Vi kan skicka en temperatur-händelse till molnet med hjälp av kod som följande:

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

Vi ska använda hårdkodade värden för temperaturen och fuktigheten i exempelkoden, men Tänk dig att vi faktiskt hämtar dessa värden av sampling motsvarande sensorer på termostat.

Koden ovan använder den **GetDateTimeOffset** helper som introducerades tidigare. Skäl som kommer att bli Rensa senare, särskiljer den här koden uttryckligen uppgiften att serialisera och skicka händelsen. Föregående kod Serialiserar temperatur-händelse till en buffert. Sedan **sendMessage** är en hjälpfunktionen (ingår i **simplesample\_amqp**) som skickas händelsen till IoT Hub:

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

Den här koden är en delmängd av den **SendAsync** helper som beskrivs i föregående avsnitt, så vi inte kommer gå igenom den igen här.

När vi kör den tidigare koden att skicka händelsen temperatur, skickas den här serialiserade form av händelsen till IoT Hub:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Vi skickar en temperatur som är av typen **TemperatureEvent** och den strukturen innehåller en **temperatur** och **tid** medlem. Detta återspeglas direkt i den serialiserade informationen.

På samma sätt kan vi skicka en händelse för fuktighet med den här koden:

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Den serialiserade form som skickas till IoT Hub visas på följande sätt:

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Igen, detta är som förväntat.

Med den här modellen kan du föreställa dig hur ytterligare händelser kan enkelt läggas till. Du definierar flera strukturer med **DECLARE\_STRUCT**, och inkludera motsvarande händelse i modellen genom att använda **WITH\_DATA**.

Nu ska vi ändra modellen så att den innehåller samma data, men med olika struktur.

### <a name="model-2"></a>Modell 2

Överväg att den här alternativa modellen till det som anges ovan:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

I det här fallet har vi tagit bort den **DECLARE\_STRUCT** makron och bara definierar dataobjekt från vårt scenario med hjälp av enkla typer från modelleringsspråk.

Bara för tillfället är ignorera den **tid** händelse. Med den aside här är koden för att inkommande **temperatur**:

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

Och koden för att skicka händelsen fuktighet visas på följande sätt:

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Den här koden skickar till IoT Hub:

```C
{"Humidity":45}
```

Än så länge finns det fortfarande inga överraskningar. Nu ska vi ändra hur vi använder makrot SERIALISERA.

Den **SERIALISERA** makro kan ta flera Datahändelser som argument. Detta gör det möjligt för oss att serialisera det **temperatur** och **fuktighet** händelse tillsammans och skicka dem till IoT Hub i ett anrop:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Du kanske tror att resultatet av den här koden är att två händelser skickas till IoT Hub:

[{”Temperatur”: 75}, {”fuktighet”: 45}]

Med andra ord du tror att den här koden är samma sak som att skicka **temperatur** och **fuktighet** separat. Det är bara för att underlätta för att skicka både händelser till **SERIALISERA** i samma anropet. Men är som inte fallet. Koden ovan skickar i stället separata händelsen till IoT Hub:

{”Temperatur”: 75, ”fuktighet”: 45}

Det kan verka underligt eftersom vår modell definierar **temperatur** och **fuktighet** som två *separat* händelser:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Till tidpunkten inte har vi mer modellera dessa händelser där **temperatur** och **fuktighet** finns i samma struktur:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Om du använder den här modellen, skulle det vara lättare att förstå hur **temperatur** och **fuktighet** skulle skickas i samma serialiserade meddelandet. Men det kanske inte är tydliga anledningen till att den fungerar på så sätt när du skickar både Datahändelser till **SERIALISERA** med hjälp av modellen 2.

Det här beteendet är lättare att förstå om du känner till antaganden som den **serialiserare** biblioteket gör. Om du vill att ordna detta går vi tillbaka till vår modell:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Tänk på den här modellen i objektorienterad villkor. I det här fallet vi utformar en fysisk enhet (en termostat) och den enheten innehåller attribut som **temperatur** och **fuktighet**.

Vi kan skicka hela tillståndet för vår modell med kod, till exempel följande:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Under förutsättning att värdena för temperatur, fuktighet och tid är inställda, så visas en händelse inträffar skickas till IoT Hub:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Ibland vill du kanske bara att skicka *vissa* egenskaperna för modellen till molnet (Detta gäller särskilt om modellen innehåller ett stort antal data-händelser). Det är bra att skicka endast en delmängd av Datahändelser, som i vårt tidigare exempel:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Detta genererar exakt samma serialiserade händelse som om vi har definierat en **TemperatureEvent** med en **temperatur** och **tid** medlem, precis som vi med modellera 1. I det här fallet kunde vi skapa exakt samma serialiserade händelse med hjälp av en annan modell (modell 2) eftersom vi har ringt **SERIALISERA** på ett annat sätt.

Det viktiga är som om du skickar flera Datahändelser till **SERIALISERA,** och sedan det förutsätter att varje händelse är en egenskap i ett enda JSON-objekt.

Den bästa metoden är beroende av du och hur du tycker om din modell. Om du skickar ”händelser” till molnet och varje händelse som innehåller en definierad uppsättning egenskaper, gör det första tillvägagångssättet klokt. I så fall skulle du använda **DECLARE\_STRUCT** att definiera strukturen för varje händelse och sedan lägga till dem i din modell med de **WITH\_DATA** makro. Sedan skicka varje händelse som vi gjorde i det första exemplet ovan. Den här metoden skickar du bara en enda händelse **SERIALISERARE**.

Om du tycker om din modell i ett objektorienterat sätt kan sedan den andra metoden passar dig. I det här fallet elementen definieras med hjälp av **WITH\_DATA** är ”egenskaper” för objektet. Du skickar delmängden av händelser till **SERIALISERA** som du vill, beroende på hur mycket av din ”objektets” tillstånd du vill skicka till molnet.

Nether är rätt eller fel. Bara känna till hur **serialiserare** biblioteket fungerar och välj den modellering-metod som bäst passar dina behov.

## <a name="message-handling"></a>Meddelandehantering

Hittills den här artikeln har endast beskrivs skicka händelser till IoT-hubb som åtgärdas inte ta emot meddelanden. Orsaken för detta är som det vi behöver veta om att ta emot meddelanden i hög grad är uppfyllt i artikeln [Azure IoT-enhetens SDK för C](iot-hub-device-sdk-c-intro.md). Såsom anges i den här artikeln du bearbetar meddelanden genom att registrera en återanropsfunktion för meddelandet:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Du kan sedan skriva den återanropsfunktion som anropas när ett meddelande tas emot:

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

Den här implementeringen av **IoTHubMessage** anropar funktionen specifika för varje åtgärd i din modell. Exempel: om din modell definierar den här åtgärden:

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

**SetAirResistance** anropas sedan när meddelandet skickas till din enhet.

Vad vi har inte förklaras ännu är hur den serialiserade versionen av meddelande som ser ut. Med andra ord, om du vill skicka en **SetAirResistance** meddelande till din enhet, hur den ut?

Om du skickar ett meddelande till en enhet, skulle du göra det via tjänsten Azure IoT SDK: N. Du behöver fortfarande veta vilka strängen som ska skicka att anropa en viss åtgärd. Det allmänna formatet för att skicka ett meddelande visas på följande sätt:

```C
{"Name" : "", "Parameters" : "" }
```

Du skickar ett serialiserade JSON-objekt med två egenskaper: **Namnet** är namnet på åtgärden (meddelande) och **parametrar** innehåller parametrar för åtgärden.

Till exempel för att anropa **SetAirResistance** du kan skicka det här meddelandet till en enhet:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

Åtgärdsnamnet måste exakt matcha en åtgärd som definierats i din modell. Parameternamnet måste matcha samt. Tänk också på skiftlägeskänslighet. **Namnet** och **parametrar** alltid är versaler. Se till att matcha gemener/versaler åtgärdsnamn och parametrar i din modell. I det här exemplet är namnet på åtgärden ”SetAirResistance” och inte ”setairresistance”.

De två andra åtgärderna **TurnFanOn** och **TurnFanOff** kan anropas genom att skicka dessa meddelanden till en enhet:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

Det här avsnittet beskrivs allt du behöver veta när skicka händelser och som tar emot meddelanden med den **serialiserare** biblioteket. Innan du fortsätter, vi beskriver vissa parametrar som du kan konfigurera som styr hur stor din modell är.

## <a name="macro-configuration"></a>Makrot konfiguration

Om du använder den **serialiserare** biblioteket som en viktig del av SDK, för att vara medveten om finns i azure-c-delad-verktyget-biblioteket.

Om du har klonat lagringsplatsen Azure-iot-sdk-c från GitHub med hjälp av alternativet – rekursiv, hittar du det här biblioteket för delade verktyg här:

```C
.\\c-utility
```

Om du inte har klona i biblioteket, kan du hitta den [här](https://github.com/Azure/azure-c-shared-utility).

I biblioteket delade verktyg kan hittar du följande mapp:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Den här mappen innehåller en Visual Studio-lösning som kallas **makrot\_utils\_h\_generator.sln**:

  ![Skärmbild av maco_utils_h_generator för Visual Studio-lösning](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

I den här lösningen skapas den **makrot\_utils.h** fil. Det finns ett standard-makro\_utils.h-fil som ingår i SDK. Den här lösningen kan du ändra vissa parametrar och sedan återskapa huvudfilen baserat på dessa parametrar.

De två viktiga parametrar berördes med är **nArithmetic** och **nMacroParameters** som definieras i de här två raderna i makrot\_utils.tt:

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Dessa värden är standardparametrar som ingår i SDK. Varje parameter har följande betydelse:

* nMacroParameters – styr hur många parametrar som du kan ha i en DECLARE\_makrodefinition i MODELLEN.
* nArithmetic – styr det totala antalet medlemmar som tillåts i en modell.

Orsaken till dessa parametrar är viktiga beror på de kontrollera hur stor din modell kan vara. Anta exempelvis att den här modellen definition:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Som nämnts tidigare **DECLARE\_MODELLEN** är bara ett C-makro. Namnen på modellen och **WITH\_DATA** instruktionen (har ett annat makro) är parametrarna för **DECLARE\_MODELLEN**. **nMacroParameters** definierar hur många parametrar kan ingå i **DECLARE\_MODELLEN**. Detta definierar effektivt, hur många data händelse och åtgärd deklarationer att du kan ha. Därför med Standardgränsen för 124 innebär det att du kan definiera en modell med en kombination av cirka 60 åtgärder och data-händelser. Om du överskrider den här gränsen, får du Kompilatorfel som ser ut ungefär så här:

  ![Skärmbild av Kompilatorfel för makrot parametrar](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

Den **nArithmetic** parametern finns mer information om den interna bearbetningen av makrospråk än ditt program.  Den kontrollerar det totala antalet medlemmar som du kan ha i din modell, inklusive **DECLARE_STRUCT** makron. Om du börja Se Kompilatorfel som detta så bör du försöka ökar **nArithmetic**:

   ![Skärmbild av aritmetiskt Kompilatorfel](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Om du vill ändra de här parametrarna kan du ändra värdena för makrot\_utils.tt filen, kompilerar makrot\_utils\_h\_generator.sln lösningen och kör kompilerade programmet. När du gör detta visas ett nytt makro\_utils.h filen genereras och placeras i den.\\ vanliga\\inc directory.

För att kunna använda den nya versionen av makrot\_utils.h, ta bort den **serialiserare** NuGet-paketet från din lösning och i dess ställe inkluderar den **serialiserare** Visual Studio-projekt. På så sätt kan din kod ska kompileras mot källkoden för serialiserare-biblioteket. Detta inkluderar uppdaterade makrot\_utils.h. Om du vill göra detta för **simplesample\_amqp**, starta genom att ta bort NuGet-paketet för serialiserare-biblioteket från lösningen:

   ![Skärmbild av att ta bort NuGet-paketet för serialiserare-biblioteket](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Lägg sedan till det här projektet i Visual Studio-lösningen:

> .\\c\\serializer\\build\\windows\\serializer.vcxproj
> 
> 

När du är klar bör din lösning se ut så här:

   ![Skärmbild av simplesample_amqp Visual Studio-lösning](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Nu när du kompilerar din lösning, uppdaterade makrot\_utils.h ingår i din binary.

Observera att öka dessa värden som är tillräckligt högt kan överstiga gränserna för kompilatorn. Så här långt i **nMacroParameters** är den viktigaste parameter som berördes. C99-specifikationen anger att minst 127 parametrar tillåts i en makrodefinition. Microsoft-kompilatorn följer specifikationen exakt (och är begränsad till 127), så du kan inte öka **nMacroParameters** bortom standardvärdet. Andra kompilatorer kan tillåta dig att göra detta (till exempel GNU-kompilatorn stöder en högre gräns).

Så här långt vi har gått igenom nästan allt du behöver veta om hur du skriver kod med den **serialiserare** biblioteket. Innan du Final, vi gå tillbaka till vissa avsnitt från föregående artiklar som du kanske undrar om.

## <a name="the-lower-level-apis"></a>På lägre nivå-API: er
Exempelprogrammet som den här artikeln fokuserar är **simplesample\_amqp**. Det här exemplet används det på högre nivå (det icke -**lla**) API: er för att skicka händelser och ta emot meddelanden. Om du använder dessa API: er, kör en bakgrundstråd som tar hand om både skicka händelser och ta emot meddelanden. Du kan dock använda API: er för på lägre nivå (alla) för att eliminera den här bakgrundstråd och ta explicit kontroll över när du skickar händelser eller ta emot meddelanden från molnet.

Mer information finns i en [föregående artikel](iot-hub-device-sdk-c-iothubclient.md), det finns en uppsättning funktioner som består av på högre nivå-API: er:

* IoTHubClient\_CreateFromConnectionString
* IoTHubClient\_SendEventAsync
* IoTHubClient\_SetMessageCallback
* IoTHubClient\_Destroy

Dessa API: er är visas i **simplesample\_amqp**.

Det finns också en liknande uppsättning API: er på lägre nivå.

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

Observera att på lägre nivå-API: er fungerar exakt på samma sätt som beskrivs i föregående artiklar. Du kan använda den första uppsättningen med API: er om du vill att en bakgrundstråd att hantera händelser som sändande och mottagande meddelanden. Du använder den andra uppsättningen av API: er om du vill ha explicita kontroll över när du skickar och tar emot data från IoT Hub. Antingen uppsättning API: er fungerar lika bra med de **serialiserare** biblioteket.

Ett exempel på hur API: er för på lägre nivå som används med den **serialiserare** -biblioteket finns i den **simplesample\_http** program.

## <a name="additional-topics"></a>Ytterligare information
Några andra ämnen värt att nämna är igen egenskapen hantera, med hjälp av alternativa enhetens autentiseringsuppgifter och konfigurationsalternativ. Det här är alla ämnena i en [föregående artikel](iot-hub-device-sdk-c-iothubclient.md). Viktiga är att alla dessa funktioner fungerar på samma sätt som med den **serialiserare** bibliotek som de gör med den **IoTHubClient** biblioteket. Till exempel om du vill koppla egenskaper till en händelse från din modell kan du använda **IoTHubMessage\_egenskaper** och **kartan**\_**AddorUpdate**, på samma sätt som beskrivs tidigare:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Om händelsen har genererats från den **serialiserare** biblioteket eller skapas manuellt med hjälp av den **IoTHubClient** spelar ingen roll biblioteket.

För alternativa enhetsautentiseringsuppgifterna, med hjälp av **IoTHubClient\_lla\_skapa** fungerar precis lika bra som **IoTHubClient\_CreateFromConnectionString** för allokera en **IOTHUB\_klienten\_hantera**.

Slutligen, om du använder den **serialiserare** biblioteket, kan du ange konfigurationsalternativ med **IoTHubClient\_lla\_SetOption** precis som du gjorde när du använder **IoTHubClient** biblioteket.

En funktion som är unik för den **serialiserare** biblioteket är initieringen API: er. Innan du kan börja arbeta med biblioteket, måste du anropa **serialiserare\_init**:

```C
serializer_init(NULL);
```

Det gör du precis innan du anropar **IoTHubClient\_CreateFromConnectionString**.

På samma sätt när du är klar arbetar med biblioteket, det senaste anropet ska du göra är att **serialiserare\_deinit**:

```C
serializer_deinit();
```

I annat fall alla andra funktioner som anges ovan fungerar på samma den **serialiserare** biblioteket som i den **IoTHubClient** biblioteket. Mer information om något av de här ämnena finns i den [föregående artikel](iot-hub-device-sdk-c-iothubclient.md) i den här serien.

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs i detalj unika aspekter av den **serialiserare** biblioteket i den **Azure IoT-enhetens SDK för C**. Med informationen som tillhandahålls i bör du ha en god förståelse för hur du använder modeller att skicka händelser och ta emot meddelanden från IoT Hub.

Detta avslutar också i tre delar om hur du utvecklar program med den **Azure IoT-enhetens SDK för C**. Det bör vara tillräckligt med information inte bara komma igång utan att ge dig en grundlig genomgång av hur API: er fungerar. För ytterligare information finns det några exempel i SDK som inte beskrivs här. I annat fall den [Azure IoT SDK-dokumentation](https://github.com/Azure/azure-iot-sdk-c) är en bra resurs för ytterligare information.

Mer information om hur du utvecklar för IoT Hub finns det [Azure IoT SDK: er](iot-hub-devguide-sdks.md).

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se [distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).