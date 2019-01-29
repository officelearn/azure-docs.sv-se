---
title: Förstå Azure IoT Hub direkta metoder | Microsoft Docs
description: Utvecklarguide – Använd direkta metoder för att anropa kod på dina enheter från en service-app.
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: nberdy
ms.openlocfilehash: caccdcb0d90a817c82e8d7816f0770b09b4ea27a
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55101030"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Förstå och anropa direktmetoder från IoT Hub

IoT Hub ger dig möjlighet att anropa direktmetoder på enheter från molnet. Direkta metoder representerar en begäran / svar-interaktion med en enhet som liknar ett HTTP-anrop i att de lyckas eller misslyckas omedelbart (efter en användardefinierade tidsgräns). Den här metoden är användbart för scenarier där loppet av omedelbara åtgärder är olika beroende på om enheten var kunna svara.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Varje enhetsmetod riktar sig mot en enskild enhet. [Schemalägg jobb på flera enheter](iot-hub-devguide-jobs.md) visar hur du ger dig ett sätt att anropa direktmetoder på flera enheter och schemalägga metodanropet för frånkopplade enheter.

Vem som helst med **tjänsten ansluta** behörigheter på IoT Hub kan anropa en metod på en enhet.

Direkta metoder följer ett mönster för begäranden och svar och är avsedda för kommunikation som kräver omedelbar bekräftelse av deras resultat. Till exempel interaktiva kontroll över enheten, till exempel aktivera en fläkt.

Referera till [moln till enhet kommunikation vägledning](iot-hub-devguide-c2d-guidance.md) direkta metoder eller meddelanden från moln till enhet om du tvekar mellan att använda önskade egenskaper.

## <a name="method-lifecycle"></a>Metoden livscykel

Direkta metoder implementeras på enheten och kan kräva noll eller flera inmatningar i nyttolasten för metoden ska instansieras korrekt. Du anropa en direkt metod via en tjänst för webbservergrupper på URI (`{iot hub}/twins/{device id}/methods/`). En enhet tar emot direkta metoder via ett ämne för enhetsspecifika MQTT (`$iothub/methods/POST/{method name}/`) eller via AMQP-länkar (den `IoThub-methodname` och `IoThub-status` programegenskaper). 

> [!NOTE]
> När du anropar en direkt metod på en enhet, egenskapsnamn och värden får bara innehålla US-ASCII utskrivbara alfanumeriska, med undantag för sådana i följande: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

Direkta metoder är synkrona och antingen lyckas eller misslyckas när tidsgränsen (standard: 30 sekunders mellanrum, inställbar upp till 300 sekunder). Direkta metoder är användbara i interaktiva scenarier där du vill att en enhet så att den fungerar endast om enheten är online och ta emot kommandon. Till exempel aktivera ett ljust från en telefon. I dessa scenarier som du vill se en omedelbar lyckats eller misslyckats så Molntjänsten kan fungera på resultatet så snart som möjligt. Enheten kan returnera vissa meddelandetexten till följd av metoden, men det inte krävs att göra detta-metoden. Det finns ingen garanti på sortering eller alla samtidighet semantik på metodanrop.

Direkta metoder är endast HTTPS från molnet sida, och MQTT eller AMQP från enheten.

Nyttolasten för metodbegäranden och svar är en JSON-dokumentet upp till 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Anropa en direkt metod från en backend-app

Nu kan anropa en direkt metod från en backend-app.

### <a name="method-invocation"></a>Metodanropet

Direkt metod-anrop på en enhet är HTTPS-anrop som består av följande objekt:

* Den *begärande-URI* specifik för enheten tillsammans med den [API-version](/rest/api/iothub/service/invokedevicemethod):

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* INLÄGGET *metod*

* *Rubriker* som innehåller auktoriseringen, begäran-ID, innehållstyp och Innehållskodning.

* En transparent JSON *brödtext* i följande format:

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

Tidsgränsen är i sekunder. Om tidsgränsen inte har angetts standard 30 sekunder.

#### <a name="example"></a>Exempel

Nedan finns en barebone exempel med hjälp av `curl`. 

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

* *HTTP-statuskod*, som används för fel som kommer från IoT Hub, inklusive ett 404-fel för enheter som för närvarande inte ansluten.

* *Rubriker* som innehåller ETag, begäran-ID, innehållstyp och Innehållskodning.

* En JSON *brödtext* i följande format:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Båda `status` och `body` tillhandahålls av enheten och används för att svara med statuskod för enhetens egna och/eller beskrivning.

### <a name="method-invocation-for-iot-edge-modules"></a>Metodanropet för IoT Edge-moduler

Anropa direktmetoder med hjälp av en modul ID stöds i den [klient-IoT c SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

För detta ändamål använder den `ServiceClient.InvokeDeviceMethodAsync()` metoden samt skickar den `deviceId` och `moduleId` som parametrar.

## <a name="handle-a-direct-method-on-a-device"></a>Hantera en direkt metod på en enhet

Nu ska vi titta på hur du hanterar en direkt metod på en IoT-enhet.

### <a name="mqtt"></a>MQTT

Följande avsnitt avser MQTT-protokollet.

#### <a name="method-invocation"></a>Metodanropet

Enheter tar emot begäranden direkt metod på avsnittet MQTT: `$iothub/methods/POST/{method name}/?$rid={request id}`. Antalet prenumerationer per enhet är begränsad till 5. Därför rekommenderas inte att prenumerera på varje direkt metod individuellt. I stället överväga att prenumerera `$iothub/methods/POST/#` och sedan filtrera levererade meddelanden baserat på din önskade metodnamn.

Brödtexten som enheten tar emot är i följande format:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Metoden begärandena QoS 0.

#### <a name="response"></a>Svar

Enheten skickar svar till `$iothub/methods/res/{status}/?$rid={request id}`, där:

* Den `status` egenskapen är enheten anger status för metod utförs.

* Den `$rid` egenskapen är ID för begäran från metodanropet togs emot från IoT Hub.

Brödtexten anges av enheten och kan vara vilken status som helst.

### <a name="amqp"></a>AMQP

Följande avsnitt avser AMQP-protokollet.

#### <a name="method-invocation"></a>Metodanropet

Enheten tar emot direkta metodbegäranden genom att skapa en receive-länk på adressen `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

AMQP-meddelande anländer på länken receive som representerar metodbegäran. Det innehåller följande avsnitt:

* Egenskapen Korrelations-ID, som innehåller en begäran-ID som ska skickas med den motsvarande metod responsen.

* En programegenskapen med namnet `IoThub-methodname`, som innehåller namnet på den metod som anropas.

* AMQP-meddelandetext som innehåller metoden nyttolasten som JSON.

#### <a name="response"></a>Svar

Enheten skapar en Skicka länk för att returnera metodsvaret på adressen `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

Metodens svaret returneras på länken sändning och är strukturerade på följande sätt:

* Egenskapen Korrelations-ID, som innehåller ID för förfrågan skickades i begärandemeddelandet metodens.

* En programegenskapen med namnet `IoThub-status`, som innehåller användaren angav metodstatus.

* AMQP-meddelandetext som innehåller metodsvaret som JSON.

## <a name="additional-reference-material"></a>Ytterligare referensmaterial

Andra referensavsnitten i IoT Hub developer guide inkluderar:

* [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md) beskriver de olika slutpunkter som varje IoT-hubb exponerar för körning och hanteringsåtgärder.

* [Begränsning och kvoter](iot-hub-devguide-quotas-throttling.md) beskriver kvoter som gäller och beteendet som händer när du använder IoT Hub.

* [Azure IoT-enheten och tjänsten SDK: er](iot-hub-devguide-sdks.md) visar en lista över olika språk SDK: er som du kan använda när du utvecklar appar för både enheten och tjänsten som interagerar med IoT Hub.

* [IoT Hub-frågespråk för enhetstvillingar, jobb och meddelanderoutning](iot-hub-devguide-query-language.md) beskriver IoT Hub-frågespråk som du kan använda för att hämta information från IoT Hub om enhetstvillingar och jobb.

* [IoT Hub MQTT-support](iot-hub-mqtt-support.md) innehåller mer information om IoT Hub-stöd för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Nu du har lärt dig hur du använder direkta metoder, kanske du är intresserad av i följande artikel för IoT Hub developer guide:

* [Schemalägga jobb på flera enheter](iot-hub-devguide-jobs.md)

Om du vill prova några av de koncept som beskrivs i den här artikeln, kanske du är intresserad av följande självstudie för IoT Hub:

* [Använd direkta metoder](quickstart-control-device-node.md)
* [Enhetshantering med Azure IoT-verktyg för VS Code](iot-hub-device-management-iot-toolkit.md)
