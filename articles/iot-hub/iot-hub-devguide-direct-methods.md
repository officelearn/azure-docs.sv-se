---
title: Förstå Azure IoT Hub Direct-metoder | Microsoft Docs
description: Guide för utvecklare – Använd direkta metoder för att anropa kod på dina enheter från en service app.
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: rezas
ms.openlocfilehash: dcbc03257b8bfeacda700f60f2724f2d02ec147d
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048276"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Förstå och anropa direkt metoder från IoT Hub

IoT Hub ger dig möjlighet att anropa direkta metoder på enheter från molnet. Direkta metoder representerar en förfrågan-svar-interaktion med en enhet som liknar ett HTTP-anrop i som de lyckas eller Miss lyckas omedelbart (efter en användardefinierad tids gräns). Den här metoden är användbar för scenarier där omedelbara åtgärder är olika beroende på om enheten kunde svara.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Varje enhets metod är riktad mot en enda enhet. [Schemalägg jobb på flera enheter](iot-hub-devguide-jobs.md) visar hur du kan anropa direkta metoder på flera enheter och schemalägga metod anrop för frånkopplade enheter.

Alla med **service Connect-** behörigheter på IoT Hub kan anropa en metod på en enhet.

Direkta metoder följer ett mönster för förfrågnings svar och är avsedda för kommunikation som kräver omedelbar bekräftelse av deras resultat. Till exempel en interaktiv kontroll av enheten, till exempel att aktivera en fläkt.

Se [rikt linjer för kommunikation från moln till enhet](iot-hub-devguide-c2d-guidance.md) om du är tveksam mellan att använda önskade egenskaper, direkta metoder eller meddelanden från molnet till enheten.

## <a name="method-lifecycle"></a>Metod livs cykel

Direkta metoder implementeras på enheten och kan kräva noll eller flera indata i metodens nytto last för att instansieras korrekt. Du anropar en direkt metod via en tjänst som är riktad till en URI (`{iot hub}/twins/{device id}/methods/`). En enhet tar emot direkta metoder via ett enhetsspecifika MQTT-avsnitt (`$iothub/methods/POST/{method name}/`) eller via AMQP-länkar (egenskaperna `IoThub-methodname` och `IoThub-status` program). 

> [!NOTE]
> När du anropar en direkt metod på en enhet kan egenskaps namn och värden bara innehålla US ASCII-utskrivbar alfanumerisk, förutom i följande uppsättning: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

Direkta metoder är synkrona och slutförs eller Miss lyckas efter tids gräns perioden (standard: 30 sekunder, vilket är upp till 300 sekunder). Direkta metoder är användbara i interaktiva scenarier där du vill att en enhet ska agera om och endast om enheten är online och tar emot kommandon. Du kan till exempel aktivera en ljus källa från en telefon. I dessa scenarier vill du se en omedelbar framgång eller ett haveri, så att moln tjänsten kan agera på resultatet så snart som möjligt. Enheten kan returnera viss meddelande text som ett resultat av metoden, men det krävs inte för att metoden ska kunna användas. Det finns ingen garanti för beställning eller någon samtidig semantik för metod anrop.

Direkta metoder är endast HTTPS-från moln sidan och MQTT eller AMQP från enhets sidan.

Nytto lasten för metod begär Anden och svar är ett JSON-dokument som är upp till 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Anropa en direkt metod från en backend-app

Starta nu en direkt metod från en backend-app.

### <a name="method-invocation"></a>Metod anrop

Direkta metod anrop på en enhet är HTTPS-anrop som består av följande objekt:

* *URI-begäran* som är unik för enheten tillsammans med [API-versionen](/rest/api/iothub/service/invokedevicemethod):

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* POST- *metoden*

* *Huvuden* som innehåller auktorisering, FÖRFRÅGNINGS-ID, innehålls typ och innehålls kodning.

* En genomskinlig JSON- *brödtext* i följande format:

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

Värdet som anges som `responseTimeoutInSeconds` i begäran är den tid som IoT Hub tjänsten måste vänta på att en direkt metod ska slutföras på en enhet. Ange att denna tids gräns ska vara minst så lång som den förväntade körnings tiden för en direkt metod av en enhet. Om ingen tids gräns anges används standardvärdet på 30 sekunder. De lägsta och högsta värdena för `responseTimeoutInSeconds` är 5 respektive 300 sekunder.

Värdet som anges som `connectTimeoutInSeconds` i begäran är tiden då det tar slut på en direkt metod som IoT Hub tjänsten måste vänta på att en frånkopplad enhet ska anslutas till onlineläge. Standardvärdet är 0, vilket innebär att enheterna redan måste vara online när den direkta metoden har anropats. Det maximala värdet för `connectTimeoutInSeconds` är 300 sekunder.


#### <a name="example"></a>Exempel

Se nedan för ett Barebone-exempel som använder `curl`. 

```bash
curl -X POST \
  https://iothubname.azure-devices.net/twins/myfirstdevice/methods?api-version=2018-06-30 \
  -H 'Authorization: SharedAccessSignature sr=iothubname.azure-devices.net&sig=x&se=x&skn=iothubowner' \
  -H 'Content-Type: application/json' \
  -d '{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}'
```

### <a name="response"></a>Svar

Backend-appen tar emot ett svar som består av följande objekt:

* *Http-status kod*:
  * 200 indikerar lyckad körning av direkt metod;
  * 404 anger att enhets-ID: t är ogiltigt eller att enheten inte var online när den direkta metoden har anropats, och för `connectTimeoutInSeconds` efteråt (Använd fel meddelande för att förstå rotor saken).
  * 504 anger Gateway-timeout som orsakas av att enheten inte svarar på ett direkt metod anrop inom `responseTimeoutInSeconds`.

* *Huvuden* som innehåller etag, FÖRFRÅGNINGS-ID, innehålls typ och innehålls kodning.

* En JSON- *brödtext* i följande format:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Både `status` och `body` tillhandahålls av enheten och används för att svara med enhetens egen status kod och/eller beskrivning.

### <a name="method-invocation-for-iot-edge-modules"></a>Metod anrop för IoT Edge moduler

Det finns stöd för att anropa direkta metoder med ett modul-ID i [IoT C# -tjänstens klient-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

För det här ändamålet använder du metoden `ServiceClient.InvokeDeviceMethodAsync()` och skickar i `deviceId` och `moduleId` som parametrar.

## <a name="handle-a-direct-method-on-a-device"></a>Hantera en direkt metod på en enhet

Nu ska vi titta på hur du hanterar en direkt metod på en IoT-enhet.

### <a name="mqtt"></a>MQTT

Följande avsnitt gäller för MQTT-protokollet.

#### <a name="method-invocation"></a>Metod anrop

Enheter tar emot direkta metod begär anden i MQTT-avsnittet: `$iothub/methods/POST/{method name}/?$rid={request id}`. Antalet prenumerationer per enhet är begränsat till 5. Vi rekommenderar därför att du inte prenumererar på varje direkt metod individuellt. Överväg i stället att prenumerera på `$iothub/methods/POST/#` och filtrera sedan de levererade meddelandena utifrån dina önskade metod namn.

Texten som enheten tar emot är i följande format:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Metod begär Anden är QoS 0.

#### <a name="response"></a>Svar

Enheten skickar svar till `$iothub/methods/res/{status}/?$rid={request id}`, där:

* Egenskapen `status` är enhets statusen för metod körning.

* Egenskapen `$rid` är förfrågnings-ID: t från det metod anrop som togs emot från IoT Hub.

Texten anges av enheten och kan vara vilken status som helst.

### <a name="amqp"></a>AMQP

Följande avsnitt gäller för AMQP-protokollet.

#### <a name="method-invocation"></a>Metod anrop

Enheten tar emot direkta metod begär Anden genom att skapa en Receive-länk på adress `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

AMQP-meddelandet anländer till Receive-länken som representerar metoden Request. Den innehåller följande avsnitt:

* Egenskapen korrelations-ID, som innehåller ett ID för begäran som ska skickas tillbaka med motsvarande metod svar.

* En program egenskap med namnet `IoThub-methodname`, som innehåller namnet på den metod som anropas.

* AMQP meddelande text som innehåller metodens nytto last som JSON.

#### <a name="response"></a>Svar

Enheten skapar en sändnings länk för att returnera metod svaret på adress `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

Metodens svar returneras för länken som skickas och struktureras på följande sätt:

* Egenskapen korrelations-ID, som innehåller det ID för begäran som skickades i metodens begär ande meddelande.

* En program egenskap med namnet `IoThub-status`, som innehåller den angivna metod statusen för användaren.

* AMQP meddelande text som innehåller metod svaret som JSON.

## <a name="additional-reference-material"></a>Ytterligare referens material

Andra referens ämnen i IoT Hub Developer Guide är:

* [IoT Hub slut punkter](iot-hub-devguide-endpoints.md) beskriver de olika slut punkter som varje IoT-hubb visar för körnings-och hanterings åtgärder.

* [Begränsning och kvoter](iot-hub-devguide-quotas-throttling.md) beskriver de kvoter som gäller och begränsnings beteendet förväntas när du använder IoT Hub.

* [Azure IoT-enhet och tjänst-SDK](iot-hub-devguide-sdks.md) : er listar de olika språk-SDK: er som du kan använda när du utvecklar både enhets-och tjänst program som samverkar IoT Hub med

* [IoT Hub frågespråk för enhets dubbla, jobb och meddelanderoutning](iot-hub-devguide-query-language.md) beskriver det IoT Hub frågespråket som du kan använda för att hämta information från IoT Hub om din enhets dubblare och jobb.

* [IoT Hub MQTT-support](iot-hub-mqtt-support.md) innehåller mer information om IoT Hub-stöd för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du använder direkta metoder, men du kanske är intresse rad av följande artikel för IoT Hub Developer-guiden:

* [Schemalägga jobb på flera enheter](iot-hub-devguide-jobs.md)

Om du vill testa några av de begrepp som beskrivs i den här artikeln kan du vara intresse rad av följande IoT Hub själv studie kurs:

* [Använd direkta metoder](quickstart-control-device-node.md)
* [Enhets hantering med Azure IoT Tools för VS Code](iot-hub-device-management-iot-toolkit.md)
