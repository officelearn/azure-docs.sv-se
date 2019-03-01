---
title: Autentisering och säkerhetsmodell – Azure Event Hubs | Microsoft Docs
description: Den här artikeln beskriver autentisering och säkerhetsmodell av Händelsehubbar i Azure.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 19b347423c28b4c615f90f325ead462b9d3e8e9e
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990042"
---
# <a name="azure-event-hubs---authentication-and-security-model"></a>Azure Event Hubs - autentisering och säkerhetsmodell

Azure Event Hubs-säkerhetsmodellen uppfyller följande krav:

* Endast klienter som presenterar giltiga autentiseringsuppgifter kan skicka data till en händelsehubb.
* En klient kan personifiera en annan klient.
* En falsk klient blockeras från att skicka data till en händelsehubb.

## <a name="client-authentication"></a>Klientautentisering

Event Hubs-säkerhetsmodellen bygger på en kombination av [signatur för delad åtkomst (SAS)](../service-bus-messaging/service-bus-sas.md) token och *händelseutfärdare*. En händelseutfärdare definierar en virtuell slutpunkt för en händelsehubb. Utgivaren kan bara användas för att skicka meddelanden till en händelsehubb. Det går inte att ta emot meddelanden från en utgivare.

En händelsehubb använder vanligtvis en utgivare per klient. Alla meddelanden som skickas till någon av utgivare av en händelsehubb är i kön i den händelsehubben. Utgivare Aktivera detaljerad åtkomstkontroll och begränsning.

Varje Event Hubs-klient tilldelas en unik token som överförts till klienten. Token som tillverkas, så att varje unik token ger tillgång till en annan unik utgivare. En klient som har en token kan bara skicka till en utgivare, men inga andra utgivare. Om flera klienter delar samma token, delar var och en av dem en utgivare.

Även om det inte rekommenderas så är det möjligt att förse enheter med token som ger direktåtkomst till en händelsehubb. Vilken enhet som innehåller detta token kan skicka meddelanden direkt till den event hub. Sådana en enhet omfattas inte av begränsning. Dessutom kan du Svartlistad enheten från att skicka till den händelsehubben.

Alla token är signerade med en SAS-nyckel. Vanligtvis signeras alla token med samma nyckel. Klienter är inte medvetna om registernyckeln. Detta förhindrar att andra klienter inom tillverkning token.

### <a name="create-the-sas-key"></a>Skapa SAS-nyckeln

När du skapar ett namnområde för Event Hubs, genererar tjänsten automatiskt en 256-bitars SAS-nyckel med namnet **RootManageSharedAccessKey**. Den här regeln har ett kopplat par med primära och sekundära nycklarna som beviljar skicka, lyssna och hantera rättigheter till namnområdet. Du kan också skapa ytterligare nycklar. Vi rekommenderar att du skapar en nyckel som att skicka beviljar behörigheter till specifika event hub. Under resten av det här avsnittet förutsätts att du har gett den här nyckeln **EventHubSendKey**.

I följande exempel skapas en endast skicka nyckel när du skapar händelsehubben:

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

Du kan generera token med SAS-nyckeln. Du måste skapa bara en token per klient. Token kan sedan produceras med följande metod. Alla token genereras med hjälp av den **EventHubSendKey** nyckel. Varje token tilldelas ett unikt URI. Resursparametern ”” motsvarar den URI-slutpunkten för tjänsten (händelsehubb i det här fallet).

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

När du anropar den här metoden, URI: N bör anges som `https://<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. För alla token URI: N är identiska, med undantag för `PUBLISHER_NAME`, vilket ska vara olika för varje token. Vi rekommenderar `PUBLISHER_NAME` representerar ID: T för den klient som tar emot den token.

Den här metoden skapar en token med följande struktur:

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

Den token upphör att gälla har angetts i sekunder från den 1 januari 1970. Följande är ett exempel på en token:

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

Token har vanligtvis en livslängd som liknar eller överskrider livslängden för klienten. Om klienten har möjlighet att skaffa en ny token, kan token med en kortare livslängd användas.

### <a name="sending-data"></a>Skicka data

När token som har skapats kan etableras varje klient med sin egen unika token.

När klienten skickar data till en händelsehubb, taggar skicka begäran med token. Om du vill hindra en angripare från avlyssning och stjäla token måste kommunikationen mellan klienten och händelsehubben ske över en krypterad kanal.

### <a name="blacklisting-clients"></a>Godkänna klienter

Om en token blir stulen av en angripare kan angriparen personifiera klienten vars token har blivit stulen. Godkänna en klient återger den klienten inte kan användas förrän den tagit emot en ny token som använder en annan utgivare.

## <a name="authentication-of-back-end-applications"></a>Autentisering av backend-program

Event Hubs använder en säkerhetsmodell som liknar den modell som används för Service Bus-ämnen för att autentisera backend-program som använder data som genereras av Event Hubs-klienter. En konsumentgrupp för Event Hubs motsvarar en prenumeration på en Service Bus-ämne. En klient kan skapa en konsumentgrupp om begäran att skapa konsumentgruppen åtföljs av en token som beviljar hanterar behörigheter för event hub, eller för det namnområde där händelsehubben tillhör. En klient har tillåtelse att använda data från en konsumentgrupp receive-begäran åtföljs av en token som ger receive rättigheter på konsumentgruppen, event hub eller namnområdet som event hub tillhör.

Den aktuella versionen av Service Bus stöder inte SAS regler för enskilda prenumerationer. Detsamma gäller för Event Hubs konsumentgrupper. Support för SAS läggs för båda funktionerna i framtiden.

Du kan använda SAS-nycklar om SAS-autentisering för enskilda konsumentgrupper, för att skydda alla konsumentgrupper med en gemensam nyckel. Den här metoden kan ett program för att använda data från någon av grupperna konsument av en händelsehubb.

## <a name="next-steps"></a>Nästa steg

Mer information om Händelsehubbar finns i följande avsnitt:

* [Event Hubs-översikt]
* [Översikt över signaturer för delad åtkomst]
* [Exempelprogram som använder Event Hubs]

[Event Hubs-översikt]: event-hubs-what-is-event-hubs.md
[Exempelprogram som använder Event Hubs]: https://github.com/Azure/azure-event-hubs/tree/master/samples
[Översikt över signaturer för delad åtkomst]: ../service-bus-messaging/service-bus-sas.md

