---
title: Skicka säkerhets meddelanden till Azure Security Center för IoT | Microsoft Docs
description: Lär dig hur du skickar säkerhets meddelanden med hjälp av Azure Security Center för IoT.
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
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: b291e2392f0756cb2d3ec294db37206d32216959
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315932"
---
# <a name="send-security-messages-sdk"></a>Skicka Security-meddelanden SDK

I den här guiden beskrivs Azure Security Center för IoT-tjänstefunktioner när du väljer att samla in och skicka dina enhets säkerhets meddelanden utan att använda ett Azure Security Center för IoT-agenten och förklarar hur du gör det.  

I den här guiden får du lära du dig att: 
> [!div class="checklist"]
> * Skicka säkerhets meddelanden med Azure IoT C SDK
> * Skicka säkerhets meddelanden med Azure IoT C# SDK
> * Skicka säkerhets meddelanden med Azure IoT python SDK
> * Skicka säkerhets meddelanden med Azure IoT Node. js SDK
> * Skicka säkerhets meddelanden med Azure IoT Java SDK


## <a name="azure-security-center-for-iot-capabilities"></a>Azure Security Center för IoT-funktioner

Azure Security Center för IoT kan bearbeta och analysera alla typer av säkerhets meddelande data så länge data som skickas överensstämmer med [Azure Security Center för IoT-schemat](https://aka.ms/iot-security-schemas) och meddelandet anges som ett säkerhets meddelande.

## <a name="security-message"></a>Säkerhets meddelande

Azure Security Center for IoT definierar ett säkerhets meddelande med följande kriterier:
- Om meddelandet skickades med Azure IoT SDK
- Om meddelandet överensstämmer med [säkerhets meddelandets schema](https://aka.ms/iot-security-schemas)
- Om meddelandet angavs som ett säkerhets meddelande innan det skickas

Varje säkerhets meddelande innehåller metadata för avsändaren, till `AgentId`exempel `AgentVersion` `MessageSchemaVersion` , och en lista över säkerhets händelser.
Schemat definierar de giltiga och nödvändiga egenskaperna för säkerhets meddelandet, inklusive typer av händelser.

>[!Note]
> Meddelanden som skickas som inte följer schemat ignoreras. Se till att verifiera schemat innan du påbörjar sändning av data eftersom ignorerade meddelanden inte lagras i nuläget. 

>[!Note]
> Meddelanden som skickas som inte angavs som ett säkerhets meddelande med Azure IoT SDK dirigeras inte till Azure Security Center för IoT-pipeline.

## <a name="valid-message-example"></a>Exempel på giltigt meddelande

Exemplet nedan visar ett giltigt säkerhets meddelande objekt. Exemplet innehåller meddelandets metadata och en `ProcessCreate` säkerhets händelse.

När det har angetts som ett säkerhets meddelande och skickats, kommer det här meddelandet att bearbetas av Azure Security Center för IoT.

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

## <a name="send-security-messages"></a>Skicka säkerhets meddelanden 

Skicka säkerhets meddelanden *utan att* använda Azure Security Center för IoT-agenten med hjälp av [Azure IoT C-enhets-SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview): n, [Azure IoT C# -enhetens SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview), [Azure IoT Node. js SDK](https://github.com/Azure/azure-iot-sdk-node), [Azure IoT python SDK](https://github.com/Azure/azure-iot-sdk-python)eller [Azure IoT Java SDK ](https://github.com/Azure/azure-iot-sdk-java).

Om du vill skicka enhets data från dina enheter för bearbetning av Azure Security Center för IoT, använder du någon av följande API: er för att markera meddelanden för korrekt routning till Azure Security Center för IoT-bearbetnings pipelinen. 

Alla data som skickas, även om de markeras med rätt sidhuvud, måste också följa [Azure Security Center för IoT-meddelandets schema](https://aka.ms/iot-security-schemas). 

### <a name="send-security-message-api"></a>Skicka API för säkerhets meddelande 

API för att **Skicka Security-meddelanden** är för närvarande tillgängligt C#i C och python, Node. js och Java.  

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
#### <a name="nodejs-api"></a>Node. js-API

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

```python
async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_d2c_message(security_message)
    await device_client.disconnect()
```



## <a name="next-steps"></a>Nästa steg
- Läs [översikten över](overview.md) Azure Security Center för IoT-tjänsten
- Läs mer om Azure Security Center för IoT- [arkitektur](architecture.md)
- Aktivera [tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar](resources-frequently-asked-questions.md)
- Lär dig hur du kommer åt [rå säkerhets data](how-to-security-data-access.md)
- Förstå [rekommendationer](concept-recommendations.md)
- Förstå [aviseringar](concept-security-alerts.md)
