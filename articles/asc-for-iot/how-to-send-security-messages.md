---
title: Skicka säkerhetsmeddelanden för enheter
description: Lär dig hur du skickar dina säkerhetsmeddelanden med Azure Security Center för IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 4877493982671b1b5db686715ef854f25c2966ea
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310981"
---
# <a name="send-security-messages-sdk"></a>Skicka säkerhetsmeddelanden SDK

I den här programguiden beskrivs Azure Security Center for IoT-tjänstfunktionerna när du väljer att samla in och skicka enhetssäkerhetsmeddelanden utan att använda en Azure Security Center för IoT-agent och förklarar hur du gör det.

I den här guiden får du lära du dig att:

> [!div class="checklist"]
> * Skicka säkerhetsmeddelanden med Azure IoT C SDK
> * Skicka säkerhetsmeddelanden med Azure IoT C# SDK
> * Skicka säkerhetsmeddelanden med Azure IoT Python SDK
> * Skicka säkerhetsmeddelanden med Azure IoT Node.js SDK
> * Skicka säkerhetsmeddelanden med Azure IoT Java SDK

## <a name="azure-security-center-for-iot-capabilities"></a>Azure Security Center för IoT-funktioner

Azure Security Center för IoT kan bearbeta och analysera alla typer av säkerhetsmeddelandedata så länge de data som skickas överensstämmer med [Azure Security Center för IoT-schema](https://aka.ms/iot-security-schemas) och meddelandet anges som ett säkerhetsmeddelande.

## <a name="security-message"></a>Säkerhetsmeddelande

Azure Security Center for IoT definierar ett säkerhetsmeddelande med följande villkor:

- Om meddelandet skickades med Azure IoT SDK
- Om meddelandet överensstämmer med [säkerhetsmeddelandeschemat](https://aka.ms/iot-security-schemas)
- Om meddelandet angavs som ett säkerhetsmeddelande innan

Varje säkerhetsmeddelande innehåller metadata för `AgentId`avsändaren, `AgentVersion` `MessageSchemaVersion` till exempel , och en lista över säkerhetshändelser.
Schemat definierar de giltiga och obligatoriska egenskaperna för säkerhetsmeddelandet, inklusive typer av händelser.

> [!NOTE]
> Meddelanden som skickas och som inte överensstämmer med schemat ignoreras. Kontrollera att schemat kontrolleras innan du börjar skicka data eftersom ignorerade meddelanden för närvarande inte lagras.

> [!NOTE]
> Meddelanden som skickades som inte har angetts som ett säkerhetsmeddelande med Azure IoT SDK dirigeras inte till Azure Security Center för IoT-pipeline.

## <a name="valid-message-example"></a>Giltigt meddelandeexempel

Exemplet nedan visar ett giltigt säkerhetsmeddelandeobjekt. Exemplet innehåller meddelandemetadata `ProcessCreate` och en säkerhetshändelse.

När det här meddelandet har angetts som ett säkerhetsmeddelande och skickats bearbetas det här meddelandet av Azure Security Center for IoT.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Skicka säkerhetsmeddelanden

Skicka säkerhetsmeddelanden *utan* att använda Azure Security Center för IoT-agent, med hjälp av [Azure IoT C-enheten SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview), Azure [IoT C#-enhet SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview), Azure [IoT Node.js SDK](https://github.com/Azure/azure-iot-sdk-node), [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python)eller [Azure IoT Java SDK](https://github.com/Azure/azure-iot-sdk-java).

Om du vill skicka enhetsdata från dina enheter för bearbetning av Azure Security Center för IoT använder du något av följande API:er för att markera meddelanden för korrekt routning till Azure Security Center för IoT-bearbetningspipeline.

Alla data som skickas, även om de markeras med rätt huvud, måste också följa [Azure Security Center för IoT-meddelandeschema](https://aka.ms/iot-security-schemas).

### <a name="send-security-message-api"></a>Api för säkerhetsmeddelande

**API:et för skicka säkerhetsmeddelanden** är för närvarande tillgängligt i C och C#, Python, Node.js och Java.

#### <a name="c-api"></a>C-API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {

    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;

    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);

    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }

    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }

    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }

cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }

    return success;
}

static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }

    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

#### <a name="c-api"></a>C#-API

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="nodejs-api"></a>Api för nod.js

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>Python API

Om du vill använda Python-API:et måste du installera paketet [azure-iot-device](https://pypi.org/project/azure-iot-device/).

När du använder Python-API:et kan du antingen skicka säkerhetsmeddelandet via modulen eller via enheten med hjälp av den unika anslutningssträngen för enheten eller modulen. När du använder följande Python-skriptexempel, med en enhet, använd **IoTHubDeviceClient**och använd **IoTHubModuleClient**med en modul .

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>Java-API

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```

## <a name="next-steps"></a>Nästa steg

- Läs [översikt](overview.md) över Azure Security Center for IoT-tjänsten
- Läs mer om Azure Security Center för [IoT-arkitektur](architecture.md)
- Aktivera [tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar](resources-frequently-asked-questions.md)
- Lär dig hur du kommer åt [rådata om säkerhet](how-to-security-data-access.md)
- Förstå [rekommendationer](concept-recommendations.md)
- Förstå [aviseringar](concept-security-alerts.md)
