---
title: "Förstå Azure IoT Hub direkt metoder | Microsoft Docs"
description: "Utvecklarhandbok - Använd direkt metoder för att anropa kod på dina enheter från en app service."
services: iot-hub
documentationcenter: .net
author: nberdy
manager: timlt
editor: 
ms.assetid: 9f0535f1-02e6-467a-9fc4-c0950702102d
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d23bf20e4483b102fe5d946cb017dce1769b39a1
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Förstå och anropa direkt metoder från IoT-hubb
IoT-hubb kan du anropa direkt metoder på enheter från molnet. Direkta metoder representerar en request-reply-interaktion med en enhet som liknar ett HTTP-anrop i att de lyckas eller misslyckas omedelbart (efter en användardefinierade timeout). Den här metoden är användbar för scenarier där loppet av omedelbara åtgärder är olika beroende på om enheten har kunna svara, till exempel skicka ett SMS-wake-up till en enhet om en enhet är offline (SMS är dyrare än ett metodanrop).

Varje enhet metod riktar sig till en enda enhet. [Jobb] [ lnk-devguide-jobs] är ett sätt att anropa direkt metoder på flera enheter och schemalägga metodanropet för frånkopplade enheter.

Alla med **tjänsten ansluta** behörigheter för IoT-hubb kan anropa en metod på en enhet.

Direkta metoder följer ett mönster i begäran och svar och är avsedda för kommunikation som kräver omedelbar bekräftelse av deras resultat, vanligtvis interaktiv kontroll på enheten, till exempel för att aktivera en fläkt.

Referera till [moln till enhet kommunikation vägledning] [ lnk-c2d-guidance] dirigera om osäkra mellan med hjälp av egenskaper, metoder eller moln till enhet meddelanden.

## <a name="method-lifecycle"></a>Metoden livscykel
Direkta metoder implementeras på enheten och kan kräva noll eller fler indata i metod nyttolasten att instansiera korrekt. Du anropa en metod som har direkt via en tjänst-riktade URI (`{iot hub}/twins/{device id}/methods/`). En enhet tar emot direkt metoder igenom avsnittet enhetsspecifika MQTT (`$iothub/methods/POST/{method name}/`). Vi stöder direkt metoder på ytterligare enheter på klientsidan nätverksprotokoll i framtiden.

> [!NOTE]
> När du anropar en direkt metod på en enhet, egenskapsnamn och värden kan endast innehålla US-ASCII utskrivbara alfanumeriskt, förutom eventuella i följande: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.
> 
> 

Direkta metoder är synkrona och antingen lyckas eller misslyckas när tidsgränsen (standard: 30 sekunder, ange upp till 3 600 sekunder). Direkta metoder är användbara i interaktiva scenarier där du vill att en enhet så att den fungerar endast om enheten är online och ta emot kommandon, som att slå på en enstaka via telefonen. I dessa scenarier som du vill se en omedelbar lyckad eller misslyckad så Molntjänsten kan fungera på resultatet så snart som möjligt. Enheten kan returnera vissa meddelandetexten på grund av metoden, men det inte krävs att göra det-metoden. Det finns ingen garanti på sortering eller alla samtidighet semantik på metodanrop.

Dirigera metoder är HTTPS endast från molnet sida och MQTT eller AMQP från enheter-sidan.

Nyttolasten för metodbegäranden och svar är en JSON-dokumentet upp till 8 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Anropa en metod som är direkt från en backend-app
### <a name="method-invocation"></a>Metodanropet
Direkt metod anrop på en enhet är HTTPS-anrop som omfattar:

* Den *URI* specifik för enheten (`{iot hub}/twins/{device id}/methods/`)
* EFTER *metod*
* *Huvuden* som innehåller tillstånd, begär ID, content-type och Innehållskodning
* En transparent JSON *brödtext* i följande format:

   ```
   {
       "methodName": "reboot",
       "responseTimeoutInSeconds": 200,
       "payload": {
           "input1": "someInput",
           "input2": "anotherInput"
       }
   }
   ```

Tidsgränsen är i sekunder. Om inte tidsgränsen anges standardvärdet 30 sekunder.

### <a name="response"></a>Svar
Backend-appen tar emot ett svar som omfattar:

* *HTTP-statuskod*, som används för fel som kommer från IoT-hubben, inklusive ett 404-fel för enheter som för närvarande inte ansluten
* *Huvuden* som innehåller en ETag, begär ID, content-type och Innehållskodning
* En JSON *brödtext* i följande format:

   ```
   {
       "status" : 201,
       "payload" : {...}
   }
   ```

   Båda `status` och `body` som tillhandahålls av enheten och används för att svara med enhetens egna statuskod och/eller beskrivning.

## <a name="handle-a-direct-method-on-a-device"></a>Hantera en direkt metod på en enhet
### <a name="method-invocation"></a>Metodanropet
Enheter får direkta metodbegäranden om MQTT avsnittet:`$iothub/methods/POST/{method name}/?$rid={request id}`

Meddelandetexten som tar emot enheten är i följande format:

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Metodbegäranden är QoS 0.

### <a name="response"></a>Svar
Enheten skickar svar till `$iothub/methods/res/{status}/?$rid={request id}`, där:

* Den `status` egenskapen innebär att enheten har angett metod utförs.
* Den `$rid` egenskapen är begäran-ID från metodanropet togs emot från IoT-hubb.

Innehållet har angetts av enheten och kan vara status.

## <a name="additional-reference-material"></a>Ytterligare referensmaterialet
Andra referensavsnitten i utvecklarhandboken för IoT-hubben är:

* [IoT-hubbslutpunkter] [ lnk-endpoints] beskriver de olika slutpunkter som varje IoT-hubb visar för körning och hanteringsåtgärder.
* [Begränsning och kvoter] [ lnk-quotas] beskriver kvoterna som gäller för IoT-hubb-tjänsten och bandbreddsbegränsning beteende som händer när du använder tjänsten.
* [Azure IoT-enheten och tjänsten SDK] [ lnk-sdks] Listar olika språk SDK: er som du kan använda när du utvecklar appar för både enheten och tjänsten som interagerar med IoT-hubben.
* [IoT-hubb frågespråk för enheten twins, jobb och meddelanderoutning] [ lnk-query] beskriver IoT-hubb frågespråk som du kan använda för att hämta information från IoT-hubb om enheten twins och jobb.
* [Stöd för IoT-hubb MQTT] [ lnk-devguide-mqtt] ger mer information om stöd för IoT-hubb för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg
Nu du har lärt dig hur du använder direkta metoder, kan du är intresserad av i följande artikel för IoT-hubb developer-guide:

* [Schema-jobb på flera enheter][lnk-devguide-jobs]

Om du vill testa vissa av de begrepp som beskrivs i den här artikeln får du är intresserad av följande IoT-hubb kursen:

* [Använda direct-metoder][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
