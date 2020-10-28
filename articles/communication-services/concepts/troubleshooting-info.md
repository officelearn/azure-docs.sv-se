---
title: Fel sökning i Azure Communication Services
description: Lär dig hur du samlar in den information du behöver för att felsöka kommunikations tjänst lösningen.
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 10/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4921078e9e7b5d9de06fbbc9a97dc4a964569e04
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754757"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Fel sökning i Azure Communication Services

I det här dokumentet får du hjälp med att samla in den information du behöver för att felsöka kommunikations tjänst lösningen.

## <a name="getting-help"></a>Få hjälp

Vi uppmuntrar utvecklare att skicka frågor, föreslå funktioner och rapportera problem som problem i [GitHub-lagringsplatsen](https://github.com/Azure/communication)för kommunikations tjänster. Andra forum är:

* [Microsoft Q&A](https://docs.microsoft.com/answers/questions/topics/single/101418.html)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure+communication)

Beroende på [support avtalet](https://azure.microsoft.com/support/plans/) för Azure-prenumerationen kan du skicka in ett support ärende direkt via [Azure Portal](https://azure.microsoft.com/support/create-ticket/).

För att hjälpa dig att felsöka vissa typer av problem kan du bli ombedd att ange någon av följande delar av informationen:

* **MS-CV-ID** : detta ID används för att felsöka samtal och meddelanden. 
* **Anrops-ID** : detta ID används för att identifiera kommunikations tjänst anrop.
* **SMS-meddelande-ID** : detta ID används för att identifiera SMS-meddelanden.

## <a name="access-your-ms-cv-id"></a>Få åtkomst till ditt MS-CV ID

MS-CV ID kan nås genom att konfigurera diagnostik i `clientOptions` objekt instansen när du initierar klient biblioteken. Diagnostik kan konfigureras för alla Azure-klient bibliotek, till exempel chatt, administration och VoIP-samtal.

### <a name="client-options-example"></a>Exempel på klient alternativ

Följande kodfragment demonstrerar diagnostik-konfigurationen. När klient biblioteken används med diagnostik aktive rad kommer diagnostikinformation att skickas till den konfigurerade händelse lyssnaren:

# <a name="c"></a>[C#](#tab/csharp)
``` 
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions 
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
``` 
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-call-id"></a>Åtkomst till ditt anrops-ID

När du skickar en supportbegäran via Azure Portal som rör anrop till anrop kan du bli ombedd att ange ID för det anrop som du refererar till. Detta kan nås via det anropande klient biblioteket:

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.call` or `callAgent.join` methods 
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends 
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId) 
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.call(…)` or `callAgent.join(…)` methods 
Log.d(call.getCallId()) 
```
---


## <a name="access-your-sms-message-id"></a>Få åtkomst till SMS-meddelandets ID

För SMS-problem kan du samla in meddelande-ID: t från objektet Response.

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="related-information"></a>Relaterad information
- [Loggar och diagnostik](logging-and-diagnostics.md)
- [Mått](metrics.md)
