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
ms.openlocfilehash: 8951680ca9488dabffd02ee084e3f6827122276e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957460"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Hantering av anslutningar och tillförlitlig meddelandehantering med hjälp av SDK: er för Azure IoT Hub-enheter

Den här artikeln innehåller övergripande vägledning för hur du utformar enhetsprogram som är mer robust. Den visar hur du drar nytta av anslutningen och pålitliga meddelandefunktioner i SDK: er för Azure IoT-enheter. Målet med den här guiden är att hjälpa dig att hantera följande scenarier:

- Åtgärda en avbruten nätverksanslutning
- Växla mellan olika nätverksanslutningar
- Återansluter på grund av tillfälliga anslutningsfel för tjänsten

Mer information om implementeringen kan variera beroende på språket. Mer information finns i API-dokumentation eller specifika SDK:

- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="designing-for-resiliency"></a>Designa för elasticitet

IoT-enheter förlitar sig ofta i icke-kontinuerliga eller instabilt nätverk (till exempel GSM eller satellit). Fel kan inträffa när enheter interagerar med molnbaserade tjänster på grund av tillfälliga tjänstens tillgänglighet och infrastrukturnivå eller tillfälligt fel. Ett program som körs på en enhet måste hantera mekanismer för anslutning, återanslutning och logik för omprövning för att skicka och ta emot meddelanden. Återförsök strategi kraven beror också kraftigt på enhetens IoT-scenario, kontext, funktioner.

SDK: er för Azure IoT Hub-enheter som syftar till att förenkla ansluter och kommunikation från moln till enhet och enheten till molnet. Dessa SDK: er är ett robust sätt att ansluta till Azure IoT Hub och en omfattande uppsättning med alternativ för att skicka och ta emot meddelanden. Utvecklare kan också ändra befintliga implementering för att anpassa en bättre strategi för återförsök för ett visst scenario.

Relevanta SDK-funktioner som har stöd för anslutning och tillförlitlig meddelandehantering beskrivs i följande avsnitt.

## <a name="connection-and-retry"></a>Anslutningen och försök igen

Det här avsnittet ger en översikt över de återanslutning och försök igen mönster som är tillgängliga när du hanterar anslutningar. Den beskriver Implementeringsguide för att använda en annan återförsöksprincip i ditt enhetprogram och visar en lista över relevanta API: er från SDK: er för enheter.

### <a name="error-patterns"></a>Fel-mönster
Anslutningsfel kan bero på många nivåer:

- Nätverksfel: frånkopplad socket och name resolution-fel
- Protokollnivån fel för HTTP, AMQP och MQTT transport: är oberoende av länkar eller upphört att gälla sessioner
- Fel på programnivå som kommer från antingen lokala misstag: ogiltiga autentiseringsuppgifter eller tjänstbeteende (till exempel som överstiger kvoten eller begränsning)

SDK: er för enhetens upptäcka fel på alla tre nivåer. OS-relaterade fel och maskinvarufel är inte har identifierats och hanteras av SDK: er för enheter. SDK-designen baseras på [The tillfälliga fel hantering av vägledning](/azure/architecture/best-practices/transient-faults#general-guidelines) från Azure Architecture Center.

### <a name="retry-patterns"></a>Försök mönster

Följande steg beskriver hur återförsök när fel identifieras:

1. SDK: N identifierar felet och det tillhörande felet i nätverket, protokoll eller program.
1. SDK: N använder fel filtret för att fastställa feltypen av och bestämma om ett nytt försök krävs.
1. Om SDK: N identifierar en **oåterkalleligt fel**, åtgärder som anslutning, skicka och ta emot har stoppats. SDK meddelar användaren. Exempel på ett oåterkalleligt fel är ett autentiseringsfel och en felaktig endpoint-fel.
1. Om SDK: N identifierar en **oåterkalleligt fel**, ett nytt försök görs enligt dina angivna återförsöksprincipen tills den definierade tidsgränsen har gått ut måste.
1. När definierade tidsgränsen har nåtts, stannar SDK: N försöker ansluta eller skicka. Den meddelar användaren.
1. SDK: N används att koppla ett återanrop för att ta emot status ändras.

SDK: erna innehåller tre återförsöksprinciper:

- **Exponentiell backoff med jitter**: den här standardprincipen för återförsök tenderar att vara aggressivt i början och långsamt över tid tills den når en maximal fördröjning. Designen baseras på [försök vägledning från Azure Architecture Center](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific).
- **Anpassat återförsök**: för vissa SDK-språk, kan du utforma en egen återförsöksprincip som passar bättre för ditt scenario och mata in det i RetryPolicy. Anpassat återförsök är inte tillgängligt i C SDK.
- **Inga återförsök**: du kan ange återförsöksprincipen för ”Inga återförsök”, vilket inaktiverar logik för omprövning. SDK försöker ansluta en gång och skicka ett meddelande om en gång, förutsatt att anslutningen har upprättats. Den här principen används vanligtvis i scenarier med bandbredd eller kostnaden frågor. Om du väljer det här alternativet går förlorade meddelanden som inte vill skicka och kan inte återställas.

### <a name="retry-policy-apis"></a>Återförsöksprincip API: er

   | SDK | SetRetryPolicy metod | Principimplementeringar | Implementeringsanvisningar |
   |-----|----------------------|--|--|
   |  IOS-C/Python  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Standard**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Anpassad:** Använd tillgängliga [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Inga återförsök:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C/Python/iOS-implementering](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._device_client_config.setretrypolicy?view=azure-java-stable)        | **Standard**: [ExponentialBackoffWithJitter-klass](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Anpassad:** implementera [RetryPolicy-gränssnittet](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Inga återförsök:** [NoRetry-klassen](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java-implementering](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |[.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_SetRetryPolicy_Microsoft_Azure_Devices_Client_IRetryPolicy) | **Standard**: [ExponentialBackoff-klass](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Anpassad:** implementera [IRetryPolicy gränssnitt](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Inga återförsök:** [NoRetry-klassen](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [C#-implementering](https://github.com/Azure/azure-iot-sdk-csharp) |
   | Node| [SetRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest#azure_iot_device_Client_setRetryPolicy) | **Standard**: [ExponentialBackoffWithJitter-klass](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Anpassad:** implementera [RetryPolicy-gränssnittet](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Inga återförsök:** [NoRetry-klassen](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Nod-implementering](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |

Följande kodexempel visar det här flödet:

#### <a name="net-implementation-guidance"></a>Vägledning för implementering av .NET

Följande kodexempel visar hur du definierar och ange standardprincipen för återförsök:

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

För att undvika hög CPU-användning kan begränsas återförsöken om koden misslyckas omedelbart. Till exempel när det finns ingen nätverk eller vägen till målet. Minsta tid att köra nästa återförsök är 1 sekund.

Om tjänsten svarar med en begränsning fel, återförsöksprincipen är annorlunda och kan inte ändras via offentliga API: N:

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5));
   SetRetryPolicy(retryPolicy);
   ```

Mekanism för återförsök upphör efter `DefaultOperationTimeoutInMilliseconds`, vilket är för närvarande inställd på 4 minuter.

#### <a name="other-languages-implementation-guidance"></a>Implementeringsguide för andra språk

Granska följande implementering dokument för kodexempel på andra språk. Databasen innehåller exempel som demonstrerar användningen av återförsöksprincip API: er.

- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="next-steps"></a>Nästa steg
- [Använd SDK:er för enhet och tjänst](./iot-hub-devguide-sdks.md)
- [Använd SDK för IoT-enhet för C](./iot-hub-device-sdk-c-intro.md)
- [Utveckla för begränsade enheter](./iot-hub-devguide-develop-for-constrained-devices.md)
- [Utveckla för mobila enheter](./iot-hub-how-to-develop-for-mobile-devices.md)
- [Felsöka enhet kopplar från](iot-hub-troubleshoot-connectivity.md)
