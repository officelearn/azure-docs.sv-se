---
title: Skicka din säkerhetsvarningar till ASC för förhandsversionen av IoT | Microsoft Docs
description: Lär dig hur du skickar meddelandena säkerhet med hjälp av ASC för IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.service: ascforiot
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 3daef0bfb2b9108e2b3c28ca0f6975a6f5768e92
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541591"
---
# <a name="tutorial-send-security-messages-sdk"></a>Självstudier: Skicka säkerhetsvarningar SDK

> [!IMPORTANT]
> ASC för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här självstudien beskriver ASC för analysmöjligheter för IoT-data när du väljer att samla in och skicka enheten säkerhetsvarningar utan att använda en ASC för IoT-agenten och förklarar hur du gör.  

I den här guiden får du lära dig att: 
> [!div class="checklist"]
> * Använda Skicka säkerhetsmeddelande API förC#
> * Använda Skicka säkerhetsmeddelande API för C

## <a name="asc-for-iot"></a>ASC för IoT 

ASC för IoT kan bearbeta och analysera säkerhetsdata för meddelande så länge data som skickas motsvarar den [ASC för IoT-schemat](https://github.com/Azure/ASC-for-IoT-Schemas). 

## <a name="send-security-messages"></a>Skicka meddelanden för säkerhet 

Skicka säkerhetsvarningar utan att använda ASC för IoT-agenten med hjälp av den [Azure IoT-enhetens SDK](https://github.com/Azure/azure-iot-sdk-csharp).

Använda en av följande API: er för att skicka enhetens data från dina enheter för bearbetning av ASC för IoT, för att markera meddelanden för rätt routning till ASC för IoT-process-pipelinen. Meddelanden som skickas på detta sätt ska bearbetas och visas som säkerhetsinformation i ASC för IoT inom både IoT Hub eller Azure Security Center. 

Alla data som skickas, även om markerats med rubriken rätt måste också vara kompatibel med den [ASC för IoT meddelande schemat](https://github.com/Azure/ASC-for-IoT-Schemas). 

> [!NOTE]
> Meddelandena som skickas som inte överensstämmer med schemat ignoreras. Se till att kontrollera schemat innan du påbörjar skickar data som inte lagras ignoreras meddelanden just nu. 

### <a name="send-security-message-api"></a>Skicka security meddelande API

Den **skicka säkerhetsvarningar** API är för närvarande tillgängligt i C och C#.  

#### <a name="c-api"></a>C#-API

```cs
private static async Task SendSecurityMessageAsync()
{
    string messageContent = "Security Data";
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