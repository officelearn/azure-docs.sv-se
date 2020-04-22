---
title: Förstå Azure IoT Hub-direktmetoder | Microsoft-dokument
description: Utvecklarguide – använd direkta metoder för att anropa kod på dina enheter från en tjänstapp.
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: rezas
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 13936a55baed59d5b6257f13f69305a1ce72927a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730389"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Förstå och anropa direktmetoder från IoT Hub

IoT Hub ger dig möjlighet att anropa direkta metoder på enheter från molnet. Direktmetoder representerar en interaktion med begäran och svar med en enhet som liknar ett HTTP-anrop eftersom de lyckas eller misslyckas omedelbart (efter en tidsgränsen för användaren). Den här metoden är användbar för scenarier där det omedelbara tillvägagångssättet är olika beroende på om enheten kunde svara.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Varje enhetsmetod riktar sig till en enda enhet. [Schemalägg jobb på flera enheter](iot-hub-devguide-jobs.md) visar hur du kan anropa direkta metoder på flera enheter och schemalägga metodanrop för frånkopplade enheter.

Alla med **tjänstanslutningsbehörighet** på IoT Hub kan anropa en metod på en enhet.

Direkta metoder följer ett mönster för begäran-svar och är avsedda för kommunikation som kräver omedelbar bekräftelse av deras resultat. Till exempel interaktiv styrning av enheten, till exempel att slå på en fläkt.

Se [kommunikationsvägledning från molnet till enheten](iot-hub-devguide-c2d-guidance.md) om du är osäker på hur du använder önskade egenskaper, direkta metoder eller meddelanden från molnet till enheten.

## <a name="method-lifecycle"></a>Metodens livscykel

Direkta metoder implementeras på enheten och kan kräva noll eller fler indata i metoden nyttolast för att korrekt instansiera. Du anropar en direkt metod via en`{iot hub}/twins/{device id}/methods/`tjänstinriktad URI ( ). En enhet tar emot direkta metoder via ett`$iothub/methods/POST/{method name}/`enhetsspecifikt MQTT-ämne ( ) eller via AMQP-länkar `IoThub-methodname` (egenskaperna och `IoThub-status` programegenskaperna). 

> [!NOTE]
> När du anropar en direkt metod på en enhet kan egenskapsnamn och värden bara innehålla alfanumeriskt utskrivbart för USA-ASCII, förutom i följande uppsättning:``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

Direktmetoder är synkrona och lyckas eller misslyckas antingen efter timeout-perioden (standard: 30 sekunder, som kan anges mellan 5 och 300 sekunder). Direkta metoder är användbara i interaktiva scenarier där du vill att en enhet ska agera om och endast om enheten är online och tar emot kommandon. Till exempel slå på en lampa från en telefon. I dessa scenarier vill du se en omedelbar framgång eller ett omedelbart fel så att molntjänsten kan agera på resultatet så snart som möjligt. Enheten kan returnera en del meddelandetext som ett resultat av metoden, men det krävs inte för att metoden ska kunna göra det. Det finns ingen garanti för beställning eller samtidighet semantik på metodanrop.

Direktmetoder är endast HTTPS från molnsidan och MQTT eller AMQP från enhetssidan.

Nyttolasten för metodbegäranden och svar är ett JSON-dokument på upp till 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Anropa en direkt metod från en backend-app

Nu, anropa en direkt metod från en backend-app.

### <a name="method-invocation"></a>Metod åkallan

Direkta metodanrop på en enhet är HTTPS-anrop som består av följande objekt:

* Begäran *URI* som är specifik för enheten tillsammans med [API-versionen:](/rest/api/iothub/service/devicemethod/invokedevicemethod)

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* *POST-metoden*

* *Rubriker* som innehåller auktorisering, begärande-ID, innehållstyp och innehållskodning.

* En transparent *JSON-brödtext* i följande format:

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

Värdet som `responseTimeoutInSeconds` anges som i begäran är den tid som IoT Hub-tjänsten måste vänta på slutförandet av en direkt metodkörning på en enhet. Ange att den här timeouten ska vara minst lika lång som den förväntade körningstiden för en direkt metod med en enhet. Om timeout inte anges används standardvärdet på 30 sekunder. Minimi- och maximivärdena för `responseTimeoutInSeconds` är 5 respektive 300 sekunder.

Värdet som `connectTimeoutInSeconds` anges som i begäran är den tid som krävs för anrop av en direkt metod som IoT Hub-tjänsten måste vänta på att en frånkopplad enhet ska anslutas. Standardvärdet är 0, vilket innebär att enheter redan måste vara online vid anrop av en direkt metod. Det maximala `connectTimeoutInSeconds` värdet för är 300 sekunder.


#### <a name="example"></a>Exempel

Se nedan för ett `curl`barebone exempel med . 

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

Backend-appen får ett svar som består av följande objekt:

* *HTTP-statuskod:*
  * 200 anger ett framgångsrikt genomförande av direkt metod.
  * 404 anger att antingen enhets-ID är ogiltigt, eller att enheten inte `connectTimeoutInSeconds` var online vid anrop av en direkt metod och därefter (använd tillsammans medföljande felmeddelande för att förstå grundorsaken);
  * 504 anger timeout för gateway som orsakas av `responseTimeoutInSeconds`enheten som inte svarar på ett direkt metodanrop inom .

* *Rubriker* som innehåller ETag, begärande-ID, innehållstyp och innehållskodning.

* En *JSON-brödtext* i följande format:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Båda `status` `body` och tillhandahålls av enheten och används för att svara med enhetens egen statuskod och/eller beskrivning.

### <a name="method-invocation-for-iot-edge-modules"></a>Metodanrop för IoT Edge-moduler

Att anropa direkta metoder med hjälp av ett modul-ID stöds i [IoT Service Client C# SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

För detta ändamål, `ServiceClient.InvokeDeviceMethodAsync()` använd metoden `deviceId` och `moduleId` passera i och som parametrar.

## <a name="handle-a-direct-method-on-a-device"></a>Hantera en direkt metod på en enhet

Låt oss titta på hur man hanterar en direkt metod på en IoT-enhet.

### <a name="mqtt"></a>MQTT

Följande avsnitt är för MQTT-protokollet.

#### <a name="method-invocation"></a>Metod åkallan

Enheter får direkta metodbegäranden om `$iothub/methods/POST/{method name}/?$rid={request id}`MQTT-ämnet: . Antalet prenumerationer per enhet är begränsat till 5. Det rekommenderas därför att man inte abonnerar på varje direkt metod individuellt. I stället överväga att `$iothub/methods/POST/#` prenumerera på och sedan filtrera levererade meddelanden baserat på önskad metodnamn.

Brödtexten som enheten tar emot är i följande format:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Metodbegäranden är QoS 0.

#### <a name="response"></a>Svar

Enheten skickar svar `$iothub/methods/res/{status}/?$rid={request id}`till , där:

* Egenskapen `status` är den enhetsbaserade statusen för metodkörning.

* Egenskapen `$rid` är begärande-ID från den metodanrop som tas emot från IoT Hub.

Kroppen ställs in av enheten och kan vara vilken status som helst.

### <a name="amqp"></a>AMQP

Följande avsnitt är för AMQP-protokollet.

#### <a name="method-invocation"></a>Metod åkallan

Enheten tar emot direkta metodbegäranden genom `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`att skapa en mottagningslänk på adressen .

AMQP-meddelandet anländer till mottagningslänken som representerar metodbegäran. Den innehåller följande avsnitt:

* Egenskapen correlation ID, som innehåller ett begärande-ID som ska skickas tillbaka med motsvarande metodsvar.

* En programegenskap med namnet `IoThub-methodname`, som innehåller namnet på den metod som anropas.

* DEN AMQP meddelande kropp innehållande den metod nyttolasten så JSON.

#### <a name="response"></a>Svar

Enheten skapar en skickande länk för att `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`returnera metodsvaret på adressen .

Metodens svar returneras på den sändande länken och är strukturerad enligt följande:

* Egenskapen korrelations-ID, som innehåller begärande-ID:et som skickades i metodens begärandemeddelande.

* En programegenskap med namnet `IoThub-status`, som innehåller den användarinförsedda metodstatusen.

* DEN AMQP meddelande kropp som innehåller metodsvar som JSON.

## <a name="additional-reference-material"></a>Ytterligare referensmaterial

Andra referensavsnitt i utvecklarhandboken för IoT Hub är:

* [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md) beskriver de olika slutpunkter som varje IoT-hubb exponerar för körnings- och hanteringsåtgärder.

* [Begränsning och kvoter](iot-hub-devguide-quotas-throttling.md) beskriver de kvoter som gäller och begränsningsbeteendet som du kan förvänta dig när du använder IoT Hub.

* [Azure IoT-enhet och tjänst SDK visar](iot-hub-devguide-sdks.md) de olika språk SDK:er som du kan använda när du utvecklar både enhets- och tjänstappar som interagerar med IoT Hub.

* [IoT Hub-frågespråk för enhetstvillingar, jobb och meddelanderoutning](iot-hub-devguide-query-language.md) beskriver det IoT Hub-frågespråk som du kan använda för att hämta information från IoT Hub om dina enhetstvillingar och jobb.

* [IoT Hub MQTT-stöd](iot-hub-mqtt-support.md) ger mer information om IoT Hub-stöd för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du använder direkta metoder, kan du vara intresserad av följande IoT Hub developer guide artikel:

* [Schemalägga jobb på flera enheter](iot-hub-devguide-jobs.md)

Om du vill prova några av de begrepp som beskrivs i den här artikeln kan du vara intresserad av följande IoT Hub-självstudiekurs:

* [Använda direkta metoder](quickstart-control-device-node.md)
* [Enhetshantering med Azure IoT-verktyg för VS Code](iot-hub-device-management-iot-toolkit.md)
