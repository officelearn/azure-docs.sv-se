---
title: "Förstå Azure IoT-hubb MQTT support | Microsoft Docs"
description: "Utvecklarhandbok - stöd för enheter som ansluter till en IoT-hubb enheten riktade slutpunkt med MQTT-protokollet. Innehåller information om inbyggda MQTT stöd i Azure IoT-enhet SDK: er."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 1d71c27c-b466-4a40-b95b-d6550cf85144
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/11/2017
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 309396badf3a4daa4c339a280f774bcd500ce3bb
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2017
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Kommunicera med din IoT-hubb med MQTT-protokollet

IoT-hubb kan enheter kommunicerar med IoT-hubb enheten slutpunkter som använder den [MQTT v3.1.1] [ lnk-mqtt-org] -protokollet på port 8883 eller MQTT v3.1.1 via WebSocket-protokollet på port 443. IoT-hubb kräver alla enhetskommunikation som ska skyddas med hjälp av TLS/SSL (därför IoT-hubb stöder inte är säkra anslutningar via port 1883).

## <a name="connecting-to-iot-hub"></a>Ansluter till IoT-hubb

En enhet kan använda MQTT-protokollet för att ansluta till en IoT-hubb som antingen med hjälp av bibliotek i den [Azure IoT SDK] [ lnk-device-sdks] eller genom att använda MQTT protokollet direkt.

## <a name="using-the-device-sdks"></a>Använder enhet SDK

[Enheten SDK] [ lnk-device-sdks] som har stöd för protokollet MQTT är tillgängliga för Java, Node.js, C, C# och Python. Enheten SDK använder standard IoT-hubb-anslutningssträngen för att upprätta en anslutning till en IoT-hubb. Om du vill använda protokollet MQTT klienten protocol-parametern måste anges till **MQTT**. Som standard enheten SDK ansluta till en IoT-hubb med den **CleanSession** -flaggan inställd på **0** och använda **QoS 1** för utbyte av meddelanden med IoT-hubben.

När en enhet är ansluten till en IoT-hubb, ger enheten SDK metoder som gör att enheten att skicka meddelanden till och ta emot meddelanden från en IoT-hubb.

Följande tabell innehåller länkar till kodexempel för varje språk som stöds och anger parametern som ska användas för att upprätta en anslutning till IoT-hubb med MQTT-protokollet.

| Språk | Protokoll-parameter |
| --- | --- |
| [Node.js][lnk-sample-node] |Azure-iot-enhet – mqtt |
| [Java][lnk-sample-java] |IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] |MQTT_Protocol |
| [C#][lnk-sample-csharp] |TransportType.Mqtt |
| [Python][lnk-sample-python] |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrera en enhetsapp från AMQP till MQTT

Om du använder den [enheten SDK][lnk-device-sdks], växlar från att använda AMQP till MQTT kräver ändring av protokoll-parametern i initieringen av klienten som nämnts tidigare anger.

När du gör det måste du se till att kontrollera följande:

* AMQP returnerar fel för många villkor när MQTT avslutar anslutningen. Din undantagshantering logik kan därför kräva några ändringar.
* MQTT stöder inte den *avvisa* åtgärder när du tar emot [moln till enhet meddelanden][lnk-messaging]. Om backend-app behöver få svar från enhetsapp, bör du använda [direkt metoder][lnk-methods].

## <a name="using-the-mqtt-protocol-directly"></a>Protokollet MQTT direkt
Om en enhet inte kan använda enheten SDK: er, kan den fortfarande ansluta till offentliga enhet-slutpunkter med protokollet MQTT på port 8883. I den **Anslut** paket enheten ska använda följande värden:

* För den **ClientId** , ska du använda den **deviceId**.
* För den **användarnamn** , ska du använda `{iothubhostname}/{device_id}/api-version=2016-11-14`, där {iothubhostname} är fullständig CName för IoT-hubben.

    Om namnet på din IoT-hubb är till exempel **contoso.azure devices.net** och om namnet på din enhet är **MyDevice01**, hela **användarnamn** fältet ska innehålla `contoso.azure-devices.net/MyDevice01/api-version=2016-11-14`.
* För den **lösenord** , ska du använda en SAS-token. Formatet på SAS-token är desamma som för HTTPS- och AMQP protokoll:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.

    >[!NOTE]
    >SAS-token lösenord krävs inte om du använder autentisering för X.509-certifikat. Mer information finns i [Konfigurera X.509 säkerheten i din Azure IoT-hubb][lnk-x509]

    Mer information om hur du skapar SAS-token i avsnittet enhet av [med IoT-hubb säkerhetstoken][lnk-sas-tokens].

    När du testar, du kan också använda den [enheten explorer] [ lnk-device-explorer] verktyg för att snabbt skapa en SAS-token som du kan kopiera och klistra in i din egen kod:

  1. Gå till den **Management** fliken i **enheten Explorer**.
  2. Klicka på **SAS-Token** (övre högra).
  3. På **SASTokenForm**, Välj din enhet i den **DeviceID** listrutan. Ange din **TTL**.
  4. Klicka på **generera** att skapa din token.

     SAS-token som skapas har den här strukturen: `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

     En del av denna token som ska användas som den **lösenord** fältet att ansluta med MQTT är: `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

MQTT ansluta och koppla från paket, IoT-hubb utfärdar en händelse på den **Operations Monitoring** kanalen med ytterligare information som kan hjälpa dig att felsöka problem med nätverksanslutningen.

### <a name="tlsssl-configuration"></a>TLS/SSL-konfiguration

För att använda MQTT direkt, protokollet klienten *måste* ansluta över TLS/SSL. Försöker hoppa över detta misslyckas med fel vid anslutning.

Du kan behöva hämta och referera till DigiCert Baltimore-rotcertifikat för att upprätta en TLS-anslutning. Detta är det certifikat som Azure använder för att säkra anslutningen och finns i den [Azure-iot-sdk-c databasen][lnk-sdk-c-certs]. Mer information om dessa certifikat finns på [Digicerts webbplats][lnk-digicert-root-certs].

Ett exempel på hur du implementerar det använder Python-versionen av den [Paho MQTT biblioteket] [ lnk-paho] Eclipse Foundation kan se ut ungefär så här.

Installera först Paho biblioteket från kommandoraden miljön:

```
>pip install paho-mqtt
```

Implementera klienten i Python-skriptet:

```
from paho.mqtt import client as mqtt
import ssl
  
path_to_root_cer = "...local\\path\\to\\digicert.cer"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
subdomain = "<iothub subdomain>"

client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.username_pw_set(username=subdomain+".azure-devices.net/" + device_id, password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None, cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(subdomain+".azure-devices.net", port=8883)
```


### <a name="sending-device-to-cloud-messages"></a>Skicka meddelanden från enhet till moln

När du har gjort en lyckad anslutning en enhet kan skicka meddelanden till IoT-hubb med `devices/{device_id}/messages/events/` eller `devices/{device_id}/messages/events/{property_bag}` som en **avsnittsnamn**. Den `{property_bag}` -elementet låter enheten för att skicka meddelanden med ytterligare egenskaper i en url-kodat format. Exempel:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Detta `{property_bag}` -elementet använder samma kodning som frågesträngar i HTTPS-protokollet.
>
>

Appen enheten kan också använda `devices/{device_id}/messages/events/{property_bag}` som den **kommer avsnittsnamn** att definiera *kommer meddelanden* vidarebefordras som meddelandet telemetri.

- IoT-hubben har inte stöd för QoS-2-meddelanden. Om en enhetsapp publicerar ett meddelande med **QoS 2**, IoT-hubb stänger nätverksanslutningen.
- IoT-hubb sparas inte behålla meddelanden. Om en enhet skickar ett meddelande med den **behålla** -flaggan inställd på 1, IoT-hubb lägger till den **x-opt-behålla** programegenskapen i meddelandet. I det här fallet i stället för att spara behålla meddelandet skickar IoT-hubb den till backend-app.
- IoT-hubb stöder endast en aktiv MQTT-anslutning per enhet. En ny MQTT anslutning för samma enhets-ID gör IoT-hubb för att ta bort den befintliga anslutningen.

Mer information finns i [Messaging Utvecklingsguide][lnk-messaging].

### <a name="receiving-cloud-to-device-messages"></a>Ta emot meddelanden moln till enhet

Om du vill ta emot meddelanden från IoT-hubb en enhet ska prenumerera med `devices/{device_id}/messages/devicebound/#` som en **avsnittet Filter**. Flera nivåer med jokertecken `#` i avsnittet Filter används bara för att tillåta att enheten tar emot ytterligare egenskaper i avsnittsnamn. IoT-hubb tillåter inte användning av den `#` eller `?` jokertecken för filtrering av underordnade ämnen. Eftersom IoT-hubb som inte är förhandlare generella pub-sub meddelanden stöder endast dokumenterade avsnittsnamn och avsnittet filter.

Enheten inte ta emot meddelanden från IoT-hubb förrän den har prenumererar på sin enhetsspecifika slutpunkt, representeras av den `devices/{device_id}/messages/devicebound/#` avsnittet filter. Efter lyckad prenumeration har upprättats, startar enheten får endast moln till enhet-meddelanden som skickas till den efter tiden för prenumerationen. Om enheten ansluter med **CleanSession** -flaggan inställd på **0**, prenumerationen är beständig mellan olika sessioner. I det här fallet nästa gång den ansluts med **CleanSession 0** enheten tar emot utestående meddelanden som har skickats till den när den har kopplats från. Om enheten använder **CleanSession** -flaggan inställd på **1** men det inte tar emot meddelanden från IoT-hubb tills den prenumererar på sin enhet-slutpunkt.

IoT-hubb levererar meddelanden med den **avsnittsnamn** `devices/{device_id}/messages/devicebound/`, eller `devices/{device_id}/messages/devicebound/{property_bag}` om det inte finns några egenskaper för meddelande. `{property_bag}`innehåller url-kodade nyckel/värde-par för meddelandeegenskaper. Endast egenskaper för program och användaren går Systemegenskaper (exempelvis **messageId** eller **correlationId**) ingår i egenskapsuppsättningen. Egenskapsnamn för systemet har prefixet  **$** , programegenskaper använda ursprungliga egenskapsnamnet med inget prefix.

När en enhetsapp prenumererar på ett ämne med **QoS 2**, IoT-hubb ger maximal QoS nivå 1 i den **SUBACK** paket. Efter det levererar IoT-hubb meddelanden till enheten med QoS-1.

### <a name="retrieving-a-device-twins-properties"></a>När en enhet dubbla egenskaper

Först måste en enhet som prenumererar på `$iothub/twin/res/#`, för att ta emot svar för den åtgärden. Sedan skickar den ett tomt meddelande till avsnittet `$iothub/twin/GET/?$rid={request id}`, med ett värde som fylls för **begärande-ID**. Tjänsten skickar sedan ett svarsmeddelande som innehåller dubbla enhetsdata om avsnittet `$iothub/twin/res/{status}/?$rid={request id}`, använder samma **begärande-ID** som begäran.

ID för förfrågan kan vara ett giltigt värde för ett meddelande egenskapsvärde enligt [IoT-hubb messaging Utvecklingsguide][lnk-messaging], och status har verifierats som ett heltal.
Svarstexten innehåller egenskapsavsnittet för enhet dubbla:

Innehållet i registerposten identitet är begränsad till medlemmen ”egenskaper” till exempel:

        {
            "properties": {
                "desired": {
                    "telemetrySendFrequency": "5m",
                    "$version": 12
                },
                "reported": {
                    "telemetrySendFrequency": "5m",
                    "batteryLevel": 55,
                    "$version": 123
                }
            }
        }

Möjliga statuskoder är:

|Status | Beskrivning |
| ----- | ----------- |
| 200 | Lyckades |
| 429 | För många förfrågningar (begränsas), enligt [IoT-hubb begränsning][lnk-quotas] |
| 5** | Serverfel |

Mer information finns i [utvecklarhandboken för enheten twins][lnk-devguide-twin].

### <a name="update-device-twins-reported-properties"></a>Uppdatera enheten dubbla rapporterade egenskaper

Följande anvisningar beskriver hur en enhet uppdaterar rapporterade egenskaper i enheten dubbla i IoT-hubb:

1. En enhet måste först prenumerera på den `$iothub/twin/res/#` avsnittet för att ta emot den åtgärden svar från IoT-hubb.

1. En enhet skickar ett meddelande som innehåller enheten dubbla uppdateringen till den `$iothub/twin/PATCH/properties/reported/?$rid={request id}` avsnittet. Detta meddelande innehåller en **begärande-ID** värde.

1. Tjänsten skickar sedan ett svarsmeddelande som innehåller det nya ETag-värdet för rapporterade egenskapssamlingen om avsnittet `$iothub/twin/res/{status}/?$rid={request id}`. Den här svarsmeddelande använder samma **begärande-ID** som begäran.

Meddelandetexten begäran innehåller ett JSON-dokument som innehåller nya värden för rapporterade egenskaper (ingen egenskap eller metadata inte kan ändras).
Varje medlem i JSON-dokumentet uppdateras eller lägga till motsvarande medlem i den enheten dubbla dokumentet. En medlem som har angetts till `null`, tar du bort medlemmen från som innehåller objektet. Exempel:

        {
            "telemetrySendFrequency": "35m",
            "batteryLevel": 60
        }

Möjliga statuskoder är:

|Status | Beskrivning |
| ----- | ----------- |
| 200 | Lyckades |
| 400 | Felaktig begäran. Felaktig JSON |
| 429 | För många förfrågningar (begränsas), enligt [IoT-hubb begränsning][lnk-quotas] |
| 5** | Serverfel |

Mer information finns i [utvecklarhandboken för enheten twins][lnk-devguide-twin].

### <a name="receiving-desired-properties-update-notifications"></a>Ta emot meddelanden om uppdateringar egenskaper

När en enhet är ansluten, IoT-hubb skickar meddelanden till avsnittet `$iothub/twin/PATCH/properties/desired/?$version={new version}`, som innehåller innehållet i uppdateringen utförs av lösningens serverdel. Exempel:

        {
            "telemetrySendFrequency": "5m",
            "route": null
        }

För egenskapen uppdateringar `null` värdena som innebär att medlemmen JSON-objekt tas bort.


> [!IMPORTANT] 
> IoT-hubb genererar ändringsmeddelanden endast när enheterna är anslutna. Se till att implementera den [enheten återanslutning flödet] [ lnk-devguide-twin-reconnection] att behålla de egenskaper synkroniseras mellan IoT-hubb och enheter appen.

Mer information finns i [utvecklarhandboken för enheten twins][lnk-devguide-twin].

### <a name="respond-to-a-direct-method"></a>Svara på en direkt metod

Först måste en enhet har du prenumererar på `$iothub/methods/POST/#`. IoT-hubb skickar metodbegäranden till avsnittet `$iothub/methods/POST/{method name}/?$rid={request id}`, med en giltig JSON eller en brödtext.

För att svara, enheten skickar ett meddelande med en giltig JSON eller brödtext till avsnittet `$iothub/methods/res/{status}/?$rid={request id}`, där **begärande-ID** måste matcha det i meddelandet med begäran och **status** måste vara ett heltal.

Mer information finns i [direkt metod Utvecklarhandbok][lnk-methods].

### <a name="additional-considerations"></a>Annat som är bra att tänka på

Som en slutlig faktor om du behöver anpassa beteendet MQTT protokoll på sidan moln bör du granska den [Azure IoT-protokollgatewayen] [ lnk-azure-protocol-gateway] som gör att du kan distribuera ett anpassat protokoll för högpresterande gateway som direkt med IoT-hubb-gränssnitt. Azure IoT-protokollet gateway kan du anpassa protokollet enhet för brownfield MQTT distributioner eller andra anpassade protokoll. Den här metoden kräver dock att du kör och använda en anpassad protocol-gateway.

## <a name="next-steps"></a>Nästa steg

Mer information om MQTT-protokollet finns i [MQTT dokumentationen][lnk-mqtt-docs].

Mer information om hur du planerar distributionen av IoT-hubb finns:

* [Azure certifierad för katalogen för IoT-enhet][lnk-devices]
* [Stöd för fler protokoll][lnk-protocols]
* [Jämför med Händelsehubbar][lnk-compare]
* [Skalning, hög tillgänglighet och Katastrofåterställning][lnk-scaling]

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Utvecklarhandbok för IoT-hubb][lnk-devguide]
* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iotedge]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-sas-tokens]: iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-x509]: iot-hub-security-x509-get-started.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md

[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-twin-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-devguide-twin]: iot-hub-devguide-device-twins.md
[lnk-sdk-c-certs]: https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c
[lnk-digicert-root-certs]: https://www.digicert.com/digicert-root-certificates.htm
[lnk-paho]: https://pypi.python.org/pypi/paho-mqtt
