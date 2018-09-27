---
title: Azure Service Bus, autentisering och auktorisering | Microsoft Docs
description: Autentisera appar till Service Bus med signatur för delad åtkomst (SAS)-autentisering.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2018
ms.author: spelluru
ms.openlocfilehash: 726e414566622d28817c34c92bd45653677d590f
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392728"
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus, autentisering och auktorisering

Program få åtkomst till Azure Service Bus-resurser med hjälp av autentisering med signatur för delad åtkomst (SAS)-token. Med SAS, presentera program en token för Service Bus som har signerats med en symmetrisk nyckel som kända både tokenutfärdaren och Service Bus (därför ”delad”) och nyckeln är direkt kopplade till en regel som beviljar behörighet för specifika, t.ex. behörighet att ta emot/lyssna eller skicka meddelanden. SAS-regler är antingen konfigurerats på namnområdet eller direkt på entiteter, till exempel en kö eller ämne, så att de detaljerade åtkomstkontroll.

SAS-token kan antingen genereras av en Service Bus-klient direkt eller genereras av vissa mellanliggande token som utfärdas slutpunkten som klienten interagerar. Ett system kan exempelvis kräva klienten för att anropa en Active Directory auktorisering skyddas webbtjänstslutpunkt för att bevisa sin identitet och åtkomstbehörigheter till systemet, och webbtjänsten och returnerar en lämplig Service Bus-token. Den här SAS-token kan skapas enkelt med hjälp av Service Bus-tokenleverantör som ingår i Azure SDK. 

> [!IMPORTANT]
> Om du använder Azure Active Directory Access Control (även kallat Access Control Service eller ACS) med Service Bus, Tänk på att stöd för den här metoden är nu begränsad och du bör migrera ditt program att använda SAS. Mer information finns i [det här blogginlägget](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) och [i den här artikeln](service-bus-migrate-acs-sas.md).

## <a name="shared-access-signature-authentication"></a>Autentisering med delad Åtkomstsignatur

[SAS-autentisering](service-bus-sas.md) gör det möjligt att ge en användaråtkomst till Service Bus-resurser med specifika rättigheter. SAS-autentisering i Service Bus innebär att konfigurationen av en kryptografisk nyckel med associerade behörigheter på en Service Bus-resurs. Klienterna kan sedan få åtkomst till resursen genom att presentera en SAS-token som består av resurs-URI som används och ett förfallodatum som signeras med den konfigurera nyckeln.

Du kan konfigurera nycklar för Signaturen för ett Service Bus-namnområde. Nyckeln gäller för alla meddelandeentiteter inom det här namnområdet. Du kan också konfigurera nycklar på Service Bus-köer och ämnen. SAS stöds även på [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md).

Om du vill använda SAS kan du konfigurera en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektet på en namnrymd, kö eller ämne. Den här regeln består av följande element:

* *KeyName*: identifierar regeln.
* *PrimaryKey*: en krypteringsnyckel som används för att logga/Validera SAS-token.
* *Sekundär nyckel*: en krypteringsnyckel som används för att logga/Validera SAS-token.
* *Rättigheter*: representerar samlingen **lyssna**, **skicka**, eller **hantera** rättigheter beviljas.

Auktoriseringsregler som konfigurerats på namnområdesnivå kan bevilja åtkomst till alla entiteter i ett namnområde för klienter med token som signerats med motsvarande nyckel. Du kan konfigurera upp till 12 sådana auktoriseringsregler på en Service Bus-namnområde, kö eller ämne. Som standard en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) med alla rättigheter som konfigureras för varje namnområde när den först har etablerats.

Om du vill få åtkomst till en entitet måste klienten kräver en SAS-token som genererats med hjälp av en specifik [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). SAS-token genereras med en kryptografisk nyckel som är associerade med auktoriseringsregeln HMAC-SHA256 av en resurssträng som består av resurs-URI som åtkomst begärs och upphöra att gälla.

Support för SAS-autentisering för Service Bus är inkluderade i Azure .NET SDK-version 2.0 och senare. SAS har stöd för en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Alla API: er som accepterar en anslutningssträng som en parameter har stöd för SAS-anslutningssträngar.

## <a name="next-steps"></a>Nästa steg

- Fortsätt att läsa [Service Bus-autentisering med signaturer för delad åtkomst](service-bus-sas.md) för mer information om SAS.
- Så här [migrera från Azure Active Directory Access Control (ACS) till signatur för delad åtkomst auktorisering](service-bus-migrate-acs-sas.md).
- [Ändringar till ACS-aktiverat namnområden](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Motsvarande information om Azure Relay-autentisering och auktorisering finns i [Azure Relay-autentisering och auktorisering](../service-bus-relay/relay-authentication-and-authorization.md). 

