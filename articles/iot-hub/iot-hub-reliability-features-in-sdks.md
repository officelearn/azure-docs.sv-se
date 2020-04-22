---
title: Hantera IoT Hub-anslutning & tillförlitliga meddelanden med SDK:er för enheter
description: Lär dig hur du förbättrar enhetens anslutning och meddelanden när du använder SD:erna för Azure IoT Hub-enheten
services: iot-hub
author: robinsh
ms.author: robinsh
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: a645ab1fa4a1be3ec8e939452a1457e84bbafe15
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759673"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Hantera anslutning och tillförlitliga meddelanden med hjälp av Azure IoT Hub-enhet SDK:er

Den här artikeln innehåller vägledning på hög nivå som hjälper dig att utforma enhetsprogram som är mer motståndskraftiga. Den visar hur du drar nytta av anslutnings- och tillförlitliga meddelandefunktioner i Azure IoT-enhetSDK:er. Syftet med den här guiden är att hjälpa dig att hantera följande scenarier:

* Åtgärda en bortsläppt nätverksanslutning

* Växla mellan olika nätverksanslutningar

* Återanslut på grund av tillfälliga anslutningsfel för tjänsten

Implementeringsinformationen kan variera beroende på språk. Mer information finns i API-dokumentationen eller specifika SDK:

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [SDK för Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python) (Tillförlitlighet ännu inte implementerad)

## <a name="designing-for-resiliency"></a>Designa för elasticitet

IoT-enheter förlitar sig ofta på icke-kontinuerliga eller instabila nätverksanslutningar (till exempel GSM eller satellit). Fel kan uppstå när enheter interagerar med molnbaserade tjänster på grund av intermittent tjänsttillgänglighet och infrastrukturnivå eller tillfälliga fel. Ett program som körs på en enhet måste hantera mekanismerna för anslutning, återanslutning och logiken för återförsök för att skicka och ta emot meddelanden. Dessutom är kraven för återförsöksstrategi starkt beroende av enhetens IoT-scenario, kontext, funktioner.

Azure IoT Hub-enheten SDK:er syftar till att förenkla anslutning och kommunikation från molnet till enheten och enhet till moln. Dessa SDK:er är ett robust sätt att ansluta till Azure IoT Hub och en omfattande uppsättning alternativ för att skicka och ta emot meddelanden. Utvecklare kan också ändra befintlig implementering för att anpassa en bättre strategi för återförsök för ett visst scenario.

De relevanta SDK-funktionerna som stöder anslutning och tillförlitliga meddelanden beskrivs i följande avsnitt.

## <a name="connection-and-retry"></a>Anslutning och försök igen

Det här avsnittet innehåller en översikt över de återanslutnings- och återförsöksmönster som är tillgängliga när du hanterar anslutningar. Den beskriver implementeringsvägledning för att använda en annan återförsöksprincip i enhetsprogrammet och listar relevanta API:er från enhetens SDK:er.

### <a name="error-patterns"></a>Felmönster

Anslutningsfel kan inträffa på många nivåer:

* Nätverksfel: frånkopplade socket- och namnmatchningsfel

* Fel på protokollnivå för HTTP-, AMQP- och MQTT-transport: fristående länkar eller utgångna sessioner

* Fel på programnivå som beror på lokala misstag: ogiltiga autentiseringsuppgifter eller tjänstbeteende (till exempel överskrider kvoten eller begränsningen)

EnhetenSDK:er upptäcker fel på alla tre nivåer. OS-relaterade fel och maskinvarufel identifieras inte och hanteras inte av enhetens SDK:er. SDK-designen baseras på [vägledningen för tillfällig felhantering](/azure/architecture/best-practices/transient-faults#general-guidelines) från Azure Architecture Center.

### <a name="retry-patterns"></a>Återförsöksmönster

I följande steg beskrivs återförsöksprocessen när anslutningsfel upptäcks:

1. SDK identifierar felet och det associerade felet i nätverket, protokollet eller programmet.

2. SDK använder felfiltret för att fastställa feltypen och avgöra om ett nytt försök behövs.

3. Om SDK identifierar ett **oåterkalleligt fel**stoppas åtgärder som anslutning, skicka och ta emot. SDK meddelar användaren. Exempel på oåterkalleliga fel är ett autentiseringsfel och ett felaktigt slutpunktsfel.

4. Om SDK identifierar ett **fel som kan återställas**försöker den igen enligt den angivna återförsöksprincipen tills den definierade timeouten har gått ut.  Observera att SDK använder **Exponential back-off med jitter** retry princip som standard.
5. När den definierade timeouten upphör att gälla slutar SDK att försöka ansluta eller skicka. Det meddelar användaren.

6. Med SDK kan användaren bifoga en motringning för att ta emot ändringar i anslutningsstatus.

SDK:erna innehåller tre principer för återförsök:

* **Exponentiell back-off med jitter**: Denna standard återförsök politik tenderar att vara aggressiv i början och sakta ner med tiden tills den når en maximal fördröjning. Designen baseras på [vägledning för återförsök från Azure Architecture Center](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific). 

* **Anpassat återförsök**: För vissa SDK-språk kan du utforma en anpassad återförsöksprincip som passar bättre för ditt scenario och sedan injicera den i RetryPolicy. Anpassat återförsök är inte tillgängligt på C SDK och stöds för närvarande inte på Python SDK. Python SDK återansluter efter behov.

* **Inget försök igen**: Du kan ange återförsöksprincipen till "inget försök igen", vilket inaktiverar logiken för återförsök. SDK försöker ansluta en gång och skicka ett meddelande en gång, förutsatt att anslutningen är etablerad. Den här principen används vanligtvis i scenarier med bandbredd eller kostnadsproblem. Om du väljer det här alternativet går meddelanden som inte kan skicka förlorade och kan inte återställas.

### <a name="retry-policy-apis"></a>Api:er för återförsöksprincip

   | SDK | Metoden SetRetryPolicy | Politiska genomföranden | Vägledning för genomförande |
   |-----|----------------------|--|--|
   |  C/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Standard:** [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Anpassad:** använd tillgänglig [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Inget nytt försök:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C/iOS-implementering](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **Standard**: [Klassen ExponentialBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Anpassat:** implementera [Gränssnittet RetryPolicy](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Inget nytt försök:** [klassen NoRetry](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java-implementering](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **Standard**: [Klassen ExponentialBackoff](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Anpassat:** implementera [IRetryPolicy-gränssnittet](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Inget nytt försök:** [klassen NoRetry](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [C# implementering](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Node| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest) | **Standard**: [Klassen ExponentialBackoffWithJitter](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Anpassat:** implementera [Gränssnittet RetryPolicy](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Inget nytt försök:** [klassen NoRetry](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Implementering av nod](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   | Python| Stöds för närvarande inte | Stöds för närvarande inte | Stöds för närvarande inte |

Följande kodexempel illustrerar det här flödet:

#### <a name="net-implementation-guidance"></a>.NET-implementeringsvägledning

I följande kodexempel visas hur du definierar och anger standardprincipen för återförsök:

   ```csharp
   // define/set default retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

För att undvika hög CPU-användning begränsas återförsöken om koden misslyckas omedelbart. Till exempel när det inte finns något nätverk eller väg till målet. Den minsta tiden för att köra nästa nytt försök är 1 sekund.

Om tjänsten svarar med ett begränsningsfel är återförsöksprincipen annorlunda och kan inte ändras via offentligt API:

   ```csharp
   // throttled retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

Återförsöksmekanismen stannar `DefaultOperationTimeoutInMilliseconds`efter , som för närvarande är inställd på 4 minuter.

#### <a name="other-languages-implementation-guidance"></a>Vägledning om implementering av andra språk

För kodexempel på andra språk, granska följande implementeringsdokument. Databasen innehåller exempel som visar användningen av api:er för återförsöksprincip.

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [SDK för Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python)

## <a name="next-steps"></a>Nästa steg

* [Använd SDK:er för enhet och tjänst](./iot-hub-devguide-sdks.md)

* [Använda SDK för IoT-enheter för C](./iot-hub-device-sdk-c-intro.md)

* [Utveckla för begränsade enheter](./iot-hub-devguide-develop-for-constrained-devices.md)

* [Utveckla för mobila enheter](./iot-hub-how-to-develop-for-mobile-devices.md)

* [Felsöka frånkoppling av enheten](iot-hub-troubleshoot-connectivity.md)
