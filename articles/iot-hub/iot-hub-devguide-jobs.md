---
title: Förstå Azure IoT Hub-jobb | Microsoft Docs
description: Utvecklarguide – schemaläggning av jobb som ska köras på flera enheter är anslutna till din IoT-hubb. Jobb kan uppdatera taggar och önskade egenskaper och anropa direktmetoder på flera enheter.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 147dd0f454bd85673bcba5cd6148c5da9716c580
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409038"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Schemalägga jobb på flera enheter

Azure IoT Hub möjliggör ett antal byggblock som [tvillingegenskaper och taggar](iot-hub-devguide-device-twins.md) och [direkta metoder](iot-hub-devguide-direct-methods.md). Backend-appar kan normalt enhetsadministratörer och ansvariga att uppdatera och interagera med IoT-enheter i grupp och enligt ett schema. Jobb köra uppdateringar för enhetstvilling och direkta metoder mot en uppsättning enheter enligt ett schema. Exempelvis kan använder en operatör en backend-app som startar och spårar ett jobb för att starta om en uppsättning enheter för att skapa 43 och våning 3 samtidigt som inte skulle vara söndrande för åtgärderna i byggnaden.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Överväg att använda jobb när du behöver att schemalägga och följa förloppet någon av följande aktiviteter på en uppsättning enheter:

* Uppdatera önskade egenskaper
* Uppdatera taggar
* Anropa direktmetoder

## <a name="job-lifecycle"></a>Livscykel för jobbet

Jobb initieras av lösningens backend-server och underhålls av IoT Hub. Du kan initiera ett jobb via en tjänst för webbservergrupper på URI (`PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30`) och status för ett jobb som körs via en tjänst för webbservergrupper på URI-fråga (`GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30`). Köra en jobbfråga för att uppdatera status för jobb som körs när ett jobb har initierats.

> [!NOTE]
> När du startar en egenskapsnamn och värden får bara innehålla US-ASCII utskrivbara alfanumeriska, med undantag för sådana i följande: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Jobb att köra direkta metoder

Följande kodavsnitt visar information för HTTPS 1.1-begäran för att köra en [direktmetod](iot-hub-devguide-direct-methods.md) på en uppsättning enheter med hjälp av ett jobb:

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

Frågevillkoret kan också vara på ett enda enhets-ID eller på en lista över enhets-ID som visas i följande exempel:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[IoT Hub Query Language](iot-hub-devguide-query-language.md) omfattar IoT Hub-frågespråk i mer detalj.

Följande utdrag visar begäran och svaret för ett jobb som schemalagts att anropa en direkt metod med namnet testMethod på alla enheter på contoso-hub-1:

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

## <a name="jobs-to-update-device-twin-properties"></a>Jobb för att uppdatera tvillingegenskaper

Följande kodavsnitt visar HTTPS 1.1 begäran information för att uppdatera tvillingegenskaper med hjälp av ett jobb:

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
> Den *updateTwin* egenskapen kräver en giltig etag-matchning, till exempel `etag="*"`.

Följande utdrag visar begäran och svaret för ett jobb som schemalagts för uppdatering tvillingegenskaper för test-enhet på contoso-hub-1:

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

## <a name="querying-for-progress-on-jobs"></a>Fråga efter status för jobb

Följande kodavsnitt visar information för HTTPS 1.1-begäran för frågor för jobb:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
```

ContinuationToken tillhandahålls av svaret.

Du kan fråga efter jobbstatus för körning på varje enhet med hjälp av den [IoT Hub-frågespråk för enhetstvillingar, jobb och meddelanderoutning](iot-hub-devguide-query-language.md).

## <a name="jobs-properties"></a>Egenskaper för jobb

I följande lista visar egenskaper och motsvarande beskrivningar som kan användas vid fråga för jobb eller jobbresultat.

| Egenskap  | Beskrivning |
| --- | --- |
| **jobId** |Programmet ange ID för jobbet. |
| **startTime** |Program tillhandahålls Starttid (ISO 8601-) för jobbet. |
| **endTime** |IoT Hub tillhandahåller datum (ISO 8601-) för när jobbet är slutfört. Gäller endast när jobbet når ”slutfört” tillstånd. |
| **type** |Typer av jobb: |
| | **scheduleUpdateTwin**: Ett jobb som används för att uppdatera en uppsättning önskade egenskaper eller taggar. |
| | **scheduleDeviceMethod**: Ett jobb som används för att anropa en enhetsmetod på en uppsättning enhetstvillingar. |
| **status** |Aktuell status för jobbet. Möjliga värden för status: |
| | **Väntande**: Schemalagda och väntar på att hämtas av jobbtjänsten. |
| | **Schemalagd**: Schemalagd för en tidpunkt i framtiden. |
| | **Kör**: Aktiva jobb. |
| | **Har avbrutits**: Jobbet har avbrutits. |
| | **Det gick inte**: Jobbet misslyckades. |
| | **Slutfört**: Jobbet har slutförts. |
| **deviceJobStatistics** |Statistik om den jobbkörningen. |
| | **deviceJobStatistics** egenskaper: |
| | **deviceJobStatistics.deviceCount**: Antalet enheter i jobbet. |
| | **deviceJobStatistics.failedCount**: Antalet enheter där jobbet misslyckades. |
| | **deviceJobStatistics.succeededCount**: Antalet enheter där jobbet har slutförts. |
| | **deviceJobStatistics.runningCount**: Antalet enheter som körs jobbet. |
| | **deviceJobStatistics.pendingCount**: Antalet enheter som väntar jobbet ska köras. |

### <a name="additional-reference-material"></a>Ytterligare referensmaterial

Andra referensavsnitten i IoT Hub developer guide inkluderar:

* [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md) beskriver de olika slutpunkter som varje IoT-hubb exponerar för körning och hanteringsåtgärder.

* [Begränsning och kvoter](iot-hub-devguide-quotas-throttling.md) beskriver kvoter som gäller för IoT Hub-tjänsten och beteendet som händer när du använder tjänsten.

* [Azure IoT-enheten och tjänsten SDK: er](iot-hub-devguide-sdks.md) visar en lista över olika språk SDK: er som du kan använda när du utvecklar appar för både enheten och tjänsten som interagerar med IoT Hub.

* [IoT Hub-frågespråk för enhetstvillingar, jobb och meddelanderoutning](iot-hub-devguide-query-language.md) beskriver IoT Hub-frågespråk. Använd det här frågespråket för att hämta information från IoT Hub om enhetstvillingar och jobb.

* [IoT Hub MQTT-support](iot-hub-mqtt-support.md) innehåller mer information om IoT Hub-stöd för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Se följande självstudie för IoT Hub för att prova några av de koncept som beskrivs i den här artikeln:

* [Schemalägg och Sänd jobb](iot-hub-node-node-schedule-jobs.md)