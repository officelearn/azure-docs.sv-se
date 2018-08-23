---
title: Förstå Azure IoT Hub-frågespråk | Microsoft Docs
description: Utvecklarguide – beskrivning av IoT-hubben som SQL-liknande frågespråk som används för att hämta information om enheten/modultvillingar och jobb från IoT hub.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: elioda
ms.openlocfilehash: 7704e08246798108aa251c19a4ab0c3baaaad570
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054178"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>IoT Hub-frågespråk för tvillingar för enheten och modulen, jobb och meddelanderoutning

IoT Hub tillhandahåller ett kraftfullt SQL-liknande språk för att hämta information om [enhetstvillingar] [ lnk-twins] och [jobb][lnk-jobs], och [meddelanderoutning][lnk-devguide-messaging-routes]. Den här artikeln beskriver vi:

* En introduktion till de viktigaste funktionerna i frågespråket IoT Hub och
* Detaljerad beskrivning av språket.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Enheten och modulen twin frågor
[Enhetstvillingar] [ lnk-twins] och modultvillingar kan innehålla godtycklig JSON-objekt som både taggar och egenskaper. IoT Hub kan du fråga enhetstvillingar och modultvillingar som ett enda JSON-dokument som innehåller alla twin information.
Anta exempelvis att din IoT hub-enhetstvillingar har följande struktur (modultvilling skulle vara liknande bara med en ytterligare moduleId):

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

### <a name="device-twin-queries"></a>Enhetstvillingfrågor

IoT-hubb exponerar enhetstvillingar som en dokumentsamling som heter **enheter**.
Följande fråga hämtar så hela uppsättningen av enhetstvillingar:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Azure IoT SDK: er] [ lnk-hub-sdks] stöder sidindelning av stora resultat.

IoT Hub kan du hämta enhetstvillingar filtrering med godtyckliga villkor. Till exempel att ta emot device twins var den **location.region** tagg har angetts till **USA** använder du följande fråga:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Booleska operatorer och aritmetiskt jämförelser stöds också. Till exempel använda twins finns i USA och konfigurerat att skicka telemetri om mindre än varje minut för att hämta enheten följande fråga:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Underlätta för dig, det är också möjligt att använda konstanterna i matrisen med de **IN** och **ni** (inte i) operatörer. Till exempel använda enhetstvillingar som rapporterar Wi-Fi eller trådbunden anslutning för att hämta följande fråga:

```sql
SELECT * FROM devices
WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Du behöver ofta identifiera alla enhetstvillingar som innehåller en viss egenskap. Funktionen stöds av IoT Hub `is_defined()` för detta ändamål. Exempelvis kan du hämta enhetstvillingar som definierar den `connectivity` egenskapen använder du följande fråga:

```SQL
SELECT * FROM devices
WHERE is_defined(properties.reported.connectivity)
```

Referera till den [WHERE-satsen] [ lnk-query-where] avsnittet för fullständig referens av vilka filtreringsfunktioner.

Gruppering och aggregeringar stöds också. Om du vill hitta antal enheter i varje telemetri använder Konfigurationsstatus exempelvis följande fråga:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Den här grupperingen frågan returnerar ett resultat liknar följande exempel:

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

I det här exemplet tre enheter rapporteras framgångsrik konfigurering, två fortfarande tillämpar konfigurationen och en rapporterade ett fel.

Projektionsfrågor kan utvecklare returnerar bara de egenskaper som de som intresserar dig. Till exempel frånkopplade om du vill hämta den senaste aktivitetstiden för alla enheter, Använd följande fråga:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Modulen twin frågor

Frågekörning på modultvillingar påminner om att fråga på enhetstvillingar, men använder en annan samling/namnrymd, dvs. i stället för ”från enheter” som du kan fråga

```sql
SELECT * FROM devices.modules
```

Tillåter vi inte koppling mellan enheter och devices.modules samlingar. Om du vill fråga modultvillingar mellan enheter gör det baserat på taggar. Den här frågan returnerar alla modultvillingar på alla enheter med statusen genomsökning:

```sql
Select * from devices.modules where properties.reported.status = 'scanning'
```

Den här frågan returnerar alla modultvillingar med statusen genomsökning, men bara om den angivna Undergrupp enheter.

```sql
Select * from devices.modules where properties.reported.status = 'scanning' and deviceId IN ('device1', 'device2')  
```

### <a name="c-example"></a>C#-exempel
Frågefunktioner som exponeras av den [C#-tjänst-SDK] [ lnk-hub-sdks] i den **RegistryManager** klass.
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

Den **fråga** objektet instantieras med en storlek (upp till 100). Och sedan flera sidor som hämtas genom att anropa den **GetNextAsTwinAsync** metoder flera gånger.

Frågeobjektet visar flera **nästa** värden, beroende på vilket alternativ för deserialisering som krävs av frågan. Till exempel enhetstvilling eller jobb enhetsobjekt eller vanlig JSON när du använder projektioner.

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

Den **fråga** objektet instantieras med en storlek (upp till 100). Och sedan flera sidor som hämtas genom att anropa den **nextAsTwin** metoden flera gånger.

Frågeobjektet visar flera **nästa** värden, beroende på vilket alternativ för deserialisering som krävs av frågan. Till exempel enhetstvilling eller jobb enhetsobjekt eller vanlig JSON när du använder projektioner.

### <a name="limitations"></a>Begränsningar

> [!IMPORTANT]
> Frågeresultat kan ha ett par minuters fördröjning med avseende på de senaste värdena i enhetstvillingar. Om frågar enskilda enhetstvillingar efter ID, använder du hämta device twin API. Detta API innehåller de senaste värdena alltid och har högre nätverksbegränsningar.

Jämförelser är för närvarande endast mellan primitiva typer (inga objekt), till exempel `... WHERE properties.desired.config = properties.reported.config` stöds endast om dessa egenskaper har primitiva värden.

## <a name="get-started-with-jobs-queries"></a>Kom igång med frågor om jobb

[Jobb] [ lnk-jobs] ger dig ett sätt att utföra åtgärder på Enhetsuppsättningar. Varje enhetstvillingen innehåller information om de jobb som den är en del i en samling som heter **jobb**.
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

Den här samlingen är för närvarande frågningsbar som **devices.jobs** i IoT Hub-frågespråk.

> [!IMPORTANT]
> För närvarande returneras egenskapen jobb aldrig vid sökning efter enhetstvillingar. Det vill säga de frågor som innehåller ”från enheter”. Egenskapen jobb som bara kan användas direkt med frågor med `FROM devices.jobs`.
>
>

Du kan exempelvis använda följande fråga för att hämta alla jobb (senaste och schemalagda) som påverkar en enskild enhet:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
```

Observera hur den här frågan innehåller specifika status (och svaret direkt metod) för varje jobb som returneras.
Det är också möjligt att filtrera med valfritt booleskt villkor för alla objektegenskaper i den **devices.jobs** samling.
Till exempel om du vill hämta alla slutförda device twin uppdateringsjobb som har skapats efter September 2016 för en specifik enhet, använder du följande fråga:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Du kan också hämta resultat per enhet för ett enskilt jobb.

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Begränsningar
För närvarande frågar på **devices.jobs** har inte stöd för:

* Projektioner, därför kan bara `SELECT *` är möjligt.
* Villkor som refererar till enhetstvillingen förutom jobbegenskaper (se föregående avsnitt).
* Utföra aggregeringar, till exempel antal, avg, gruppera efter.

## <a name="device-to-cloud-message-routes-query-expressions"></a>Enhet-till-moln meddelandevägar fråga uttryck

Med hjälp av [enhet till moln vägar][lnk-devguide-messaging-routes], du kan konfigurera IoT Hub för att skicka meddelanden från enheten till molnet till olika slutpunkter. Skicka är baserad på uttryck som utvärderas mot enskilda meddelanden.

Vägen [villkor] [ lnk-query-expressions] använder IoT Hub-frågesyntax för språk som villkor i frågor mot enhetstvillingar och jobb, men endast en delmängd av funktionerna är tillgängliga. Väg villkoren utvärderas på meddelanderubriker och brödtext. Din routning frågeuttryck kan omfatta endast meddelandehuvudena, endast brödtext, eller båda meddelande rubriker och meddelandetext. IoT Hub förutsätter att ett visst schema för rubriker och brödtext för att dirigera meddelanden och i följande avsnitt beskrivs vad som krävs för IoT Hub för att dirigera korrekt.

### <a name="routing-on-message-headers"></a>Routning på meddelandehuvudena

IoT Hub förutsätter meddelandehuvudena för meddelanderoutning följande JSON-återgivning:

```json
{
  "message": {
    "systemProperties": {
      "contentType": "application/json",
      "contentEncoding": "utf-8",
      "iothub-message-source": "deviceMessages",
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z"
    },
    "appProperties": {
      "processingPath": "<optional>",
      "verbose": "<optional>",
      "severity": "<optional>",
      "testDevice": "<optional>"
    },
    "body": "{\"Weather\":{\"Temperature\":50}}"
  }
}
```

Meddelandet Systemegenskaper föregås den `'$'` symbolen.
Användaregenskaper nås alltid med deras namn. Om ett användarnamn för egenskapen är i linje med en systemegenskap (till exempel `$contentType`), Användaregenskapen hämtas med den `$contentType` uttryck.
Du kan alltid komma åt Systemegenskapen med hakparenteser `{}`: du kan exempelvis använda uttrycket `{$contentType}` att komma åt Systemegenskapen `contentType`. Inom hakparenteser egenskapsnamn hämta alltid motsvarande Systemegenskapen.

Kom ihåg att egenskapsnamn är skiftlägeskänsliga.

> [!NOTE]
> Alla meddelandeegenskaper är strängar. Systemegenskaper, enligt beskrivningen i den [utvecklarguide][lnk-devguide-messaging-format], är för närvarande inte tillgängliga för användning i frågor.
>

Exempel: Om du använder en `messageType` egenskapen kanske du vill dirigera all telemetri till en slutpunkt och alla aviseringar till en annan slutpunkt. Du kan skriva följande uttryck för att dirigera telemetri:

```sql
messageType = 'telemetry'
```

Och att dirigera aviseringarna som följande uttryck:

```sql
messageType = 'alert'
```

Booleskt uttryck och funktioner stöds också. Den här funktionen kan du skilja mellan allvarlighetsgrad, till exempel:

```sql
messageType = 'alerts' AND as_number(severity) <= 2
```

Referera till den [uttryck och villkor] [ lnk-query-expressions] avsnittet för en fullständig lista över stöds operatorer och funktioner.

### <a name="routing-on-message-bodies"></a>Routning på meddelandetext

IoT Hub kan endast dirigerar baserat på meddelandetexten innehållet om meddelandets brödtext är korrekt formaterad JSON-kodad i UTF-8, UTF-16- eller UTF-32. Ange innehållstyp för meddelandet att `application/json`. Ange det innehåll som kodning till en av stöds UTF-kodningar i meddelandehuvudena. Om något av rubrikerna som har angetts försöker IoT Hub inte att utvärdera eventuella frågeuttryck som involverar texten mot meddelandet. Om meddelandet inte är ett JSON-meddelande, eller om meddelandet inte anger den innehållstyp och Innehållskodning, kan du fortfarande använda meddelanderoutning för att dirigera meddelandet utifrån meddelandehuvudena.

I följande exempel visas hur du skapar ett meddelande med en korrekt formaterad och kodade JSON-texten:

```csharp
string messageBody = @"{ 
                            ""Weather"":{ 
                                ""Temperature"":50, 
                                ""Time"":""2017-03-09T00:00:00.000Z"", 
                                ""PrevTemperatures"":[ 
                                    20, 
                                    30, 
                                    40 
                                ], 
                                ""IsEnabled"":true, 
                                ""Location"":{ 
                                    ""Street"":""One Microsoft Way"", 
                                    ""City"":""Redmond"", 
                                    ""State"":""WA"" 
                                }, 
                                ""HistoricalData"":[ 
                                    { 
                                    ""Month"":""Feb"", 
                                    ""Temperature"":40 
                                    }, 
                                    { 
                                    ""Month"":""Jan"", 
                                    ""Temperature"":30 
                                    } 
                                ] 
                            } 
                        }"; 
 
// Encode message body using UTF-8 
byte[] messageBytes = Encoding.UTF8.GetBytes(messageBody); 
 
using (var message = new Message(messageBytes)) 
{ 
    // Set message body type and content encoding. 
    message.ContentEncoding = "utf-8"; 
    message.ContentType = "application/json"; 
 
    // Add other custom application properties.  
    message.Properties["Status"] = "Active";    
 
    await deviceClient.SendEventAsync(message); 
}
```

Du kan använda `$body` i frågeuttrycket att vidarebefordra meddelandet. Du kan använda en enkel brödtext-referens, brödtext matrisreferens eller flera brödtext referenser i frågeuttrycket. Din frågeuttryck kan också kombinera en brödtext-referens med en referens för rubriken. Följande är alla giltiga frågeuttryck:

```sql
$body.Weather.HistoricalData[0].Month = 'Feb'
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled
length($body.Weather.Location.State) = 2
$body.Weather.Temperature = 50 AND Status = 'Active'
```

## <a name="basics-of-an-iot-hub-query"></a>Grunderna i en IoT Hub-fråga
Varje IoT Hub-fråga består av väljer och från satser med valfritt var och en GROUP BY-satser. Varje fråga som körs på en samling av JSON-dokument, till exempel enhetstvillingar. FROM-satsen anger dokumentsamling itereras på (**enheter** eller **devices.jobs**). Sedan tillämpas filtret i WHERE-satsen. Resultatet av det här steget är grupperade med aggregeringar, som angetts i GROUP BY-satsen. En rad skapas för varje grupp som angetts i SELECT-satsen.

```sql
SELECT <select_list>
FROM <from_specification>
[WHERE <filter_condition>]
[GROUP BY <group_specification>]
```

## <a name="from-clause"></a>FROM-satsen
Den **från < from_specification >** satsen kan anta att bara två värden: **från enheter** att fråga enhetstvillingar, eller **från devices.jobs** till Frågedetaljer jobb per enhet.

## <a name="where-clause"></a>WHERE-satsen
Den **där < filter_condition >** -satsen är valfritt. Anger ett eller flera villkor att JSON-dokument i samlingen från måste uppfylla för att vara med i resultatet. Valfritt JSON-dokument måste utvärderas de angivna villkoren ”true” som ska ingå i resultatet.

Tillåtna villkor beskrivs i avsnittet [uttryck och villkor][lnk-query-expressions].

## <a name="select-clause"></a>SELECT-satsen
Den **väljer < select_list >** är obligatoriskt och anger vilka värden hämtas från frågan. Den anger JSON-värden som används för att generera nya JSON-objekt.
För varje element i den filtrerade (och eventuellt grupperade) delmängden av samlingen från genererar Projektionsfasen ett nytt JSON-objekt. Det här objektet har konstruerats med värdena som anges i SELECT-satsen.

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

**Attribute_name** refererar till en egenskap av JSON-dokument i samlingen från. Några exempel på Välj satser finns i den [komma igång med enhetstvillingfrågor] [ lnk-query-getstarted] avsnittet.

För närvarande val av satser skiljer sig från **Välj*** stöds endast i mängdfrågor på enhetstvillingar.

## <a name="group-by-clause"></a>GROUP BY-sats
Den **GROUP BY < group_specification >** -satsen är ett valfritt steg som körs efter det filter som angetts i WHERE-satsen och innan projektionen som anges i listan Välj. Den grupperar dokument baserat på värdet för ett attribut. Dessa grupper används för att generera sammanställda värden som anges i SELECT-satsen.

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

**Attribute_name** refererar till en egenskap av JSON-dokument i samlingen från.

GROUP BY-satsen stöds för närvarande bara när du frågar efter enhetstvillingar.

## <a name="expressions-and-conditions"></a>Uttryck och villkor
På hög nivå, en *uttryck*:

* Returnerar en instans av en JSON-typ (till exempel boolesk, tal, sträng, matrisen eller objekt).
* Definieras av manipulera data från enheten JSON-dokument och konstanter som med hjälp av inbyggda operatorer och funktioner.

*Villkor* är uttryck som utvärderas till ett booleskt värde. En konstant som skiljer sig från boolesk **SANT** anses **FALSKT**. Den här regeln innehåller **null**, **odefinierat**, valfri instans av objektet eller matrisen, valfri sträng och Boolean **FALSKT**.

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

För att förstå vad varje symbol i uttryck syntax står för, finns i följande tabell:

| Symbol | Definition |
| --- | --- |
| attribute_name | Alla egenskaper i JSON-dokumentet i den **FROM** samling. |
| binary_operator | Alla binära operatorn som anges i den [operatörer](#operators) avsnittet. |
| function_name| En funktion som anges i den [Functions](#functions) avsnittet. |
| decimal_literal |Ett flyttal uttrycks i decimalform. |
| hexadecimal_literal |Ett tal uttrycks av strängen ”0 x” följt av en sträng med hexadecimala siffror. |
| string_literal |Stränglitteraler är Unicode-strängar som representeras av en sekvens med noll eller flera Unicode-tecken eller escape-sekvenser. Stränglitteraler omges av enkla citattecken eller dubbla citattecken. Tillåtna visar: `\'`, `\"`, `\\`, `\uXXXX` för Unicode-tecken som definieras av 4 hexadecimala siffror. |

### <a name="operators"></a>Operatorer
Följande operatorer som stöds:

| Familj | Operatorer |
| --- | --- |
| Aritmetiska |+, -, *, /, % |
| Logiska |OCH, ELLER INTE |
| Jämförelse |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Functions
När du frågar efter tvillingar och jobb som det går endast att är funktionen:

| Funktion | Beskrivning |
| -------- | ----------- |
| IS_DEFINED(Property) | Returnerar ett booleskt värde som anger om egenskapen har tilldelats ett värde (inklusive `null`). |

Följande matematiska funktioner stöds i vägar villkor:

| Funktion | Beskrivning |
| -------- | ----------- |
| ABS(x) | Returnerar det absoluta (positiva) värdet för det angivna numeriskt uttrycket. |
| EXP(x) | Returnerar exponentiell värdet för det angivna numeriska uttrycket (e ^ x). |
| Power(x,y) | Returnerar värdet för det angivna uttrycket till angiven potens (x ^ y).|
| Square(x) | Returnerar för det angivna numeriska värdet. |
| CEILING(x) | Returnerar det minsta heltalsvärdet större än eller lika med det angivna numeriska uttrycket. |
| FLOOR(x) | Returnerar det största heltalet mindre än eller lika med det angivna numeriska uttrycket. |
| Sign(x) | Returnerar positiv (+ 1), noll (0) eller minustecken (-1) i det angivna numeriska uttrycket.|
| Rot(x) | Returnerar kvadratroten för det angivna numeriska värdet. |

I vägar villkor stöds följande typkontroll och omvandling funktioner:

| Funktion | Beskrivning |
| -------- | ----------- |
| AS_NUMBER | Konverterar den inmatade strängen till ett tal. `noop` Om indata är ett tal. `Undefined` om strängen inte representerar ett tal.|
| IS_ARRAY | Returnerar ett booleskt värde som anger om vilken typ av det angivna uttrycket är en matris. |
| IS_BOOL | Returnerar ett booleskt värde som anger om det angivna uttrycket är ett booleskt värde. |
| IS_DEFINED | Returnerar ett booleskt värde som anger om egenskapen har tilldelats ett värde. |
| IS_NULL | Returnerar ett booleskt värde som anger om vilken typ av det angivna uttrycket är null. |
| IS_NUMBER | Returnerar ett booleskt värde som anger om det angivna uttrycket är ett tal. |
| IS_OBJECT | Returnerar ett booleskt värde som anger om det angivna uttrycket är ett JSON-objekt. |
| IS_PRIMITIVE | Returnerar ett booleskt värde som anger om det angivna uttrycket är en primitiv hämtas (string, Boolean, numeriska och eller `null`). |
| IS_STRING | Returnerar ett booleskt värde som anger om det angivna uttrycket är en sträng. |

Följande sträng-funktioner stöds i vägar villkor:

| Funktion | Beskrivning |
| -------- | ----------- |
| SAMMANFOGA (x, y,...) | Returnerar en sträng som är resultatet av att sammanfoga två eller flera strängvärden. |
| LENGTH(x) | Returnerar antalet tecken i angivet stränguttryck.|
| LOWER(x) | Returnerar ett stränguttryck när versal data har konverterats till gemener. |
| UPPER(x) | Returnerar ett stränguttryck efter konvertera gemen data till versaler. |
| DELSTRÄNGEN (sträng, start [, längd]) | Returnerar en del av ett stränguttryck med början vid den angivna nollbaserade teckenpositionen och fortsätter med den angivna längden eller i slutet av strängen. |
| INDEX_OF (string, fragment) | Returnerar startpositionen för den första förekomsten av andra stränguttryck i första angivet stränguttryck eller -1 om strängen inte hittas.|
| STARTS_WITH (x, y) | Returnerar ett booleskt värde som anger om först stränguttryck börjar med andra. |
| ENDS_WITH (x, y) | Returnerar ett booleskt värde som anger om först stränguttryck slutar med andra. |
| CONTAINS(x,y) | Returnerar ett booleskt värde som anger om först stränguttryck innehåller andra. |

## <a name="next-steps"></a>Nästa steg
Lär dig att köra frågor i dina appar med hjälp av [Azure IoT SDK: er][lnk-hub-sdks].

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
