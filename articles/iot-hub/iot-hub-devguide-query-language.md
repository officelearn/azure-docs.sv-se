---
title: "Förstå Azure IoT Hub-frågespråket | Microsoft Docs"
description: "Utvecklarhandbok - beskrivning av SQL-liknande IoT-hubb frågespråk som används för att hämta information om enheten twins och jobb från din IoT-hubb."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 851a9ed3-b69e-422e-8a5d-1d79f91ddf15
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: elioda
ms.openlocfilehash: 450f2d38f7b641bcf6b8be061969404a1b582b4c
ms.sourcegitcommit: 7d4b3cf1fc9883c945a63270d3af1f86e3bfb22a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
---
# <a name="iot-hub-query-language-for-device-twins-jobs-and-message-routing"></a>IoT-hubb frågespråk för enheten twins, jobb och meddelanderoutning

IoT-hubb ger en kraftfull SQL-liknande språk för att hämta information om [enhet twins] [ lnk-twins] och [jobb][lnk-jobs], och [meddelanderoutning][lnk-devguide-messaging-routes]. Den här artikeln beskriver:

* En introduktion till de viktigaste funktionerna i frågespråk IoT-hubb och
* Detaljerad beskrivning av språket.

## <a name="device-twin-queries"></a>Enheten dubbla frågor
[Enheten twins] [ lnk-twins] godtyckliga JSON-objekt kan innehålla både taggar och egenskaper. IoT-hubb kan du fråga enheten twins som enda JSON-dokument som innehåller alla dubbla enhetsinformation.
Anta exempelvis att din IoT-hubb enheten twins har följande struktur:

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00",    
    "connectionState": "Disconnected",    
    "lastActivityTime": "0001-01-01T00:00:00",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",    
    "x509Thumbprint": {    
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

IoT-hubb visar twins för enheten som en dokumentsamling som heter **enheter**.
Följande fråga hämtar så hela uppsättningen med enheten twins:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Azure IoT-SDK] [ lnk-hub-sdks] stöd för sidindelning av stora resultat.

IoT-hubb kan du hämta enheten twins med valfri villkor för filtrering. Till exempel att ta emot enheten twins var den **location.region** taggen är inställd på **USA** använder du följande fråga:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Booleska operatorer och aritmetiskt jämförelser stöds också. Till exempel använda twins finns i USA och konfigurerad att skicka telemetri om mindre än en gång per minut för att hämta enheten följande fråga:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

I syfte att underlätta, det är också möjligt att använda matriskonstanter med den **IN** och **ni** (inte i) operatörer. Till exempel använda enheten twins som rapporterar Wi-Fi eller trådbunden anslutning för att hämta följande fråga:

```sql
SELECT * FROM devices
WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Det är ofta krävs för att identifiera alla enheter twins som innehåller en specifik egenskap. IoT-hubb stöder funktionen `is_defined()` för detta ändamål. Till exempel att hämta enheten twins som definierar den `connectivity` egenskapen Använd följande fråga:

```SQL
SELECT * FROM devices
WHERE is_defined(properties.reported.connectivity)
```

Referera till den [WHERE-satsen] [ lnk-query-where] avsnittet för fullständig referens av filtreringsfunktioner.

Gruppering och aggregeringar stöds också. Till exempel använda Konfigurationsstatus för att hitta antal enheter i varje telemetri följande fråga:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Den här grupperingen frågan skulle returnera ett resultat som liknar följande exempel. Här kan tre enheter rapporterar lyckad konfiguration, två fortfarande använder konfigurationen och en rapporterade ett fel. 

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

Projektionsfrågor kan utvecklare returnerar bara de egenskaper som de är intresserad av. Till exempel kopplas om du vill hämta den senaste aktivitetstiden för alla enheter, Använd följande fråga:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="c-example"></a>C#-exempel
Frågefunktioner som exponeras av den [C# service SDK] [ lnk-hub-sdks] i den **RegistryManager** klass.
Här är ett exempel på en enkel fråga:

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

Observera hur **frågan** instans-objekt med en storlek (upp till 100) och sedan flera sidor kan hämtas genom att anropa den **GetNextAsTwinAsync** metoder flera gånger.
Observera att frågeobjektet innehåller flera **nästa***, beroende på alternativet deserialisering krävs av frågan, till exempel dubbla eller jobb enhetsobjekt eller vanligt JSON som ska användas när du använder projektioner.

### <a name="nodejs-example"></a>Node.js-exempel
Frågefunktioner som exponeras av den [Azure IoT service SDK för Node.js] [ lnk-hub-sdks] i den **registret** objekt.
Här är ett exempel på en enkel fråga:

```nodejs
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

Observera hur **frågan** instans-objekt med en storlek (upp till 100) och sedan flera sidor kan hämtas genom att anropa den **nextAsTwin** metoder flera gånger.
Observera att frågeobjektet innehåller flera **nästa***, beroende på alternativet deserialisering krävs av frågan, till exempel dubbla eller jobb enhetsobjekt eller vanligt JSON som ska användas när du använder projektioner.

### <a name="limitations"></a>Begränsningar
> [!IMPORTANT]
> Frågeresultatet kan ha några minuter för fördröjning i förhållande till de senaste värdena i enheten twins. Om frågar enskild enhet twins-ID: t är det alltid bättre att använda hämta enheten dubbla API, som alltid innehåller de senaste värdena och som har högre throttling-begränsning.

För närvarande jämförelser stöds endast mellan primitiva typer (inga objekt), till exempel `... WHERE properties.desired.config = properties.reported.config` stöds endast om dessa egenskaper har primitiva värden.

## <a name="get-started-with-jobs-queries"></a>Kom igång med jobb-frågor
[Jobb] [ lnk-jobs] ger dig ett sätt att utföra åtgärder på uppsättningar av enheter. Varje enhet dubbla innehåller information om de jobb som den är en del i en samling som heter **jobb**.
Logiskt

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

Den här samlingen är för närvarande frågbar som **devices.jobs** frågespråket i IoT-hubben.

> [!IMPORTANT]
> För närvarande returneras egenskapen jobb aldrig när du frågar enheten twins (frågor som innehåller 'från enheter-). Endast kan nås direkt med frågor med `FROM devices.jobs`.
>
>

Du kan exempelvis använda följande fråga för att hämta alla jobb (tidigare och schemalagda) som påverkar en enstaka enhet:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
```

Observera hur den här frågan ger enhetsspecifika status (och direkta metodsvaret) för varje jobb som returneras.
Det är också möjligt att filtrera med valfri boolesk villkor för alla objektegenskaper i den **devices.jobs** samling.
Till exempel om du vill hämta alla slutförda enheten dubbla uppdateringsjobb som skapades efter September 2016 för en specifik enhet, använder du följande fråga:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Du kan också hämta resultat för ett enda jobb per enhet.

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Begränsningar
För närvarande en sökning på **devices.jobs** stöder inte:

* Projektioner därför bara `SELECT *` är möjligt.
* Villkor som refererar till enheten identiska förutom jobbegenskaper (finns i föregående avsnitt).
* Utföra aggregeringar, till exempel antal, avg, gruppera efter.

## <a name="device-to-cloud-message-routes-query-expressions"></a>Enhet till moln meddelandevägar fråga uttryck

Med hjälp av [enhet till moln vägar][lnk-devguide-messaging-routes], kan du konfigurera IoT-hubb för att skicka meddelanden från enhet till moln till olika slutpunkter som är baserade på uttryck som utvärderas mot enskilda meddelanden.

Vägen [villkoret] [ lnk-query-expressions] använder samma IoT-hubb-frågespråket i villkoren för dubbla och jobb. Väg villkor utvärderas på meddelandehuvuden och brödtext. Din routning frågeuttryck kan innebära endast meddelanderubriker, endast meddelandetexten, eller båda meddelande sidhuvuden och meddelande. IoT-hubb förutsätter ett visst schema för sidhuvuden och meddelandetexten för att vidarebefordra meddelanden. I följande avsnitt beskrivs vad som krävs för IoT-hubb att dirigera korrekt.

### <a name="routing-on-message-headers"></a>Routning på meddelanderubriker

IoT-hubb förutsätter följande JSON-representation av meddelandehuvuden för meddelanderoutning:

```json
{
    "$messageId": "",
    "$enqueuedTime": "",
    "$to": "",
    "$expiryTimeUtc": "",
    "$correlationId": "",
    "$userId": "",
    "$ack": "",
    "$connectionDeviceId": "",
    "$connectionDeviceGenerationId": "",
    "$connectionAuthMethod": "",
    "$content-type": "",
    "$content-encoding": "",

    "userProperty1": "",
    "userProperty2": ""
}
```

Meddelandet Systemegenskaper föregås av `'$'` symbolen.
Egenskaper för användare nås alltid med deras namn. Om ett användarnamn för egenskapen händer med sammanfaller med en systemegenskap (exempelvis `$to`), egenskapen hämtas med den `$to` uttryck.
Du kan alltid komma åt Systemegenskapen med hakparenteser `{}`: du kan till exempel använda uttrycket `{$to}` att komma åt Systemegenskapen `to`. Inom hakparenteser egenskapsnamn hämta alltid Systemegenskapen motsvarande.

Kom ihåg att egenskapsnamn är skiftlägeskänsliga.

> [!NOTE]
> Alla egenskaper är strängar. Systemegenskaper, enligt beskrivningen i den [Utvecklarhandbok][lnk-devguide-messaging-format], är inte tillgänglig för användning i frågor.
>

Om du använder till exempel en `messageType` egenskap, kanske du vill vidarebefordra all telemetri till en slutpunkt och alla aviseringar till en annan slutpunkt. Du kan skriva följande uttryck för att dirigera telemetrin:

```sql
messageType = 'telemetry'
```

Och följande uttryck för att dirigera aviseringsmeddelanden:

```sql
messageType = 'alert'
```

Booleskt uttryck och funktioner stöds också. Den här funktionen kan du skilja mellan allvarlighetsgrad, till exempel:

```sql
messageType = 'alerts' AND as_number(severity) <= 2
```

Referera till den [uttryck och villkor] [ lnk-query-expressions] avsnittet för en fullständig lista över stöds operatorer och funktioner.

### <a name="routing-on-message-bodies"></a>Routning på meddelandetext

IoT-hubb kan endast vidarebefordra baserat på meddelandetexten innehållet om meddelandetexten är korrekt formaterad JSON kodning i UTF-8, UTF-16 eller UTF-32. Ange innehållstypen i meddelandet för att `application/json` och Innehållskodning till någon av teckenkodningar som stöds UTF i meddelandena. Om något av huvudena har angetts försöker inte IoT-hubb utvärdera eventuella frågeuttryck som rör innehållet mot meddelandet. Om meddelandet inte är ett JSON-meddelande, eller om meddelandet inte anger content-type och Innehållskodning, kan du fortfarande använda meddelanderoutning för att vidarebefordra meddelandet baserat på meddelandena.

Du kan använda `$body` i frågeuttrycket att skicka meddelandet. Du kan använda en enkel body-referens, brödtext matrisreferens eller flera body-referenser i frågeuttrycket. Din frågeuttryck kan också kombinera en brödtext referens med en referens för sidhuvudet. Följande är alla giltig fråga uttryck:

```sql
$body.message.Weather.Location.State = 'WA'
$body.Weather.HistoricalData[0].Month = 'Feb'
$body.Weather.Temperature = 50 AND $body.message.Weather.IsEnabled
length($body.Weather.Location.State) = 2
$body.Weather.Temperature = 50 AND Status = 'Active'
```

## <a name="basics-of-an-iot-hub-query"></a>Grunderna i en fråga för IoT-hubb
Alla IoT-hubb fråga består av väljer och från satser med valfria WHERE och GROUP BY-satser. Varje fråga körs på en mängd JSON-dokument, till exempel enhet twins. FROM-satsen anger dokumentsamlingen till hävdade på (**enheter** eller **devices.jobs**). Sedan används filter i WHERE-satsen. Med aggregeringar, resultatet av det här steget grupperas som angetts i GROUP BY-satsen och för varje grupp skapas en rad som angetts i SELECT-satsen.

```sql
SELECT <select_list>
FROM <from_specification>
[WHERE <filter_condition>]
[GROUP BY <group_specification>]
```

## <a name="from-clause"></a>FROM-satsen
Den **från < from_specification >** satsen anta bara två värden: **från enheter** till frågan enhet twins eller **från devices.jobs** till Frågedetaljer jobb per enhet.

## <a name="where-clause"></a>WHERE-satsen
Den **där < filter_condition >** -satsen är valfria. Anger ett eller flera villkor som JSON-dokument i samlingen från måste uppfylla för att ingå som en del av resultatet. JSON-dokument måste utvärdera de angivna villkoren på ”true” ska ingå i resultatet.

Tillåtna villkor beskrivs i avsnittet [uttryck och villkor][lnk-query-expressions].

## <a name="select-clause"></a>SELECT-satsen
Den **väljer < select_list >** är obligatoriskt och anger vilka värden hämtas från frågan. Det anger JSON-värden som används för att generera nya JSON-objekt.
För varje element i delmängden filtrerade (och alternativt grupperade) i mängden från genererar Projektionsfasen ett nytt JSON-objekt med de värden som anges i SELECT-satsen.

Nedan följer grammatik i SELECT-satsen:

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

**Attribute_name** refererar till en egenskap av JSON-dokumentet i FROM-samlingen. Några exempel på SELECT-satser kan hittas i den [komma igång med enheten dubbla frågor] [ lnk-query-getstarted] avsnitt.

För närvarande markeringen satser skiljer sig **Välj*** stöds endast i sammanställd frågor för enheten twins.

## <a name="group-by-clause"></a>GROUP BY-sats
Den **GROUP BY < group_specification >** -satsen är ett valfritt steg som kan utföras efter det filter som angetts i WHERE-satsen och innan projektionen som anges i listan Välj. Grupperas dokument baserat på värdet för ett attribut. Dessa grupper används för att generera sammanställda värden som anges i SELECT-satsen.

Ett exempel på en fråga med GROUP BY är:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Formella syntaxen för GROUP BY är:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** refererar till en egenskap av JSON-dokumentet i FROM-samlingen.

GROUP BY-satsen stöds för närvarande bara när du frågar enheten twins.

## <a name="expressions-and-conditions"></a>Uttryck och villkor
På en hög nivå, en *uttryck*:

* Returnerar en instans av en JSON-typ (till exempel booleskt värde, antal, sträng, matris eller objekt).
* Definieras av manipulera data från enheten JSON-dokumentet och konstanter med hjälp av inbyggda operatorer och funktioner.

*Villkor* är uttryck som utvärderas till ett booleskt värde. En konstant som skiljer sig boolesk **true** anses **FALSKT** (inklusive **null**, **Odefinierad**, valfri instans av objektet eller matrisen valfri sträng och tydligt av booleskt **FALSKT**).

Syntaxen för uttryck är:

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

Se tabellen nedan för att förstå vad varje symbol i uttryck syntax står för:

| Symbol | Definition |
| --- | --- |
| attribute_name | En egenskap av JSON-dokument i den **FROM** samling. |
| binary_operator | En binär operator som anges i den [operatörer](#operators) avsnitt. |
| function_name| En funktion som anges i den [funktioner](#functions) avsnitt. |
| decimal_literal |Ett flyttal uttryckt i decimalformat. |
| hexadecimal_literal |Ett tal anges av strängen ”0 x” följt av en sträng med hexadecimala siffror. |
| string_literal |Stränglitteraler är Unicode-strängar som representeras av en följd av noll eller flera Unicode-tecken eller escape-sekvenser. Stränglitteraler omges av enkla citattecken eller dubbla citattecken. Tillåtna visar: `\'`, `\"`, `\\`, `\uXXXX` för Unicode-tecken som definieras av 4 hexadecimala siffror. |

### <a name="operators"></a>Operatorer
Följande operatorer stöds:

| Familj | Operatorer |
| --- | --- |
| Aritmetiska |+, -, *, /, % |
| Logiska |OCH, ELLER INTE |
| Jämförelse |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Funktioner
När du frågar twins och jobb som det går endast att är funktionen:

| Funktion | Beskrivning |
| -------- | ----------- |
| IS_DEFINED(Property) | Returnerar ett booleskt värde som anger om egenskapen har tilldelats ett värde (inklusive `null`). |

Följande matematiska funktioner stöds i vägar villkor:

| Funktion | Beskrivning |
| -------- | ----------- |
| ABS(x) | Returnerar det absoluta (positiva) värdet av uttryck. |
| EXP(x) | Returnerar exponentialfördelningen värdet för det angivna numeriska uttrycket (e ^ x). |
| Power(x,y) | Returnerar värdet för det angivna uttrycket till angiven potens (x ^ y).|
| Square(x) | Returnerar kvadratroten av det angivna numeriska värdet. |
| CEILING(x) | Returnerar det minsta heltalsvärdet större än eller lika med det angivna numeriska uttrycket. |
| FLOOR(x) | Returnerar det största heltalet mindre än eller lika med det angivna numeriska uttrycket. |
| Sign(x) | Returnerar positivt (+ 1), noll (0) eller minustecken (-1) för det angivna numeriskt uttrycket.|
| Rot(x) | Returnerar kvadratroten av det angivna numeriskt värdet. |

I vägar villkor stöds följande typkontroll och omvandling funktioner:

| Funktion | Beskrivning |
| -------- | ----------- |
| AS_NUMBER | Konverterar den inmatade strängen till ett tal. `noop`Om indata är ett tal. `Undefined` om strängen inte representerar ett tal.|
| IS_ARRAY | Returnerar ett booleskt värde som anger om det angivna uttrycket är en matris. |
| IS_BOOL | Returnerar ett booleskt värde som anger om det angivna uttrycket är ett booleskt värde. |
| IS_DEFINED | Returnerar ett booleskt värde som anger om egenskapen har tilldelats ett värde. |
| IS_NULL | Returnerar ett booleskt värde som anger om typ av det angivna uttrycket är null. |
| IS_NUMBER | Returnerar ett booleskt värde som anger om typ av det angivna uttrycket är ett tal. |
| IS_OBJECT | Returnerar ett booleskt värde som anger om det angivna uttrycket är ett JSON-objekt. |
| IS_PRIMITIVE | Returnerar ett booleskt värde som anger om typ av det angivna uttrycket är en primitiv (string, Boolean, numeriska eller `null`). |
| IS_STRING | Returnerar ett booleskt värde som anger om det angivna uttrycket är en sträng. |

Följande sträng-funktioner stöds i vägar villkor:

| Funktion | Beskrivning |
| -------- | ----------- |
| SAMMANFOGA (x, y,...) | Returnerar en sträng som är resultatet av att sammanfoga två eller flera strängvärden. |
| LENGTH(x) | Returnerar antalet tecken i angivet stränguttryck.|
| LOWER(x) | Returnerar ett stränguttryck när versal data har konverterats till gemener. |
| UPPER(x) | Returnerar ett stränguttryck när gemen data har konverterats till versaler. |
| SUBSTRING (sträng, start [, längd]) | Returnerar en del av ett stränguttryck som börjar vid den angivna nollbaserade teckenpositionen och fortsätter med den angivna längden eller till slutet av strängen. |
| INDEX_OF (string, fragment) | Returnerar startpositionen för den första förekomsten av andra stränguttryck inom det första uttrycket i strängen, eller -1 om strängen inte hittas.|
| STARTS_WITH (x, y) | Returnerar ett booleskt värde som anger om först stränguttryck börjar med andra. |
| ENDS_WITH (x, y) | Returnerar ett booleskt värde som anger om först stränguttryck slutar med andra. |
| CONTAINS(x,y) | Returnerar ett booleskt värde som anger om först stränguttryck innehåller andra. |

## <a name="next-steps"></a>Nästa steg
Lär dig att köra frågor i dina appar med hjälp av [Azure IoT SDK][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#get-started-with-device-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging-routes]: iot-hub-devguide-messages-read-custom.md
[lnk-devguide-messaging-format]: iot-hub-devguide-messages-construct.md
[lnk-devguide-messaging-routes]: ./iot-hub-devguide-messages-read-custom.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
