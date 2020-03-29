---
title: Förstå Azure IoT Hub-jobb | Microsoft-dokument
description: Utvecklarguide – schemalägga jobb för att köras på flera enheter som är anslutna till din IoT-hubb. Jobb kan uppdatera taggar och önskade egenskaper och anropa direkta metoder på flera enheter.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 147dd0f454bd85673bcba5cd6148c5da9716c580
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65409038"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Schemalägga jobb på flera enheter

Azure IoT Hub aktiverar ett antal byggstenar som [enhetstvillingegenskaper och taggar](iot-hub-devguide-device-twins.md) och [direkta metoder](iot-hub-devguide-direct-methods.md). Vanligtvis gör backend-appar det möjligt för enhetsadministratörer och operatörer att uppdatera och interagera med IoT-enheter i grupp och vid en schemalagd tidpunkt. Jobb kör enhetstvillinguppdateringar och direkta metoder mot en uppsättning enheter vid en schemalagd tidpunkt. En operatör använder till exempel en backend-app som initierar och spårar ett jobb för att starta om en uppsättning enheter i byggnad 43 och våning 3 i taget som inte skulle störa byggnadens verksamhet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Överväg att använda jobb när du behöver schemalägga och spåra förloppet någon av följande aktiviteter på en uppsättning enheter:

* Uppdatera önskade egenskaper
* Uppdatera taggar
* Anropa direkta metoder

## <a name="job-lifecycle"></a>Livscykel för jobb

Jobb initieras av lösningens backend och underhålls av IoT Hub. Du kan initiera ett jobb via en`PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30`tjänstinriktad URI ( ) och fråga efter förlopp på ett körjobb via en tjänstinriktad URI (`GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30`). Om du vill uppdatera statusen för att köra jobb när ett jobb har initierats kör du en jobbfråga.

> [!NOTE]
> När du initierar ett jobb kan egenskapsnamn och värden bara innehålla alfanumeriskt utskrivbart för USA-ASCII, förutom i följande uppsättning:`$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Jobb för att köra direkta metoder

Följande kodavsnitt visar information om HTTPS 1.1-begäran om körning av en [direkt metod](iot-hub-devguide-direct-methods.md) på en uppsättning enheter som använder ett jobb:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "<methodName>",
        "payload": <payload>,
        "responseTimeoutInSeconds": methodTimeoutInSeconds
    },
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

Frågevillkoret kan också finnas på ett enda enhets-ID eller i en lista över enhets-ID:er som visas i följande exempel:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[IoT Hub Query Language](iot-hub-devguide-query-language.md) täcker IoT Hub-frågespråket i detalj.

Följande kodavsnitt visar begäran och svaret för ett jobb som schemalagts att anropa en direkt metod med namnet testMethod på alla enheter på contoso-hub-1:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job01?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=68iv------------------------------------v8Hxalg%3D&se=1556849884&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 317

{
    "jobId": "job01",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "testMethod",
        "payload": {},
        "responseTimeoutInSeconds": 30
    },
    "queryCondition": "*", 
    "startTime": "2019-05-04T15:53:00.077Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 65
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 01:46:18 GMT

{"jobId":"job01","type":"scheduleDeviceMethod","status":"queued"}
```

## <a name="jobs-to-update-device-twin-properties"></a>Jobb för att uppdatera enhetstvillingegenskaper

Följande kodavsnitt visar information om https 1.1-begäran om uppdatering av enhetstvillingegenskaper med hjälp av ett jobb:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleUpdateTwin",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

> [!NOTE]
> Egenskapen *updateTwin* kräver en giltig etag-matchning. till exempel `etag="*"`.

Följande kodavsnitt visar begäran och svaret för ett jobb som schemalagts för att uppdatera enhetstvillingegenskaper för testenhet på contoso-hub-1:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job02?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=BN0U-------------------------------------RuA%3D&se=1556925787&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 339

{
    "jobId": "job02",
    "type": "scheduleUpdateTwin",
    "updateTwin": {
      "properties": {
        "desired": {
          "test1": "value1"
        }
      },
     "etag": "*"
     },
    "queryCondition": "deviceId = 'test-device'",
    "startTime": "2019-05-08T12:19:56.868Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 63
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 22:45:13 GMT

{"jobId":"job02","type":"scheduleUpdateTwin","status":"queued"}
```

## <a name="querying-for-progress-on-jobs"></a>Fråga efter förlopp för jobb

Följande kodavsnitt visar information om https 1.1-begäran om frågor för jobb:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
```

Fortsättningen Är från svaret.

Du kan fråga efter jobbkörningsstatus på varje enhet med [frågespråket IoT Hub för enhetstvillingar, jobb och meddelanderoutning](iot-hub-devguide-query-language.md).

## <a name="jobs-properties"></a>Egenskaper för jobb

I följande lista visas egenskaper och motsvarande beskrivningar, som kan användas vid frågor om jobb eller jobbresultat.

| Egenskap | Beskrivning |
| --- | --- |
| **jobId (jobbId)** |Ansökan som tillhandahålls ID för jobbet. |
| **startTime** |Ansökan som starttid (ISO-8601) för jobbet. |
| **endTime** |IoT Hub anges datum (ISO-8601) för när jobbet slutförts. Gäller först när jobbet har slutförts. |
| **Typ** |Typer av jobb: |
| | **scheduleUpdateTwin**: Ett jobb som används för att uppdatera en uppsättning önskade egenskaper eller taggar. |
| | **scheduleDeviceMethod**: Ett jobb som används för att anropa en enhetsmetod på en uppsättning enhetstvillingar. |
| **status** |Aktuellt tillstånd för jobbet. Möjliga värden för status: |
| | **väntar**: Schemalagd och väntar på att hämtas av jobbtjänsten. |
| | **schemalagd:** Schemalagd för en tid i framtiden. |
| | **köra**: För närvarande aktivt jobb. |
| | **har avbrutits:** Jobbet har avbrutits. |
| | **misslyckades**: Jobbet misslyckades. |
| | **:** Jobbet har slutförts. |
| **deviceJobStatistics** |Statistik om jobbets utförande. |
| | **deviceJobStatistics** egenskaper: |
| | **deviceJobStatistics.deviceCount**: Antal enheter i jobbet. |
| | **deviceJobStatistics.failedCount**: Antal enheter där jobbet misslyckades. |
| | **deviceJobStatistics.succeededCount**: Antal enheter där jobbet lyckades. |
| | **deviceJobStatistics.runningCount**: Antal enheter som för närvarande kör jobbet. |
| | **deviceJobStatistics.pendingCount**: Antal enheter som väntar på att köra jobbet. |

### <a name="additional-reference-material"></a>Ytterligare referensmaterial

Andra referensavsnitt i utvecklarhandboken för IoT Hub är:

* [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md) beskriver de olika slutpunkter som varje IoT-hubb exponerar för körnings- och hanteringsåtgärder.

* [Begränsning och kvoter](iot-hub-devguide-quotas-throttling.md) beskriver de kvoter som gäller för IoT Hub-tjänsten och begränsningsbeteendet som du kan förvänta dig när du använder tjänsten.

* [Azure IoT-enhet och tjänst SDK visar](iot-hub-devguide-sdks.md) de olika språk SDK:er som du kan använda när du utvecklar både enhets- och tjänstappar som interagerar med IoT Hub.

* [IoT Hub-frågespråk för enhetstvillingar, jobb och meddelanderoutning](iot-hub-devguide-query-language.md) beskriver frågespråket IoT Hub. Använd det här frågespråket för att hämta information från IoT Hub om dina enhetstvillingar och jobb.

* [IoT Hub MQTT-stöd](iot-hub-mqtt-support.md) ger mer information om IoT Hub-stöd för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Information om hur du provar några av de begrepp som beskrivs i den här artikeln finns i följande IoT Hub-självstudiekurs:

* [Schemalägga och sända jobb](iot-hub-node-node-schedule-jobs.md)