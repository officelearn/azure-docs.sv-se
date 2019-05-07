---
title: Skicka dina säkerhets-meddelanden till Azure Security Center för förhandsversionen av IoT | Microsoft Docs
description: Lär dig hur du skickar meddelandena säkerhet med hjälp av Azure Security Center för IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: a91a3538a9c176e3c76e351eb53eb84decc85938
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65200538"
---
# <a name="send-security-messages-sdk"></a>Skicka säkerhetsvarningar SDK

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I den här guiden beskriver Azure Security Center (ASC) för IoT-tjänstfunktioner när du väljer att samla in och skicka enheten säkerhetsvarningar utan att använda en ASC för IoT-agenten och förklarar hur du gör.  

I den här guiden får du lära du dig att: 
> [!div class="checklist"]
> * Använda Skicka säkerhetsmeddelande API förC#
> * Använda Skicka säkerhetsmeddelande API för C

## <a name="asc-for-iot-capabilities"></a>ASC för IoT-funktioner

ASC för IoT kan bearbeta och analysera säkerhetsdata för meddelande så länge data som skickas motsvarar den [ASC för IoT-schemat](https://aka.ms/iot-security-schemas) och meddelandet har angetts som ett säkerhetsmeddelande.

## <a name="security-message"></a>Säkerhetsmeddelande

ASC för IoT definierar ett säkerhetsmeddelande med hjälp av följande kriterier:
- Om meddelandet har skickats med Azure IoT C /C# SDK
- Om meddelandet överensstämmer med den [säkerhetsplanen för meddelande](https://aka.ms/iot-security-schemas)
- Om meddelandet har angetts som ett säkerhetsmeddelande innan de skickas

Metadata för avsändaren innehåller t.ex. varje säkerhetsmeddelande `AgentId`, `AgentVersion`, `MessageSchemaVersion` och en lista över säkerhetshändelser.
Schemat definierar giltiga och obligatoriska egenskaper för de säkerhetsvarningar, inklusive typer av händelser.

[!NOTE]
> Meddelandena som skickas som inte överensstämmer med schemat ignoreras. Se till att kontrollera schemat innan du påbörjar skickar data som inte lagras ignoreras meddelanden just nu. 
> Meddelandena som skickas som inte har angetts som ett säkerhetsmeddelande med hjälp av Azure IoT C /C# SDK inte ska dirigeras till ASC för IoT-pipeline

## <a name="valid-message-example"></a>Exempel på giltiga meddelande

Exemplet nedan visar ett meddelande för ett giltigt säkerhetsobjekt. Exemplet innehåller meddelandets metadata och en `ProcessCreate` säkerhetshändelse.

Ange en gång som ett säkerhetsmeddelande och har skickat det här meddelandet kommer att bearbetas av ASC för IoT.

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
                    "Executable": "/usr/bin/echo",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "nginx",
                    "CommandLine": "./backup .htaccess"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Skicka meddelanden för säkerhet 

Skicka säkerhetsvarningar utan att använda ASC för IoT-agenten med hjälp av den [Azure IoT C# enhets-SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) eller [Azure IoT C-enhets-SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview).

Använda en av följande API: er för att skicka enhetens data från dina enheter för bearbetning av ASC för IoT, för att markera meddelanden för rätt routning till ASC för IoT-process-pipelinen. Meddelanden som skickas på detta sätt ska bearbetas och visas som säkerhetsinformation i ASC för IoT inom både IoT Hub eller Azure Security Center. 

Alla data som skickas, även om markerats med rubriken rätt måste också vara kompatibel med den [ASC för IoT meddelande schemat](https://aka.ms/iot-security-schemas). 

### <a name="send-security-message-api"></a>Skicka security meddelande API

Den **skicka säkerhetsvarningar** API är för närvarande tillgängligt i C och C#.  

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

## <a name="next-steps"></a>Nästa steg
- Läsa ASC för IoT-tjänsten [översikt](overview.md)
- Läs mer om ASC för IoT [arkitektur](architecture.md)
- Aktivera den [service](quickstart-onboard-iot-hub.md)
- Läs den [vanliga frågor och svar](resources-frequently-asked-questions.md)
- Lär dig hur du kommer åt [raw säkerhetsdata](how-to-security-data-access.md)
- Förstå [rekommendationer](concept-recommendations.md)
- Förstå [aviseringar](concept-security-alerts.md)
