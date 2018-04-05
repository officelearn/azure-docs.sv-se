---
title: Förstå Azure IoT Hub jobb | Microsoft Docs
description: Utvecklarhandbok - schemaläggning av jobb ska köras på flera enheter anslutna till din IoT-hubb. Jobb kan uppdatera taggar och önskade egenskaper och anropa direkt metoder på flera enheter.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: fe78458f-4f14-4358-ac83-4f7bd14ee8da
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 367eb703465b2fbc6f1e06a383bc9df709cabe78
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="schedule-jobs-on-multiple-devices"></a>Schemalägga jobb på flera enheter

Azure IoT-hubb möjliggör ett antal byggblock som [dubbla enhetsegenskaper och taggar] [ lnk-twin-devguide] och [direkt metoder][lnk-dev-methods].  Backend-appar kan normalt enhetsadministratörer och ansvariga att uppdatera och interagera med IoT-enheter gruppvis och vid ett schemalagt klockslag.  Jobb köra dubbla uppdateringar och direkt metoder mot en uppsättning enheter vid ett schemalagt klockslag.  En operator skulle till exempel använda en backend-app som startar och spårar ett jobb för att starta om en uppsättning enheter i att skapa 43 och våning 3 samtidigt som inte är störande till åtgärder i byggnaden.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Överväg att använda jobb när du behöver schemalägga och följa förloppet någon av följande aktiviteter på en uppsättning enheter:

* Uppdatera önskade egenskaper
* Uppdatera taggar
* Anropa direkt metoder

## <a name="job-lifecycle"></a>Jobbet livscykel
Jobb initieras av lösningens serverdel och underhålls av IoT-hubb.  Du kan starta ett jobb via en tjänst-riktade URI (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14`) och status för ett jobb som körs via en tjänst-riktade URI-fråga (`{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`). Kör en jobbfråga om du vill uppdatera status för jobb som körs när ett jobb startas.

> [!NOTE]
> När du startar en egenskapsnamn och värden kan endast innehålla US-ASCII utskrivbara alfanumeriskt, förutom eventuella i följande: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`.

## <a name="jobs-to-execute-direct-methods"></a>Jobb som ska köra direkt metoder
Följande utdrag visar information för HTTPS 1.1-begäran för att köra en [direkt metod] [ lnk-dev-methods] på en uppsättning enheter med hjälp av ett jobb:

    PUT /jobs/v2/<jobId>?api-version=2016-11-14

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            responseTimeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }

Frågevillkoret kan också vara på en enda enhets-ID eller en lista över enhets-ID som visas i följande exempel:

```
queryCondition = "deviceId = 'MyDevice1'"
queryCondition = "deviceId IN ['MyDevice1','MyDevice2']"
queryCondition = "deviceId IN ['MyDevice1']
```
[IoT-hubb Query Language] [ lnk-query] täcker IoT-hubb frågespråket i mer detalj.

## <a name="jobs-to-update-device-twin-properties"></a>Jobb för att uppdatera dubbla enhetsegenskaper
Följande utdrag visar HTTPS 1.1 begäran information för att uppdatera enheten två egenskaper med hjälp av ett jobb:

    PUT /jobs/v2/<jobId>?api-version=2016-11-14
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }

## <a name="querying-for-progress-on-jobs"></a>Frågar efter status för jobb
Följande utdrag visar information för HTTPS 1.1-begäran för [frågar efter jobb][lnk-query]:

    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

ContinuationToken tillhandahålls av svaret.  

## <a name="jobs-properties"></a>Egenskaper för jobb
I följande lista visar egenskaper och motsvarande beskrivningar som kan användas när du frågar för jobb eller jobb resultat.

| Egenskap | Beskrivning |
| --- | --- |
| **jobId** |Programmet ange ID för jobbet. |
| **startTime** |Program som starttid (ISO 8601) för jobbet. |
| **endTime** |IoT-hubb som datum (ISO 8601) för när jobbet har slutförts. Gäller endast när jobbet når tillståndet 'slutförda'. |
| **Typ** |Typer av jobb: |
| | **scheduledUpdateTwin**: ett jobb som används för att uppdatera en uppsättning egenskaper eller taggar. |
| | **scheduledDeviceMethod**: ett jobb som används för att anropa en metod för enheten på en uppsättning twins för enheten. |
| **status** |Aktuell status för jobbet. Möjliga värden för status: |
| | **väntande**: schemaläggs och väntar på att hämtas av tjänsten jobb. |
| | **schemalagda**: schemalagd vid en tidpunkt i framtiden. |
| | **kör**: aktiva jobb. |
| | **avbruten**: jobbet har avbrutits. |
| | **Det gick inte**: jobbet misslyckades. |
| | **slutföra**: jobbet har slutförts. |
| **deviceJobStatistics** |Statistik om den jobbkörningen. |
| | **deviceJobStatistics** egenskaper: |
| | **deviceJobStatistics.deviceCount**: antal enheter i jobbet. |
| | **deviceJobStatistics.failedCount**: antalet enheter där jobbet misslyckades. |
| | **deviceJobStatistics.succeededCount**: antalet enheter där jobbet har slutförts. |
| | **deviceJobStatistics.runningCount**: antalet enheter som för närvarande körs jobbet. |
| | **deviceJobStatistics.pendingCount**: antalet enheter som väntar på att köra jobbet. |

### <a name="additional-reference-material"></a>Ytterligare referensmaterialet
Andra referensavsnitten i utvecklarhandboken för IoT-hubben är:

* [IoT-hubbslutpunkter] [ lnk-endpoints] beskriver de olika slutpunkter som varje IoT-hubb visar för körning och hanteringsåtgärder.
* [Begränsning och kvoter] [ lnk-quotas] beskriver kvoterna som gäller för IoT-hubb-tjänsten och bandbreddsbegränsning beteende som händer när du använder tjänsten.
* [Azure IoT-enheten och tjänsten SDK] [ lnk-sdks] Listar olika språk SDK: er som du kan använda när du utvecklar appar för både enheten och tjänsten som interagerar med IoT-hubben.
* [IoT-hubb frågespråk för enheten twins, jobb och meddelanderoutning] [ lnk-query] beskriver frågespråket i IoT-hubb. Använd den här frågespråket för att hämta information från IoT-hubb om enheten twins och jobb.
* [Stöd för IoT-hubb MQTT] [ lnk-devguide-mqtt] ger mer information om stöd för IoT-hubb för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg
Om du vill prova några av de begrepp som beskrivs i den här artikeln finns i följande IoT-hubb kursen:

* [Schemat och sändning jobb][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-node-node-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
