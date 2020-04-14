---
title: Förstå Azure IoT Hub MQTT-stöd | Microsoft-dokument
description: Utvecklarguide – stöd för enheter som ansluter till en IoT Hub-enhetsvänd slutpunkt med MQTT-protokollet. Innehåller information om inbyggt MQTT-stöd i Azure IoT-enhetenSDK:er.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.openlocfilehash: 9ccfaa57b8e8fdea325bed908ffe8815b09d0d15
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257801"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Kommunicera med din IoT-hubb med MQTT-protokollet

IoT Hub gör det möjligt för enheter att kommunicera med slutpunkterna för IoT Hub-enheten med:

* [MQTT v3.1.1](https://mqtt.org/) på port 8883
* MQTT v3.1.1 över WebSocket på port 443.

IoT Hub är inte en fullständig MQTT-koordinator och stöder inte alla beteenden som anges i MQTT v3.1.1-standarden. I den här artikeln beskrivs hur enheter kan använda MQTT-beteenden som stöds för att kommunicera med IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

All enhetskommunikation med IoT Hub måste vara säkrad med TLS/SSL. Därför stöder IoT Hub inte icke-säkra anslutningar via port 1883.

## <a name="connecting-to-iot-hub"></a>Ansluta till IoT Hub

En enhet kan använda MQTT-protokollet för att ansluta till en IoT-hubb med något av följande alternativ.

* Bibliotek i [Azure IoT SDK:er](https://github.com/Azure/azure-iot-sdks).
* MQTT-protokollet direkt.

MQTT-porten (8883) är blockerad i många företags- och utbildningsnätverksmiljöer. Om du inte kan öppna port 8883 i brandväggen rekommenderar vi att du använder MQTT via webbuttag. MQTT över Web Sockets kommunicerar över port 443, som nästan alltid är öppen i nätverksmiljöer. Mer information om hur du anger MQTT- och MQTT-protokollen över webbuttag när du använder Azure IoT SDK:er finns [i Använda enhetens SDK:er](#using-the-device-sdks).

## <a name="using-the-device-sdks"></a>Använda enhetenSDK:er

[EnhetSDK:er](https://github.com/Azure/azure-iot-sdks) som stöder MQTT-protokollet är tillgängliga för Java, Node.js, C, C#och Python. Enhetens SDK:er använder standard-IoT Hub-anslutningssträngen för att upprätta en anslutning till en IoT-hubb. Om du vill använda MQTT-protokollet måste klientprotokollparametern anges till **MQTT**. Du kan också ange MQTT över webbuttag i parametern klientprotokoll. Som standard ansluter enhetens SDK:er till en IoT-hubb med **CleanSession-flaggan** inställd **på 0** och använder **QoS 1** för meddelandeutbyte med IoT-hubben.

När en enhet är ansluten till en IoT-hubb innehåller enhetens SDK-enheter metoder som gör det möjligt för enheten att utbyta meddelanden med en IoT-hubb.

Följande tabell innehåller länkar till kodexempel för varje språk som stöds och anger vilken parameter som ska användas för att upprätta en anslutning till IoT Hub med MQTT- eller MQTT-protokollet över webbuttag.

| Språk | Parameter för MQTT-protokoll | MQTT över protokollparameter för webbuttag
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | azure-iot-device-mqtt. Mqtt (på) | azure-iot-device-mqtt. MqttWs ( |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol?view=azure-java-stable). MQTT (på andra sätt) | IotHubClientProtocol.MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C #](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [TransportType](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.transporttype?view=azure-dotnet). Mqtt (på) | TransportType.Mqtt faller tillbaka till MQTT över web sockets om MQTT misslyckas. Om du bara vill ange MQTT över webbuttag använder du TransportType.Mqtt_WebSocket_Only |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | Stöder MQTT som standard | Lägg `websockets=True` till i anropet för att skapa klienten |

Följande fragment visar hur du anger MQTT över Web Sockets-protokollet när du använder Azure IoT Node.js SDK:

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

Följande fragment visar hur du anger MQTT över Web Sockets-protokollet när du använder Azure IoT Python SDK:

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>Standardtidsgränsen för keep-alive

För att säkerställa att en klient/IoT Hub-anslutning förblir vid liv skickar både tjänsten och klienten regelbundet en *keep-alive-ping* till varandra. Klienten som använder IoT SDK skickar ett levande liv med det intervall som definieras i den här tabellen nedan:

|Språk  |Standardintervall för keep-alive  |Konfigurerbar  |
|---------|---------|---------|
|Node.js     |   180 sekunder      |     Inga    |
|Java     |    230 sekunder     |     Inga    |
|C     | 240 sekunder |  [Ja](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 sekunder |  [Ja](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python (V2)   | 60 sekunder |  Inga   |

Efter [MQTT-spec](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)är IoT Hubs pingintervall för keep-alive 1,5 gånger klientens keep-alive-värde. IoT Hub begränsar dock den maximala tidsgränsen för serversidan till 29,45 minuter (1767 sekunder) eftersom alla Azure-tjänster är bundna till azure load balancer TCP-tidsgränsen för inaktiv, vilket är 29,45 minuter. 

En enhet som använder Java SDK skickar till exempel den keep-alive pingen och förlorar sedan nätverksanslutningen. 230 sekunder senare missar enheten den livhållna pingen eftersom den är offline. IoT Hub stänger dock inte anslutningen omedelbart - `(230 * 1.5) - 230 = 115` den väntar ytterligare en sekund innan du kopplar från enheten med felet [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md). 

Det maximala klientädviktsvärdet `1767 / 1.5 = 1177` som du kan ställa in är sekunder. All trafik återställer livsföringen. En lyckad SAS-tokenuppdatering återställer till exempel keep-alive.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrera en enhetsapp från AMQP till MQTT

Om du använder [enhetenSDK:er](https://github.com/Azure/azure-iot-sdks)måste byte från att använda AMQP till MQTT ändra protokollparametern i klientiniteringen som tidigare nämnts.

När du gör det, se till att kontrollera följande objekt:

* AMQP returnerar fel för många villkor, medan MQTT avslutar anslutningen. Därför kan undantagshanteringslogiken kräva vissa ändringar.

* MQTT stöder inte *avvisningsåtgärder* när du tar emot [meddelanden från molnet till enheten](iot-hub-devguide-messaging.md). Om backend-appen behöver få svar från enhetsappen kan du överväga att använda [direktmetoder](iot-hub-devguide-direct-methods.md).

* AMQP stöds inte i Python SDK

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Använda MQTT-protokollet direkt (som en enhet)

Om en enhet inte kan använda enhetens SDK:er kan den fortfarande ansluta till de offentliga enhetsslutpunkterna med MQTT-protokollet på port 8883. I **CONNECT-paketet** bör enheten använda följande värden:

* Använd **deviceId**för fältet **ClientId** .

* Använd , där `{iothubhostname}` `{iothubhostname}/{device_id}/?api-version=2018-06-30`är det fullständiga CName för IoT-hubben för fältet **Användarnamn.**

    Om namnet på din IoT-hubb **till** exempel är contoso.azure-devices.net och om namnet på enheten är **MyDevice01**ska det fullständiga **fältet Användarnamn** innehålla:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* Använd en SAS-token för fältet **Lösenord.** Formatet för SAS-token är detsamma som för både HTTPS- och AMQP-protokollen:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Om du använder X.509-certifikatautentisering krävs inte SAS-tokenlösenord. Mer information finns [i Konfigurera X.509-säkerhet i din Azure IoT Hub](iot-hub-security-x509-get-started.md) och följ kodinstruktionerna [nedan](#tlsssl-configuration).

  Mer information om hur du genererar SAS-token finns i enhetsavsnittet [i Använda IoT Hub-säkerhetstoken](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app).

  När du testar kan du också använda [Azure IoT Tools för Visual Studio-kod](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) över flera plattformar eller kommandot CLI-tillägg az [iot hub generate-sas-token](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token) för att snabbt generera en SAS-token som du kan kopiera och klistra in i din egen kod:

### <a name="for-azure-iot-tools"></a>För Azure IoT-verktyg

1. Expandera fliken **AZURE IOT HUB DEVICES** längst ned till vänster i Visual Studio Code.
  
2. Högerklicka på enheten och välj **Generera SAS-token för enhet**.
  
3. Ställ in **utgångstiden** och tryck på "Retur".
  
4. SAS-token skapas och kopieras till Urklipp.

   SAS-token som genereras har följande struktur:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Den del av den här token som ska användas som **fältet Lösenord** för att ansluta med MQTT är:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

För MQTT-anslutnings- och frånkopplingspaket utfärdar IoT Hub en händelse på **operationsövervakningskanalen.** Den här händelsen innehåller ytterligare information som kan hjälpa dig att felsöka anslutningsproblem.

Enhetsappen kan ange ett **Ska-meddelande** i **CONNECT-paketet.** Enhetsappen `devices/{device_id}/messages/events/` ska `devices/{device_id}/messages/events/{property_bag}` använda eller som **r-ämnesnamn för** att definiera **Will-meddelanden** som ska vidarebefordras som ett telemetrimeddelande. I det här fallet, om nätverksanslutningen är stängd, men ett **DISCONNECT-paket** inte tidigare tagits emot från enheten, skickar IoT Hub **Meddelandet Will** som levereras i **CONNECT-paketet** till telemetrikanalen. Telemetrikanalen kan vara antingen standardslutpunkten **Händelser** eller en anpassad slutpunkt som definieras av IoT Hub-routning. Meddelandet har egenskapen **iothub-MessageType** med värdet **Will** tilldelad.

### <a name="an-example-of-c-code-using-mqtt-without-azure-iot-c-sdk"></a>Ett exempel på C-kod med MQTT utan Azure IoT C SDK
I den här [databasen](https://github.com/Azure-Samples/IoTMQTTSample)hittar du ett par C/C++-demoprojekt som visar hur du skickar telemetrimeddelanden, tar emot händelser med en IoT-hubb utan att använda Azure IoT C SDK. 

Dessa exempel använder Eclipse Mosquitto-biblioteket för att skicka meddelande till MQTT Broker som implementerats i IoT-hubben.

Den här databasen innehåller:

**För Windows:**

* TelemetriMQTTWin32: innehåller kod för att skicka ett telemetrimeddelande till en Azure IoT-hubb som är byggd och körs på en Windows-dator.

* SubscribeMQTTWin32: innehåller kod för att prenumerera på händelser i en viss IoT-hubb på en Windows-dator.

* DeviceTwinMQTTWin32: innehåller kod för att fråga och prenumerera på enhetstvillinghändelserna för en enhet i Azure IoT-hubben på en Windows-dator.

* PnPMQTTWin32: innehåller kod för att skicka ett telemetrimeddelande med IoT Plug & Play preview Device-funktioner till en Azure IoT-hubb som är byggd och körs på en Windows-dator. Mer om IoT Plug & Spela [här](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play)

**För Linux:**

* MQTTLinux: innehåller kod och byggskript som ska köras på Linux (WSL, Ubuntu och Raspbian har testats hittills).

* LinuxConsoleVS2019: innehåller samma kod men i ett VS2019-projekt som riktar sig till WSL (Windows Linux-undersystem). Med det här projektet kan du felsöka koden som körs på Linux steg för steg från Visual Studio.

**För mosquitto_pub:**

Den här mappen innehåller två exempelkommandon som används med mosquitto_pub verktyg som tillhandahålls av Mosquitto.org.

* Mosquitto_sendmessage: för att skicka ett enkelt textmeddelande till en Azure IoT-hubb som fungerar som en enhet.

* Mosquitto_subscribe: om du vill se händelser som inträffar i en Azure IoT-hubb.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Använda MQTT-protokollet direkt (som en modul)

Att ansluta till IoT Hub över MQTT med hjälp av en modulidentitet liknar enheten (beskrivs [ovan)](#using-the-mqtt-protocol-directly-as-a-device)men du måste använda följande:

* Ange klient-ID:et till `{device_id}/{module_id}`.

* Om du autentiserar med användarnamn `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` och lösenord ställer du in användarnamnet på och använder SAS-token som är associerad med modulidentiteten som lösenord.

* Används `devices/{device_id}/modules/{module_id}/messages/events/` som ämne för publicering av telemetri.

* Använd `devices/{device_id}/modules/{module_id}/messages/events/` som KOMMER-ämne.

* De dubbla GET- och PATCH-ämnena är identiska för moduler och enheter.

* Det dubbla statusämnet är identiskt för moduler och enheter.

## <a name="tlsssl-configuration"></a>TLS/SSL-konfiguration

Om du vill använda MQTT-protokollet direkt *måste* klienten ansluta via TLS/SSL. Försök att hoppa över det här steget misslyckas med anslutningsfel.

För att upprätta en TLS-anslutning kan du behöva hämta och referera till DigiCert Baltimore Root Certificate. Det här certifikatet är det som Azure använder för att skydda anslutningen. Du hittar det här certifikatet i [Azure-iot-sdk-c-databasen.](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) Mer information om dessa certifikat finns på [Digicerts webbplats](https://www.digicert.com/digicert-root-certificates.htm).

Ett exempel på hur du implementerar detta med python-versionen av [Paho MQTT-biblioteket](https://pypi.python.org/pypi/paho-mqtt) av Eclipse Foundation kan se ut så här.

Installera först Paho-biblioteket från kommandoradsmiljön:

```cmd/sh
pip install paho-mqtt
```

Implementera sedan klienten i ett Python-skript. Ersätt platshållarna enligt följande:

* `<local path to digicert.cer>`är sökvägen till en lokal fil som innehåller DigiCert Baltimore Root-certifikatet. Du kan skapa den här filen genom att kopiera certifikatinformationen från [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) i `-----BEGIN CERTIFICATE-----` Azure `-----END CERTIFICATE-----`IoT SDK för C. Inkludera raderna och ta bort markeringarna `"` i början och slutet av varje rad och ta bort `\r\n` tecknen i slutet av varje rad.

* `<device id from device registry>`är ID för en enhet som du har lagt till i din IoT-hubb.

* `<generated SAS token>`är en SAS-token för den enhet som skapats enligt beskrivningen tidigare i den här artikeln.

* `<iot hub name>`namnet på din IoT-hubb.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer file>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

Om du vill autentisera med hjälp av ett enhetscertifikat uppdaterar du kodavsnittet ovan med följande ändringar (se [Så här skaffar du ett X.509 CA-certifikat](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) om hur du förbereder för certifikatbaserad autentisering):

```python
# Create the client as before
# ...

# Set the username but not the password on your client
client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=None)

# Set the certificate and key paths on your client
cert_file = "<local path to your certificate file>"
key_file = "<local path to your device key file>"
client.tls_set(ca_certs=path_to_root_cert, certfile=cert_file, keyfile=key_file,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>Skicka meddelanden från enhet till moln

När du har gjort en lyckad anslutning kan en `devices/{device_id}/messages/events/` `devices/{device_id}/messages/events/{property_bag}` enhet skicka meddelanden till IoT Hub med eller som **ämnesnamn**. Elementet `{property_bag}` gör det möjligt för enheten att skicka meddelanden med ytterligare egenskaper i ett url-kodat format. Ett exempel:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Det `{property_bag}` här elementet använder samma kodning som frågesträngar i HTTPS-protokollet.

Följande är en lista över implementeringsspecifika IoT Hub-beteenden:

* IoT Hub stöder inte QoS 2-meddelanden. Om en enhetsapp publicerar ett meddelande med **QoS 2**stänger IoT Hub nätverksanslutningen.

* IoT Hub kvarstår inte Behåll meddelanden. Om en enhet skickar ett meddelande med flaggan **BEHÅLL** inställd på 1 läggs egenskapen **x-opt-retain** till meddelandet. I det här fallet, i stället för att spara behålla meddelandet, skickar IoT Hub det till serverd-appen.

* IoT Hub stöder bara en aktiv MQTT-anslutning per enhet. Alla nya MQTT-anslutningar för samma enhets-ID gör att IoT Hub släpper den befintliga anslutningen.

Mer information finns i [Guiden För meddelandeutvecklare](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Ta emot meddelanden från molnet till enheten

Om du vill ta emot meddelanden från IoT Hub bör en enhet prenumerera med hjälp av `devices/{device_id}/messages/devicebound/#` som **ämnesfilter**. Jokertecknet på flera `#` nivåer i ämnesfiltret används bara för att enheten ska kunna ta emot ytterligare egenskaper i ämnesnamnet. IoT Hub tillåter inte användning `#` `?` av eller jokertecken för filtrering av underämnen. Eftersom IoT Hub inte är en allmän pub-sub meddelandemäklare, stöder den bara de dokumenterade ämnesnamnen och ämnesfiltren.

Enheten får inga meddelanden från IoT Hub förrän den har prenumererat på sin enhetsspecifika `devices/{device_id}/messages/devicebound/#` slutpunkt, representerad av ämnesfiltret. När en prenumeration har upprättats tar enheten emot meddelanden från molnet till enheten som skickades till den efter prenumerationen. Om enheten ansluter med **CleanSession-flaggan** inställd **på 0**sparas prenumerationen över olika sessioner. I det här fallet tas alla utestående meddelanden som skickas till den nästa gång enheten ansluts till **CleanSession 0** när den kopplas från. Om enheten använder **CleanSession-flaggan** inställd på **1** får den dock inga meddelanden från IoT Hub förrän den prenumererar på dess enhetsslutpunkt.

IoT Hub levererar meddelanden med **ämnesnamnet** `devices/{device_id}/messages/devicebound/`eller `devices/{device_id}/messages/devicebound/{property_bag}` när det finns meddelandeegenskaper. `{property_bag}`innehåller url-kodade nyckel-/värdepar av meddelandeegenskaper. Endast programegenskaper och användarinställningarbara systemegenskaper (till exempel **messageId** eller **correlationId)** ingår i egenskapspåsen. Systemegenskapsnamn **$** har prefixet, programegenskaper använder det ursprungliga egenskapsnamnet utan prefix.

När en enhetsapp prenumererar på ett ämne med **QoS 2**beviljar IoT Hub maximal QoS-nivå 1 i **SUBACK-paketet.** Därefter levererar IoT Hub meddelanden till enheten med QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>Hämta egenskaperna för en enhetstvilling

Först prenumererar en `$iothub/twin/res/#`enhet på , för att ta emot åtgärdens svar. Sedan skickas ett tomt meddelande `$iothub/twin/GET/?$rid={request id}`till ämnet , med ett ifyllt värde för **begäran-ID**. Tjänsten skickar sedan ett svarsmeddelande som `$iothub/twin/res/{status}/?$rid={request id}`innehåller enhetstvillingdata i ämnet , med samma **begärande-ID** som begäran.

Begärande-ID kan vara valfritt giltigt värde för ett meddelandeegenskapsvärde, enligt [IoT Hub messaging developer's guide](iot-hub-devguide-messaging.md), och status valideras som ett heltal.

Svarstexten innehåller egenskapsdelen av enhetstvillingen, som visas i följande svarsexempel:

```json
{
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
```

Möjliga statuskoder är:

|Status | Beskrivning |
| ----- | ----------- |
| 200 | Lyckades |
| 429 | För många begäranden (begränsat), enligt [IoT Hub-begränsning](iot-hub-devguide-quotas-throttling.md) |
| 5** | Serverfel |

Mer information finns i [utvecklarguide för enhetstvillingar](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>Uppdatera enhetstvillingens rapporterade egenskaper

Om du vill uppdatera rapporterade egenskaper utfärdar enheten en begäran till IoT Hub via en publikation över ett angivet MQTT-ämne. När begäran har bearbetats svarar IoT Hub på uppdateringsåtgärdens lyckade eller misslyckade status via en publikation till ett annat ämne. Det här avsnittet kan prenumereras av enheten för att meddela det om resultatet av sin dubbla uppdateringsbegäran. För att implementera den här typen av interaktion med begäran/svar i`$rid`MQTT utnyttjar vi begreppet begärande-ID ( ) som ursprungligen tillhandahölls av enheten i dess uppdateringsbegäran. Det här begärande-ID:et ingår också i svaret från IoT Hub så att enheten kan korrelera svaret på sin specifika tidigare begäran.

I följande sekvens beskrivs hur en enhet uppdaterar de rapporterade egenskaperna i enhetstvillingen i IoT Hub:

1. En enhet måste först `$iothub/twin/res/#` prenumerera på ämnet för att få åtgärdens svar från IoT Hub.

2. En enhet skickar ett meddelande som innehåller `$iothub/twin/PATCH/properties/reported/?$rid={request id}` enhetstvillinguppdateringen till ämnet. Det här meddelandet innehåller ett **id-värde för begäran.**

3. Tjänsten skickar sedan ett svarsmeddelande som innehåller det nya ETag-värdet för den rapporterade egenskapssamlingen i ämnet `$iothub/twin/res/{status}/?$rid={request id}`. Det här svarsmeddelandet använder samma **begärande-ID** som begäran.

Meddelandetexten för begäran innehåller ett JSON-dokument som innehåller nya värden för rapporterade egenskaper. Varje medlem i JSON-dokumentet uppdateras eller lägg till motsvarande medlem i enhetstvillingens dokument. En medlemsuppsättning till `null`, tar bort medlemmen från det innehållande objektet. Ett exempel:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Möjliga statuskoder är:

|Status | Beskrivning |
| ----- | ----------- |
| 204 | Lyckad (inget innehåll returneras) |
| 400 | Felaktig begäran. Missbildad JSON |
| 429 | För många begäranden (begränsat), enligt [IoT Hub-begränsning](iot-hub-devguide-quotas-throttling.md) |
| 5** | Serverfel |

Python-kodavsnittet nedan visar den dubbla rapporterade egenskaper uppdateringsprocessen över MQTT (med Paho MQTT klient):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

När dubbla rapporterade egenskaper uppdatering operation ovan, publikationsmeddelandet från IoT Hub kommer att ha följande ämne: `$iothub/twin/res/204/?$rid=1&$version=6`, var `204` är statuskoden som anger framgång, `$rid=1` motsvarar begäran ID som tillhandahålls av enheten i koden, och `$version` motsvarar den version av rapporterade egenskaper avsnitt av enhet tvillingar efter uppdateringen.

Mer information finns i [utvecklarguide för enhetstvillingar](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>Ta emot önskade egenskaper uppdateringsmeddelanden

När en enhet är ansluten skickar IoT Hub meddelanden till ämnet `$iothub/twin/PATCH/properties/desired/?$version={new version}`, som innehåller innehållet i uppdateringen som utförs av lösningens backend. Ett exempel:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

När det gäller `null` egenskapsuppdateringar innebär värden att JSON-objektmedlemmen tas bort. Observera också `$version` att anger den nya versionen av den önskade egenskapsdelen av tvillingen.

> [!IMPORTANT]
> IoT Hub genererar ändringsmeddelanden endast när enheter är anslutna. Se till att implementera [enhetens återanslutningsflöde](iot-hub-devguide-device-twins.md#device-reconnection-flow) för att hålla önskade egenskaper synkroniserade mellan IoT Hub och enhetsappen.

Mer information finns i [utvecklarguide för enhetstvillingar](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Svara på en direkt metod

Först måste en enhet `$iothub/methods/POST/#`prenumerera på . IoT Hub skickar metodbegäranden till ämnet `$iothub/methods/POST/{method name}/?$rid={request id}`, med antingen en giltig JSON eller en tom brödtext.

För att svara skickar enheten ett meddelande med en giltig `$iothub/methods/res/{status}/?$rid={request id}`JSON eller tom brödtext till ämnet . I det här meddelandet måste **begärande-ID** matcha det i meddelandet om begäran och **statusen** måste vara ett heltal.

Mer information finns i [Direkt metodutvecklarens guide](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Annat som är bra att tänka på

Om du behöver anpassa MQTT-protokollbeteendet på molnsidan bör du som en sista faktor granska [Azure IoT-protokollgatewayen](iot-hub-protocol-gateway.md). Med den här programvaran kan du distribuera en högpresterande anpassad protokollgateway som samverkar direkt med IoT Hub. Azure IoT-protokollgatewayen gör att du kan anpassa enhetsprotokollet så att det passar MQTT-distributioner på brownfield eller andra anpassade protokoll. Den här metoden kräver dock att du kör och använder en anpassad protokollgateway.

## <a name="next-steps"></a>Nästa steg

Mer information om MQTT-protokollet finns i [MQTT-dokumentationen](https://mqtt.org/documentation).

Mer information om hur du planerar distributionen av IoT Hub finns i:

* [Azure Certified for IoT-enhetskatalog](https://catalog.azureiotsolutions.com/)
* [Stöd ytterligare protokoll](iot-hub-protocol-gateway.md)
* [Jämför med händelsehubbar](iot-hub-compare-event-hubs.md)
* [Skalning, HA och DR](iot-hub-scaling.md)

Mer information om hur du utforskar funktionerna i IoT Hub finns i:

* [Utvecklarhandledning för IoT Hub](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
