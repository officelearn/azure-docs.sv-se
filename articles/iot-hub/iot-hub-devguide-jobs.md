---
title: Förstå Azure IoT Hub-jobb | Microsoft Docs
description: Utvecklarguide – schemaläggning av jobb som ska köras på flera enheter är anslutna till din IoT-hubb. Jobb kan uppdatera taggar och önskade egenskaper och anropa direktmetoder på flera enheter.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: cb6afd04dacf3ae5c3d88293e2b96e180e69c33d
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585466"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Schemalägga jobb på flera enheter

Azure IoT Hub möjliggör ett antal byggblock som [tvillingegenskaper och taggar](iot-hub-devguide-device-twins.md) och [direkta metoder](iot-hub-devguide-direct-methods.md). Backend-appar kan normalt enhetsadministratörer och ansvariga att uppdatera och interagera med IoT-enheter i grupp och enligt ett schema. Jobb köra uppdateringar för enhetstvilling och direkta metoder mot en uppsättning enheter enligt ett schema. Exempelvis kan använder en operatör en backend-app som startar och spårar ett jobb för att starta om en uppsättning enheter för att skapa 43 och våning 3 samtidigt som inte skulle vara söndrande för åtgärderna i byggnaden.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Överväg att använda jobb när du behöver att schemalägga och följa förloppet någon av följande aktiviteter på en uppsättning enheter:

* Uppdatera önskade egenskaper
* Uppdatera taggar
* Anropa direktmetoder

## <a name="job-lifecycle"></a>Livscykel för jobbet

Jobb initieras av lösningens backend-server och underhålls av IoT Hub. Du kan initiera ett jobb via en tjänst för webbservergrupper på URI (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14`) och status för ett jobb som körs via en tjänst för webbservergrupper på URI-fråga (`{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`). Köra en jobbfråga för att uppdatera status för jobb som körs när ett jobb har initierats.

> [!NOTE]
> När du startar en egenskapsnamn och värden får bara innehålla US-ASCII utskrivbara alfanumeriska, med undantag för sådana i följande: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`
> 

## <a name="jobs-to-execute-direct-methods"></a>Jobb att köra direkta metoder

Följande kodavsnitt visar information för HTTPS 1.1-begäran för att köra en [direktmetod](iot-hub-devguide-direct-methods.md) på en uppsättning enheter med hjälp av ett jobb:

    ```
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
    ```

Frågevillkoret kan också vara på ett enda enhets-ID eller på en lista över enhets-ID som visas i följande exempel:

```
queryCondition = "deviceId = 'MyDevice1'"
queryCondition = "deviceId IN ['MyDevice1','MyDevice2']"
queryCondition = "deviceId IN ['MyDevice1']
```
[IoT Hub Query Language](iot-hub-devguide-query-language.md) omfattar IoT Hub-frågespråk i mer detalj.

## <a name="jobs-to-update-device-twin-properties"></a>Jobb för att uppdatera tvillingegenskaper

Följande kodavsnitt visar HTTPS 1.1 begäran information för att uppdatera tvillingegenskaper med hjälp av ett jobb:

    ```
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
    ```

## <a name="querying-for-progress-on-jobs"></a>Fråga efter status för jobb

Följande kodavsnitt visar information för HTTPS 1.1-begäran för frågor för jobb:

    ```
    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>
    ```
    
ContinuationToken tillhandahålls av svaret.

Du kan fråga efter jobbstatus för körning på varje enhet med hjälp av den [IoT Hub-frågespråk för enhetstvillingar, jobb och meddelanderoutning](iot-hub-devguide-query-language.md).

## <a name="jobs-properties"></a>Egenskaper för jobb

I följande lista visar egenskaper och motsvarande beskrivningar som kan användas vid fråga för jobb eller jobbresultat.

| Egenskap  | Beskrivning |
| --- | --- |
| **jobb-ID** |Programmet ange ID för jobbet. |
| **startTime** |Program tillhandahålls Starttid (ISO 8601-) för jobbet. |
| **endTime** |IoT Hub tillhandahåller datum (ISO 8601-) för när jobbet är slutfört. Gäller endast när jobbet når ”slutfört” tillstånd. |
| **typ** |Typer av jobb: |
| | **scheduledUpdateTwin**: ett jobb som används för att uppdatera en uppsättning önskade egenskaper eller taggar. |
| | **scheduledDeviceMethod**: ett jobb som används för att anropa en enhetsmetod på en uppsättning enhetstvillingar. |
| **Status** |Aktuell status för jobbet. Möjliga värden för status: |
| | **väntande**: schemalagd och väntar på att hämtas av jobbtjänsten. |
| | **schemalagda**: schemalagda för en tidpunkt i framtiden. |
| | **kör**: aktiva jobb. |
| | **har avbrutits**: jobbet har avbrutits. |
| | **Det gick inte**: Det gick inte att. |
| | **slutfört**: jobbet har slutförts. |
| **deviceJobStatistics** |Statistik om den jobbkörningen. |
| | **deviceJobStatistics** egenskaper: |
| | **deviceJobStatistics.deviceCount**: antal enheter i jobbet. |
| | **deviceJobStatistics.failedCount**: antalet enheter där jobbet misslyckades. |
| | **deviceJobStatistics.succeededCount**: antalet enheter där jobbet har slutförts. |
| | **deviceJobStatistics.runningCount**: antalet enheter som för närvarande körs jobbet. |
| | **deviceJobStatistics.pendingCount**: antalet enheter som väntar jobbet ska köras. |

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