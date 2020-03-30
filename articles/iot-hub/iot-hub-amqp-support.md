---
title: Förstå Azure IoT Hub AMQP-stöd | Microsoft-dokument
description: Utvecklarguide – stöd för enheter som ansluter till IoT Hub-enhetsinriktade och tjänstinriktade slutpunkter med AMQP-protokollet. Innehåller information om inbyggt AMQP-stöd i Azure IoT-enhetenSDK:er.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.openlocfilehash: 7f7e957502419b766f7da63048e8168192ea20da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284790"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Kommunicera med din IoT-hubb med hjälp av AMQP-protokollet

Azure IoT Hub stöder [AMQP-version 1.0 (OASIS Advanced Message Queuing Protocol) för](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) att leverera en mängd olika funktioner via enhetsinriktade och tjänstinriktade slutpunkter. I det här dokumentet beskrivs användningen av AMQP-klienter för att ansluta till en IoT-hubb för att använda IoT Hub-funktioner.

## <a name="service-client"></a>Tjänstklient

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Ansluta och autentisera till en IoT-hubb (tjänstklient)

Om du vill ansluta till en IoT-hubb med HJÄLP AV AMQP kan en klient använda den [anspråksbaserade säkerhetsautentiseringen (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) eller [SASL-autentisering (Simple Authentication and Security Layer).](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)

Följande information krävs för serviceklienten:

| Information | Värde |
|-------------|--------------|
| Värdnamn för IoT-hubb | `<iot-hub-name>.azure-devices.net` |
| Nyckelnamn | `service` |
| Åtkomstnyckel | En primär eller sekundär nyckel som är associerad med tjänsten |
| Signatur för delad åtkomst | En kortvarig signatur för delad `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`åtkomst i följande format: . Information om hur du hämtar koden för att generera signaturen finns i [Kontrollera åtkomst till IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

Följande kodavsnitt använder [uAMQP-biblioteket i Python](https://github.com/Azure/azure-uamqp-python) för att ansluta till en IoT-hubb via en avsänarlänk.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Anropa meddelanden från molnet till enheten (tjänstklient)

Mer information om meddelandeutbytet mellan molnet till enheten mellan tjänsten och IoT-hubben och mellan enheten och IoT-hubben finns i [Skicka meddelanden från molnet till enheten från din IoT-hubb](iot-hub-devguide-messages-c2d.md). Tjänstklienten använder två länkar för att skicka meddelanden och ta emot feedback för tidigare skickade meddelanden från enheter, enligt beskrivningen i följande tabell:

| Skapad av | Länktyp | Länksökväg | Beskrivning |
|------------|-----------|-----------|-------------|
| Tjänst | Länk för avsändare | `/messages/devicebound` | Moln-till-enhet-meddelanden som är avsedda för enheter skickas till den här länken av tjänsten. Meddelanden som skickas via `To` den här länken har sin egenskap `/devices/<deviceID>/messages/devicebound`inställd på målenhetens mottagarlänksökväg . |
| Tjänst | Länk till mottagare | `/messages/serviceBound/feedback` | Meddelanden om slutförande, avslag och övergivande av feedback som kommer från enheter som tas emot på den här länken efter tjänst. Mer information om feedbackmeddelanden finns i [Skicka meddelanden från molnet till enheten från en IoT-hubb](./iot-hub-devguide-messages-c2d.md#message-feedback). |

Följande kodavsnitt visar hur du skapar ett meddelande från molnet till enheten och skickar det till en enhet med hjälp av [uAMQP-biblioteket i Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

För att få feedback skapar serviceklienten en mottagarlänk. Följande kodavsnitt visar hur du skapar en länk med hjälp av [uAMQP-biblioteket i Python:](https://github.com/Azure/azure-uamqp-python)

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
    print('received a message')
    # Check content_type in message property to identify feedback messages coming from device
    if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
        msg_body_raw = msg.get_data()
        msg_body_str = ''.join(msg_body_raw)
        msg_body = json.loads(msg_body_str)
        print(json.dumps(msg_body, indent=2))
        print('******************')
        for feedback in msg_body:
            print('feedback received')
            print('\tstatusCode: ' + str(feedback['statusCode']))
            print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
            print('\tdeviceId: ' + str(feedback['deviceId']))
            print
    else:
        print('unknown message:', msg.properties.content_type)
```

Som visas i föregående kod har ett feedbackmeddelande från molnet till enheten en innehållstyp av *application/vnd.microsoft.iothub.feedback.json*. Du kan använda egenskaperna i meddelandets JSON-brödtext för att dra slutsatsen leveransstatus för det ursprungliga meddelandet:

* Nyckeln `statusCode` i feedback-brödtexten har något av följande värden: *Framgång*, *Utgånget*, *DeliveryCountExceed*, *Avvisad*eller *Rensad*.

* Nyckeln `deviceId` i återkopplingskroppen har målenhetens ID.

* Nyckeln `originalMessageId` i feedback-brödtexten har ID:et för det ursprungliga molnet-till-enhet-meddelandet som skickades av tjänsten. Du kan använda den här leveransstatusen för att korrelera feedback till meddelanden från molnet till enheten.

### <a name="receive-telemetry-messages-service-client"></a>Ta emot telemetrimeddelanden (tjänstklient)

Som standard lagrar din IoT-hubb intade enhetstelemetrimeddelanden i en inbyggd händelsehubb. Din tjänstklient kan använda AMQP-protokollet för att ta emot de lagrade händelserna.

För detta ändamål måste tjänstklienten först ansluta till IoT-hubbslutpunkten och få en omdirigeringsadress till de inbyggda händelsehubbar. Tjänstklienten använder sedan den angivna adressen för att ansluta till den inbyggda händelsehubben.

I varje steg måste klienten presentera följande information:

* Giltiga tjänstautentiseringsuppgifter (tjänstsignaturtoken för delad åtkomst).

* En väl formaterad sökväg till konsumentgrupppartitionen som den avser att hämta meddelanden från. För en viss konsumentgrupp och partitions-ID `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` har sökvägen följande `$Default`format: (standardkonsumentgruppen är ).

* Ett valfritt filtreringspredikat för att ange en startpunkt i partitionen. Detta predikat kan vara i form av ett sekvensnummer, förskjutning eller enqueued tidsstämpel.

Följande kodavsnitt använder [uAMQP-biblioteket i Python](https://github.com/Azure/azure-uamqp-python) för att demonstrera föregående steg:

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI


def set_endpoint_filter(uri, endpoint_filter=''):
    source_uri = uamqp.address.Source(uri)
    source_uri.set_filter(endpoint_filter)
    return source_uri


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

För ett visst enhets-ID använder IoT-hubben en hash av enhets-ID för att avgöra vilken partition som ska lagras i. Det föregående kodavsnittet visar hur händelser tas emot från en sådan partition. Observera dock att ett vanligt program ofta måste hämta händelser som lagras i alla händelsehubbpartitioner.

## <a name="device-client"></a>Enhetsklient

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Ansluta och autentisera till en IoT-hubb (enhetsklient)

Om du vill ansluta till en IoT-hubb med HJÄLP AV AMQP kan en enhet använda [anspråksbaserad säkerhet (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) eller [SASL-autentisering (Simple Authentication and Security Layer).](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)

Följande information krävs för enhetsklienten:

| Information | Värde |
|-------------|--------------|
| Värdnamn för IoT-hubb | `<iot-hub-name>.azure-devices.net` |
| Åtkomstnyckel | En primär eller sekundär nyckel som är associerad med enheten |
| Signatur för delad åtkomst | En kortvarig signatur för delad `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`åtkomst i följande format: . Information om hur du hämtar koden för att generera signaturen finns i [Kontrollera åtkomst till IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

Följande kodavsnitt använder [uAMQP-biblioteket i Python](https://github.com/Azure/azure-uamqp-python) för att ansluta till en IoT-hubb via en avsänarlänk.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

Följande länksökvägar stöds som enhetsåtgärder:

| Skapad av | Länktyp | Länksökväg | Beskrivning |
|------------|-----------|-----------|-------------|
| Enheter | Länk till mottagare | `/devices/<deviceID>/messages/devicebound` | Meddelanden från molnet till enheten som är avsedda för enheter tas emot på den här länken av varje målenhet. |
| Enheter | Länk för avsändare | `/devices/<deviceID>/messages/events` | Meddelanden från enhet till moln som skickas från en enhet skickas via den här länken. |
| Enheter | Länk för avsändare | `/messages/serviceBound/feedback` | Feedback från molnet till enheten som skickas till tjänsten via den här länken av enheter. |

### <a name="receive-cloud-to-device-commands-device-client"></a>Ta emot kommandon från molnet till enheten (enhetsklient)

Moln-till-enhet-kommandon som skickas till enheter `/devices/<deviceID>/messages/devicebound` anländer på en länk. Enheter kan ta emot dessa meddelanden i batchar och använda meddelandedatanyttolasten, meddelandeegenskaperna, anteckningarna eller programegenskaperna i meddelandet efter behov.

Följande kodavsnitt använder [uAMQP-biblioteket i Python](https://github.com/Azure/azure-uamqp-python)) för att ta emot meddelanden från molnet till enheten med en enhet.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
    batch = receive_client.receive_message_batch(max_batch_size=5)
    for msg in batch:
        print('*** received a message ***')
        print(''.join(msg.get_data()))

        # Property 'to' is set to: '/devices/device1/messages/devicebound',
        print('\tto:                     ' + str(msg.properties.to))

        # Property 'message_id' is set to value provided by the service
        print('\tmessage_id:             ' + str(msg.properties.message_id))

        # Other properties are present if they were provided by the service
        print('\tcreation_time:          ' + str(msg.properties.creation_time))
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Skicka telemetrimeddelanden (enhetsklient)

Du kan också skicka telemetrimeddelanden från en enhet med hjälp av AMQP. Enheten kan också ange en ordlista med programegenskaper eller olika meddelandeegenskaper, till exempel meddelande-ID.

Följande kodavsnitt använder [uAMQP-biblioteket i Python](https://github.com/Azure/azure-uamqp-python) för att skicka meddelanden från en enhet till molnet från en enhet.

```python
# ...
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>Ytterligare information

* AMQP-anslutningarna kan störas på grund av ett nätverksfel eller autentiseringstokens utgång (genereras i koden). Serviceklienten måste hantera dessa omständigheter och återupprätta anslutningen och länkarna om det behövs. Om en autentiseringstoken upphör att gälla kan klienten undvika en anslutningssläpp genom att proaktivt förnya token innan den upphör att gälla.

* Din klient måste ibland kunna hantera länkomdirigeringar korrekt. För att förstå en sådan åtgärd, se din AMQP-klientdokumentation.

## <a name="next-steps"></a>Nästa steg

Mer information om AMQP-protokollet finns i [AMQP v1.0-specifikationen](https://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Mer information om IoT Hub-meddelanden finns i:

* [Meddelanden från molnet till enheten](./iot-hub-devguide-messages-c2d.md)
* [Stöd för tilläggsprotokoll](iot-hub-protocol-gateway.md)
* [Stöd för MQTT-protokollet (Message Queuing Telemetry Transport)](./iot-hub-mqtt-support.md)
