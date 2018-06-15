---
title: Översikt över Azure Event Hubs autentisering och säkerhetsmodell | Microsoft Docs
description: Autentisering och säkerhet modellen översikt över Event Hubs.
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/30/2018
ms.author: sethm
ms.openlocfilehash: 5264930dcb802c2a58abc179bdd0041acc9f58d0
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311377"
---
# <a name="event-hubs-authentication-and-security-model-overview"></a>Autentisering och säkerhet modellen översikt över Event Hubs

Händelsehubbar i Azure-säkerhetsmodellen uppfyller följande krav:

* Endast klienter som presenterar giltiga autentiseringsuppgifter kan skicka data till en händelsehubb.
* En klient kan personifiera en annan klient.
* En falsk klient blockeras från att skicka data till en händelsehubb.

## <a name="client-authentication"></a>Klientautentisering

Händelsehubbar-säkerhetsmodellen bygger på en kombination av [delade signatur åtkomst (SAS)](../service-bus-messaging/service-bus-sas.md) token och *händelseutfärdare*. En händelseutfärdare definierar en virtuell slutpunkt för en händelsehubb. Utgivaren kan bara användas för att skicka meddelanden till en händelsehubb. Det går inte att ta emot meddelanden från en utgivare.

En händelsehubb använder normalt en utgivare per klient. Alla meddelanden som skickas till någon av utgivare i en händelsehubb är köas inom den händelsehubben. Utgivare Aktivera detaljerad åtkomstkontroll och begränsning.

Varje händelsehubbklient tilldelas ett unikt token som överförs till klienten. Token som tillverkas så att varje unik token ger tillgång till en annan unik utgivare. En klient som har en token kan bara skicka till en utgivare, men inga andra utgivare. Om flera klienter delar samma token, delar dem en utgivare.

Det är möjligt att förse enheter med token som ger direktåtkomst till en händelsehubb rekommenderas inte. Alla enheter som innehåller detta token kan skicka meddelanden direkt till den händelsehubben. Dessa enheter inte omfattas begränsning. Dessutom kan du övriga enheten från att skicka till den event hub.

Alla token är signerade med en SAS-nyckel. Alla token är vanligtvis signerade med samma nyckel. Klienter är inte medvetna om nyckeln. Detta förhindrar att andra klienter tillverkar token.

### <a name="create-the-sas-key"></a>Skapa SAS-nyckeln

När du skapar ett namnområde för Händelsehubbar tjänsten automatiskt genererar en 256-bitars SAS-nyckel som heter **RootManageSharedAccessKey**. Den här regeln har en associerad par primära och sekundära nycklar som ger skicka lyssna och hantera rättigheter till i namnområdet. Du kan också skapa ytterligare nycklar. Vi rekommenderar att du ger en nyckel som ger skickar behörigheter till specifika händelsehubben. Under resten av det här avsnittet förutsätts att den här nyckeln med namnet **EventHubSendKey**.

I följande exempel skapas en endast send-nyckel när du skapar händelsehubben:

```csharp
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create event hub with a SAS rule that enables sending to that event hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Generera token

Du kan generera token med SAS-nyckeln. Du måste skapa en enda token per klient. Token kan sedan produceras med följande metod. Alla token genereras med hjälp av den **EventHubSendKey** nyckel. Varje token tilldelas ett unikt URI.

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

När du anropar den här metoden URI: N måste anges som `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. För alla token URI är identiska, med undantag av `PUBLISHER_NAME`, som ska vara olika för varje token. Vi rekommenderar `PUBLISHER_NAME` representerar ID för klienten som tar emot säkerhetstoken.

Den här metoden skapar en token med följande struktur:

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

Förfallotid för token har angetts i sekunder från den 1 januari 1970. Följande är ett exempel på en token:

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

Token har vanligtvis en livslängd som liknar eller överskrider livslängden på klienten. Om klienten har möjlighet att få en ny token, kan token med en kortare livslängd användas.

### <a name="sending-data"></a>Skicka data

När token har skapats kan etableras varje klient med sin egen unika token.

När klienten skickar data till en händelsehubb, taggar skicka begäran till token. Kommunikationen mellan klienten och händelsehubben måste ske över en krypterad kanal för att hindra en angripare från avlyssning och stjäla token.

### <a name="blacklisting-clients"></a>Godkänna klienter

Om en token blir stulen av en angripare kan angripare imitera klienten vars token stulna. Godkänna en klient återger den klienten inte kan användas tills det mottar en ny token som använder en annan utgivare.

## <a name="authentication-of-back-end-applications"></a>Autentisering av backend-program

Händelsehubbar använder en säkerhetsmodell som liknar den modell som används för Service Bus-ämnen för att autentisera backend-program som använder data som genereras av Händelsehubbar klienter. En konsumentgrupp Händelsehubbar motsvarar en prenumeration på en Service Bus-ämne. En klient kan skapa en konsumentgrupp om begäran att skapa konsumentgruppen åtföljs av en token som beviljar hanterar behörighet för händelsehubben eller för det namnområde där händelsehubben tillhör. En klient är tillåtet att använda data från en konsumentgrupp receive-begäran åtföljs av en token som ger receive rättigheter på just den konsumentgruppen, händelsehubben eller det namnområde där händelsehubben tillhör.

Den aktuella versionen av Service Bus stöder inte SAS regler för enskilda prenumerationer. Detsamma gäller för Händelsehubbar konsumentgrupper. Stöd för SAS läggs för båda funktionerna i framtiden.

SAS-autentisering för enskilda konsumentgrupper saknas, kan du använda SAS-nycklar för att skydda alla konsumentgrupper med en gemensam nyckel. Den här metoden kan program använda data från någon av konsumentgrupper för en händelsehubb.

## <a name="next-steps"></a>Nästa steg

Mer information om Händelsehubbar finns i följande avsnitt:

* [Event Hubs-översikt]
* [Översikt över signaturer för delad åtkomst]
* [Exempelprogram som använder Event Hubs]

[Event Hubs-översikt]: event-hubs-what-is-event-hubs.md
[Exempelprogram som använder Event Hubs]: https://github.com/Azure/azure-event-hubs/tree/master/samples
[Översikt över signaturer för delad åtkomst]: ../service-bus-messaging/service-bus-sas.md

