---
title: 'Hur du hanterar anslutningar och tillförlitlig meddelandehantering med hjälp av SDK: er för Azure IoT Hub-enheter'
description: 'Lär dig hur du kan förbättra din enhetsanslutning och meddelanden när du använder SDK: er för Azure IoT Hub-enheter'
services: iot-hub
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 9a07fa2010eef22c4d1477641d07dee70ab5a9cb
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227460"
---
# <a name="how-to-manage-connectivity-and-reliable-messaging-using-azure-iot-hub-device-sdks"></a>Hur du hanterar anslutningar och tillförlitlig meddelandehantering med hjälp av SDK: er för Azure IoT Hub-enheter

Den här guiden ger avancerad hjälp för att utforma elastiska enhetsprogram genom att utnyttja den anslutnings- och pålitliga meddelandefunktioner i SDK: er för Azure IoT-enheter. Målet med den här artikeln är att få svar på frågor och hantera dessa scenarier:

- hantera en avbruten nätverksanslutning
- Hantera växla mellan olika nätverksanslutningar
- Hantera återanslutning på grund av tillfälliga anslutningsfel för tjänsten

Implementeringsdetaljer kan variera beroende på språket, se länkade API-dokumentation eller specifika SDK för mer information.

- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)


## <a name="designing-for-resiliency"></a>Designa för elasticitet

IoT-enheter förlitar sig ofta i icke-kontinuerliga och/eller instabilt nätverk, till exempel GSM eller satellit. Dessutom när du interagerar med molnbaserade tjänster, kan det uppstå fel på grund av tillfälliga förhållanden, till exempel tillfällig tjänstens tillgänglighet och infrastrukturnivå fel (kallas ofta tillfälliga fel). Ett program som körs på en enhet behöver hantera anslutningen och återanslutning mekanismer, samt omprövningslogiken för skicka och ta emot meddelanden. Dessutom beror återförsök strategi kraven så mycket på IoT-scenario enheten deltar i, och enhetens kontext och funktioner.

SDK: er för Azure IoT Hub-enheter som syftar till att förenkla ansluter och kommunikation från moln till enhet och enheten till molnet genom att tillhandahålla en stabil och omfattande sätt att ansluta och skicka och ta emot meddelanden till och från Azure IoT Hub. Utvecklare kan också ändra befintliga implementering för att utveckla strategi för rätt återförsök för ett visst scenario.

Relevanta SDK-funktioner som har stöd för anslutning och tillförlitlig meddelandehantering beskrivs i följande avsnitt.

## <a name="connection-and-retry"></a>Anslutningen och försök igen

Det här avsnittet innehåller en översikt över de återanslutning och försök igen mönster som är tillgängliga när du hanterar anslutningar, Implementeringsguide för att använda annan återförsöksprincip i programmet på exempelenheten och relevanta API: er för enhetens SDK: er.

### <a name="error-patterns"></a>Fel-mönster
Anslutningsfel kan inträffa i många nivåer:

-  Nätverksfel, till exempel en frånkopplad socket och name resolution fel
- På protokollnivå fel för HTTP, AMQP och MQTT transport som är oberoende av länkar eller sessioner har upphört att gälla
- Programnivå fel som resulterar från antingen lokala misstag, till exempel ogiltiga autentiseringsuppgifter eller tjänsten beteende som överstiger kvoten eller begränsning

SDK: er för enheter identifiera fel i alla tre nivåer.  OS-relaterade fel och maskinvarufel är inte har identifierats och hanteras av SDK: er för enheter.  Designen baseras på [The tillfälliga fel hantering av vägledning](/azure/architecture/best-practices/transient-faults#general-guidelines) från Azure Architecture Center.

### <a name="retry-patterns"></a>Försök mönster

Den övergripande processen för återförsök när fel identifieras är: 
1. SDK: N identifierar felet och det tillhörande felet i nätverket, protokoll eller program.
2. SDK-använder felet filtrera för att bestämma om försök beroende på vilken fel, måste utföras.  Om en **oåterkalleligt fel** identifieras av SDK, operations (anslutning och skicka och ta emot) kommer att stoppas och meddelar SDK användaren. Ett oåterkalleligt fel är ett fel som SDK: N kan identifiera och bedömer att det inte återställas, till exempel, en autentisering eller felaktig endpoint-fel.
3. Om en **oåterkalleligt fel** har identifierat, SDK: N börjar att försöka använda återförsökspolicyn som anges tills en definierad tidsgräns går ut.
4. När definierade tidsgränsen upphör SDK stoppas försöker ansluta eller skicka, och meddelar användaren.
5.  SDK: N används att koppla ett återanrop för att ta emot status ändras. 

Tre återförsöksprinciper tillhandahålls:
- **Exponentiell backoff med jitter**: det här är standardprincipen för återförsök som tillämpas.  Den tenderar att vara aggressivt i början, långsammare och når en maximal fördröjning som inte överskrids.  Designen baseras på [försök vägledning från Azure Architecture Center](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific).
- **Anpassat återförsök**: du kan implementera en egen återförsöksprincip och mata in det i RetryPolicy beroende på vilket språk du valt. Du kan utforma en återförsöksprincip som är lämpligt för ditt scenario.  Detta är inte tillgänglig på C-SDK.
- **Inga återförsök**: det finns ett alternativ för att ange återförsöksprincipen för ”Inga återförsök”, vilket inaktiverar logik för omprövning.  SDK försöker ansluta en gång och skicka ett meddelande om en gång, förutsatt att anslutningen har upprättats. Den här principen används vanligtvis i fall där det finns bandbredd eller kostnaden frågor.   Om du väljer det här alternativet går förlorade meddelanden som inte vill skicka och kan inte återställas. 

### <a name="retry-policy-apis"></a>Återförsöksprincip API: er

   | SDK | SetRetryPolicy metod | Principimplementeringar | Implementeringsanvisningar |
   |-----|----------------------|--|--|
   |  IOS-C/Python  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Standard**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Anpassad:** Använd tillgängliga [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Inga återförsök:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C/Python/iOS-implementering](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._device_client_config.setretrypolicy?view=azure-java-stable)        | **Standard**: [ExponentialBackoffWithJitter-klass](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Anpassad:** implementera [RetryPolicy-gränssnittet](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Inga återförsök:** [NoRetry-klassen](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java-implementering](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |[.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_SetRetryPolicy_Microsoft_Azure_Devices_Client_IRetryPolicy) | **Standard**: [ExponentialBackoff-klass](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Anpassad:** implementera [IRetryPolicy gränssnitt](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Inga återförsök:** [NoRetry-klassen](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [C#-implementering](https://github.com/Azure/azure-iot-sdk-csharp) |
   | Node| [SetRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest#azure_iot_device_Client_setRetryPolicy) | **Standard**: [ExponentialBackoffWithJitter-klass](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Anpassad:** implementera [RetryPolicy-gränssnittet](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Inga återförsök:** [NoRetry-klassen](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Nod-implementering](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   

Nedan finns kodexempel som illustrerar det här flödet. 

#### <a name="net-implementation-guidance"></a>Vägledning för implementering av .NET

Kodexemplet nedan visar hur du definierar och ange standardprincipen för återförsök:

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

För att undvika hög CPU-användning, nya försök har begränsats om koden misslyckas omedelbart (till exempel när det finns ingen nätverk eller dirigera till mål) så att minimitiden som att köra nästa återförsök är 1 sekund. 

Om tjänsten svarar med en begränsning fel återförsöksprincipen skiljer sig och kan inte ändras via offentliga API: N:

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5));
   SetRetryPolicy(retryPolicy);
   ```

Mekanism för återförsök att stoppas efter `DefaultOperationTimeoutInMilliseconds`, vilket är för närvarande inställd på 4 minuter.

#### <a name="other-languages-implementation-guidance"></a>Implementeringsguide för andra språk
Granska implementering dokumentationen nedan för andra språk.  Exempel som visar användningen av återförsöksprincip API: er finns i databasen.
- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="next-steps"></a>Nästa steg
- [Använd SDK:er för enhet och tjänst](.\iot-hub-devguide-sdks.md)
- [Använd SDK för IoT-enhet för C](.\iot-hub-device-sdk-c-intro.md)
- [Utveckla för begränsade enheter](.\iot-hub-devguide-develop-for-constrained-devices.md)
- [Utveckla för mobila enheter](.\iot-hub-how-to-develop-for-mobile-devices.md)
- [Felsöka enhet kopplar från](iot-hub-troubleshoot-connectivity.md)
