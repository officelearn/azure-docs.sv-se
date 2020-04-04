---
title: Förstå frågespråket Azure IoT Hub | Microsoft-dokument
description: Utvecklarguide - beskrivning av det SQL-liknande IoT Hub-frågespråk som används för att hämta information om enhets-/modultvillingar och jobb från din IoT-hubb.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: robinsh
ms.openlocfilehash: bcc53322ac6942b52853be561bc3441e23fbf53b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632938"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>IoT Hub-frågespråk för enhet och modultvillingar, jobb och meddelanderedigering

IoT Hub ger ett kraftfullt SQL-liknande språk för att hämta information om [enhetstvillingar,](iot-hub-devguide-device-twins.md) [modultvillingar,](iot-hub-devguide-module-twins.md) [jobb](iot-hub-devguide-jobs.md)och [meddelanderoutning](iot-hub-devguide-messages-d2c.md). Denna artikel presenterar:

* En introduktion till de viktigaste funktionerna i IoT Hub-frågespråket och
* Den detaljerade beskrivningen av språket. Mer information om frågespråk för meddelanderoutning finns [i frågor i meddelanderoutning](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Dubbelfrågor för enhet och modul

[Enhetstvillingar](iot-hub-devguide-device-twins.md) och [modultvillingar](iot-hub-devguide-module-twins.md) kan innehålla godtyckliga JSON-objekt som både taggar och egenskaper. IoT Hub kan du fråga enhet tvillingar och tvillingar modul som en enda JSON dokument som innehåller all dubbel information.

Anta till exempel att din IoT hub enhet tvillingar har följande struktur (modul twin skulle vara liknande bara med en extra moduleId):

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

### <a name="device-twin-queries"></a>Dubbla frågor om enhet

IoT Hub exponerar enhetstvillingarna som en dokumentsamling som kallas **enheter**. Följande fråga hämtar till exempel hela uppsättningen enhetstvillingar:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Azure IoT SDK-filer](iot-hub-devguide-sdks.md) stöder växling av stora resultat.

IoT Hub kan du hämta enhetstvillingar filtrering med godtyckliga villkor. Om du till exempel vill ta emot enhetstvillingar där taggen **location.region** är inställd på **USA** använder du följande fråga:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Booleska operatörer och aritmetiska jämförelser stöds också. Om du till exempel vill hämta enhetstvillingar som finns i USA och konfigureras för att skicka telemetri mindre än varje minut använder du följande fråga:

```sql
SELECT * FROM devices
  WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Som en bekvämlighet är det också möjligt att använda matriskonstanter med **in-** och **NIN-operatorerna** (inte i). Om du till exempel vill hämta enhetstvillingar som rapporterar WiFi eller trådbunden anslutning använder du följande fråga:

```sql
SELECT * FROM devices
  WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Det är ofta nödvändigt att identifiera alla enhetstvillingar som innehåller en specifik egenskap. IoT Hub stöder `is_defined()` funktionen för detta ändamål. Om du till exempel vill `connectivity` hämta enhetstvillingar som definierar egenskapen använder du följande fråga:

```SQL
SELECT * FROM devices
  WHERE is_defined(properties.reported.connectivity)
```

Se [avsnittet WHERE-satsen](iot-hub-devguide-query-language.md#where-clause) för fullständig referens av filtreringsfunktionerna.

Gruppering och aggregeringar stöds också. Om du till exempel vill hitta antalet enheter i varje telemetrikonfigurationsstatus använder du följande fråga:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
  FROM devices
  GROUP BY properties.reported.telemetryConfig.status
```

Den här grupperingsfrågan returnerar ett resultat som liknar följande exempel:

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

I det här exemplet rapporterade tre enheter lyckad konfiguration, två tillämpar fortfarande konfigurationen och en rapporterade ett fel.

Projektionsfrågor gör det möjligt för utvecklare att returnera endast de egenskaper de bryr sig om. Om du till exempel vill hämta den senaste aktivitetstiden för alla frånkopplade enheter använder du följande fråga:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Modul dubbla frågor

Att fråga på modultvillingar liknar att fråga på enhetstvillingar, men med hjälp av ett annat samlings-/namnområde. i stället för från **enheter**frågar du från **devices.modules**:

```sql
SELECT * FROM devices.modules
```

Vi tillåter inte koppling mellan enheterna och devices.modules-samlingarna. Om du vill fråga modultvillingar mellan enheter gör du det baserat på taggar. Den här frågan returnerar alla modultvillingar på alla enheter med skanningsstatus:

```sql
SELECT * FROM devices.modules WHERE properties.reported.status = 'scanning'
```

Den här frågan returnerar alla modultvillingar med skanningsstatus, men endast på den angivna delmängden av enheter:

```sql
SELECT * FROM devices.modules
  WHERE properties.reported.status = 'scanning'
  AND deviceId IN ['device1', 'device2']
```

### <a name="c-example"></a>C# exempel

Frågefunktionen visas av [C#-tjänsten SDK](iot-hub-devguide-sdks.md) i klassen **RegistryManager.**

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

**Frågeobjektet** instansieras med en sidstorlek (upp till 100). Sedan hämtas flera sidor genom att anropa **GetNextAsTwinAsync-metoderna** flera gånger.

Frågeobjektet visar **Next** flera Nästa-värden, beroende på det deserialiseringsalternativ som krävs av frågan. Till exempel enhetstvilling- eller jobbobjekt eller vanlig JSON när du använder projektioner.

### <a name="nodejs-example"></a>Nod.js exempel

Frågefunktionen visas av [Azure IoT-tjänsten SDK för nod.js](iot-hub-devguide-sdks.md) i **registerobjektet.**

Här är ett exempel på en enkel fråga:

```javascript
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

**Frågeobjektet** instansieras med en sidstorlek (upp till 100). Sedan hämtas flera sidor genom att anropa **nextAsTwin-metoden** flera gånger.

Frågeobjektet visar **Next** flera Nästa-värden, beroende på det deserialiseringsalternativ som krävs av frågan. Till exempel enhetstvilling- eller jobbobjekt eller vanlig JSON när du använder projektioner.

### <a name="limitations"></a>Begränsningar

> [!IMPORTANT]
> Frågeresultat kan ha några minuters fördröjning med avseende på de senaste värdena i enhetstvillingar. Om du frågar enskilda enhetstvillingar efter ID använder du [det dubbla REST API:et](https://docs.microsoft.com/rest/api/iothub/service/twin/getdevicetwin). Det här API:et returnerar alltid de senaste värdena och har högre begränsningsgränser. Du kan utfärda REST API direkt eller använda motsvarande funktioner i en av [Azure IoT Hub Service SDK:er](iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks).

För närvarande stöds jämförelser endast mellan primitiva typer `... WHERE properties.desired.config = properties.reported.config` (inga objekt), till exempel stöds endast om dessa egenskaper har primitiva värden.

## <a name="get-started-with-jobs-queries"></a>Komma igång med jobbfrågor

[Jobb](iot-hub-devguide-jobs.md) är ett sätt att utföra åtgärder på enheter. Varje enhetstvilling innehåller information om de jobb som den ingår i en samling som kallas **jobb**.

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

För närvarande kan den här samlingen ifrågasättas **som devices.jobs** i frågespråket IoT Hub.

> [!IMPORTANT]
> För närvarande returneras egenskapen jobb aldrig när du frågar enhetstvillingar. Det vill än frågor som innehåller "FRÅN-enheter". Egenskapen jobb kan endast nås direkt `FROM devices.jobs`med frågor med .
>
>

Om du till exempel vill hämta alla jobb (tidigare och schemalagda) som påverkar en enda enhet kan du använda följande fråga:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
```

Observera hur den här frågan ger enhetsspecifik status (och eventuellt direkt metodsvar) för varje returnerat jobb.

Det är också möjligt att filtrera med godtyckliga booleska villkor på alla objektegenskaper i **devices.jobs** samlingen.

Om du till exempel vill hämta alla slutförda enhetstvillinguppdateringsjobb som skapades efter september 2016 för en viss enhet använder du följande fråga:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Du kan också hämta resultaten per enhet för ett enskilt jobb.

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Begränsningar

För närvarande stöder frågor om **devices.jobs** inte:

* Prognoser är därför `SELECT *` endast möjliga.
* Villkor som refererar till enhetstvillingen utöver jobbegenskaper (se föregående avsnitt).
* Utföra aggregeringar, till exempel antal, genomsnitt, grupp för.

## <a name="basics-of-an-iot-hub-query"></a>Grunderna i en IoT Hub-fråga

Varje IoT Hub-fråga består av SELECT- och FROM-satser, med valfria WHERE- och GROUP BY-satser. Varje fråga körs på en samling JSON-dokument, till exempel enhetstvillingar. FRÅN-satsen anger den dokumentsamling som ska itereras på (**enheter,** **enheter.moduler**eller **devices.jobs**). Sedan tillämpas filtret i WHERE-satsen. Med aggregeringar grupperas resultatet av det här steget enligt vad som anges i SATS FÖR GRUPP. För varje grupp genereras en rad enligt vad som anges i SELECT-satsen.

```sql
SELECT <select_list>
  FROM <from_specification>
  [WHERE <filter_condition>]
  [GROUP BY <group_specification>]
```

## <a name="from-clause"></a>FROM-satsen

**FRÅN-<from_specification**>-satsen kan endast anta tre värden: **FRÅN-enheter** för att fråga **enhetstvillingar, FRÅN-enheter.modulens** till fråga modultvillingar eller **FRÅN devices.jobs** fråga jobb per enhetsinformation.

## <a name="where-clause"></a>WHERE-satsen

**WHERE <filter_condition>-satsen** är valfri. Den anger ett eller flera villkor som JSON-dokumenten i FROM-samlingen måste uppfylla för att inkluderas som en del av resultatet. Alla JSON-dokument måste utvärdera de angivna villkoren till "true" som ska ingå i resultatet.

De tillåtna villkoren [beskrivs](iot-hub-devguide-query-language.md#expressions-and-conditions)i avsnittet Uttryck och villkor .

## <a name="select-clause"></a>SELECT-satsen

**SELECT <select_list>** är obligatoriskt och anger vilka värden som hämtas från frågan. Den anger de JSON-värden som ska användas för att generera nya JSON-objekt.
För varje element i den filtrerade (och eventuellt grupperade) delmängden av FROM-samlingen genererar projektionsfasen ett nytt JSON-objekt. Det här objektet är konstruerat med de värden som anges i SELECT-satsen.

Följande är grammatiken i SELECT-satsen:

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

**Attribute_name** refererar till någon egenskap för JSON-dokumentet i FROM-samlingen. Några exempel på SELECT-satser finns i avsnittet Komma igång med enhetstvillingfrågor.

För närvarande stöds urvalssatser som skiljer **sig från SELECT*** endast i aggregerade frågor på enhetstvillingar.

## <a name="group-by-clause"></a>GRUPP FÖR-satser

**Group BY <group_specification>-satsen** är ett valfritt steg som körs efter filtret som anges i WHERE-satsen och före den projektion som anges i SELECT. Den grupperar dokument baserat på värdet av ett attribut. Dessa grupper används för att generera aggregerade värden enligt select-satsen.

Ett exempel på en fråga som använder GROUP BY är:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Den formella syntaxen för GROUP BY är:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** refererar till någon egenskap för JSON-dokumentet i FROM-samlingen.

För närvarande stöds GROUP BY-satsen endast när du frågar enhetstvillingar.

> [!IMPORTANT]
> Termen `group` behandlas för närvarande som ett speciellt nyckelord i frågor. Om du använder `group` som ditt egenskapsnamn, överväg att omge det med `SELECT * FROM devices WHERE tags.[[group]].name = 'some_value'`dubbla parenteser för att undvika fel, t.ex.
>

## <a name="expressions-and-conditions"></a>Uttryck och villkor

På en hög nivå, ett *uttryck:*

* Utvärderar till en förekomst av en JSON-typ (till exempel boolesk, tal, sträng, matris eller objekt).
* Definieras genom att manipulera data som kommer från enheten JSON dokument och konstanter med hjälp av inbyggda operatörer och funktioner.

*Villkor* är uttryck som utvärderas till en boolesk. Alla konstanter som skiljer sig från **booleskt sant** betraktas som **falska**. Den här regeln innehåller **null**, **odefinierad,** alla objekt- eller matrisförekomster, alla strängar och boolesk **false**.

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

Om du vill förstå vad varje symbol i uttrycksyntaxen står för läser du följande tabell:

| Symbol | Definition |
| --- | --- |
| attribute_name | Alla egenskaper för JSON-dokumentet i **FROM-samlingen.** |
| binary_operator | Alla binära operatorer som anges i avsnittet [Operatorer.](#operators) |
| function_name| Alla funktioner som anges i avsnittet [Funktioner.](#functions) |
| decimal_literal |En flottör uttryckt i decimalnotation. |
| hexadecimal_literal |Ett tal uttryckt av strängen "0x" följt av en sträng av hexadecimala siffror. |
| string_literal |Stränglitteraler är Unicode-strängar som representeras av en sekvens av noll eller fler Unicode-tecken eller escape-sekvenser. Stränglitteraler omges av enkla citattecken eller dubbla citattecken. Tillåtna rymningar: `\'`, `\"`, `\\` `\uXXXX` för Unicode-tecken som definieras av 4 hexadecimala siffror. |

### <a name="operators"></a>Operatorer

Följande operatorer stöds:

| Familj | Operatorer |
| --- | --- |
| Aritmetiska |+, -, *, /, % |
| Logiska |AND, OR, NOT (och, eller, inte) |
| Jämförelse |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Functions

När du frågar tvillingar och jobb är den enda funktion som stöds:

| Funktion | Beskrivning |
| -------- | ----------- |
| IS_DEFINED(egenskap) | Returnerar ett booleskt som anger om egenskapen har tilldelats ett värde (inklusive `null`). |

Under ruttförhållanden stöds följande matematiska funktioner:

| Funktion | Beskrivning |
| -------- | ----------- |
| ABS(x) | Returnerar det absoluta (positiva) värdet för det angivna numeriska uttrycket. |
| EXP(x) | Returnerar exponentiellt värde för det angivna numeriska uttrycket (e^x). |
| EFFEKT(x,y) | Returnerar värdet för det angivna uttrycket till den angivna effekten (x^y).|
| KVADRAT(x)    | Returnerar kvadraten för det angivna numeriska värdet. |
| TAK (x) | Returnerar det minsta heltalsvärdet som är större än eller lika med det angivna numeriska uttrycket. |
| GOLV (x) | Returnerar det största heltalsvärdet som är mindre än eller lika med det angivna numeriska uttrycket. |
| TECKEN(x) | Returnerar det positiva tecknet (+1), noll (0) eller negativt (-1) för det angivna numeriska uttrycket.|
| SQRT(x) | Returnerar kvadratroten för det angivna numeriska värdet. |

Under vägförhållanden stöds följande typkontroll- och gjutningsfunktioner:

| Funktion | Beskrivning |
| -------- | ----------- |
| AS_NUMBER | Konverterar indatasträngen till ett tal. `noop`om inmatningen är ett tal. `Undefined` om strängen inte representerar ett tal.|
| IS_ARRAY | Returnerar ett booleskt värde som anger om typen av det angivna uttrycket är en matris. |
| IS_BOOL | Returnerar ett booleskt värde som anger om typen av det angivna uttrycket är ett booleskt. |
| IS_DEFINED | Returnerar ett booleskt värde som anger huruvida egenskapen har tilldelats ett värde. Detta stöds endast när värdet är en primitiv typ. Primitiva typer inkluderar sträng, boolesk, numerisk eller `null`. DateTime stöds inte objekttyper och matriser. |
| IS_NULL | Returnerar ett booleskt värde som anger om typen av det angivna uttrycket är null. |
| IS_NUMBER | Returnerar ett booleskt värde som anger om typen av det angivna uttrycket är ett tal. |
| IS_OBJECT | Returnerar ett booleskt värde som anger om typen av det angivna uttrycket är ett JSON-objekt. |
| IS_PRIMITIVE | Returnerar ett booleskt värde som anger om typen av det angivna uttrycket `null`är en primitiv (sträng, boolesk, numerisk eller ). |
| IS_STRING | Returnerar ett booleskt värde som anger om typen av det angivna uttrycket är en sträng. |

Under flödesförhållanden stöds följande strängfunktioner:

| Funktion | Beskrivning |
| -------- | ----------- |
| CONCAT(x, y, ...) | Returnerar en sträng som är resultatet av en sammanfogning av två eller fler strängvärden. |
| LÄNGD(x) | Returnerar antalet tecken i det angivna stränguttrycket.|
| LÄGRE(x) | Returnerar ett stränguttryck efter att teckendata med versaler har konverterats till gemener. |
| ÖVRE(x) | Returnerar ett stränguttryck efter att teckendata med gemener har konverterats till versaler. |
| DELSTRÄNG(sträng, start [, längd]) | Returnerar en del av ett stränguttryck som börjar vid den angivna teckenfria positionen och fortsätter till den angivna längden eller till strängens. |
| INDEX_OF(sträng, fragment) | Returnerar startpositionen för den första förekomsten av det andra stränguttrycket i det första angivna stränguttrycket eller -1 om strängen inte hittas.|
| STARTS_WITH(x, y) | Returnerar ett booleskt som anger om det första stränguttrycket börjar med det andra. |
| ENDS_WITH(x, y) | Returnerar en boolesk som anger om det första stränguttrycket slutar med det andra. |
| INNEHÅLLER(x,y) | Returnerar ett booleskt värde som anger huruvida det första stränguttrycket innehåller det andra. |

## <a name="next-steps"></a>Nästa steg

Lär dig hur du kör frågor i dina appar med [Azure IoT SDK:er](iot-hub-devguide-sdks.md).
