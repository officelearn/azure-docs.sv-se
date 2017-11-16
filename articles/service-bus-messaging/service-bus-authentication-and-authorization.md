---
title: Azure Service Bus-autentisering och auktorisering | Microsoft Docs
description: "Autentisera appar till Service Bus med delade signatur åtkomst (SAS)-autentisering."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: sethm
ms.openlocfilehash: b8b5887f2003dd793ae7a50f066b893f685002a0
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus, autentisering och auktorisering

Program får tillgång till Azure Service Bus-resurser med hjälp av delade signatur åtkomst (SAS) tokenautentisering. Med SAS, program kan utgöra en token till Service Bus som har signerats med en symmetrisk nyckel kända både token utfärdaren och Service Bus (därför ”delad”) och nyckeln är direkt som associeras med en regel som beviljar specifika åtkomsträttigheter som behörigheten till ta emot/lyssna eller skicka meddelanden. SAS-regler är antingen konfigureras på namnområdet eller direkt på entiteter, till exempel en kö eller ett ämne, vilket ger detaljerade åtkomstkontroll.

SAS-token kan antingen genereras av en Service Bus-klient direkt eller genereras av vissa mellanliggande token utfärdande slutpunkt som klienten samverkar. Ett system kan till exempel kräva klienten för att anropa en Active Directory auktorisering skyddas slutpunkt för webbtjänsten för att bevisa sin identitet och åtkomstbehörigheter till systemet, och webbtjänsten sedan returnerar lämplig Service Bus-token. SAS-token kan genereras enkelt använda Service Bus-token providern ingår i Azure SDK. 

> [!IMPORTANT]
> Om du använder Azure Active Directory-åtkomstkontroll (även kallat åtkomstkontrolltjänsten eller ACS) med Service Bus, Observera att stöd för den här metoden är nu begränsad och du bör migrera dina program att använda SAS. Mer information finns i [i det här blogginlägget](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) och [i den här artikeln](service-bus-migrate-acs-sas.md).

## <a name="shared-access-signature-authentication"></a>Autentisering med delad Åtkomstsignatur

[SAS-autentisering](service-bus-sas.md) gör att du kan ge en användaråtkomst till Service Bus-resurser med specifika rättigheter. SAS-autentisering i Service Bus omfattar konfiguration av en krypteringsnyckel med associerade behörigheter på en Service Bus-resurs. Klienterna kan sedan komma åt resursen genom att presentera en SAS-token som består av resurs-URI som används och en utgången signeras med den konfigurerade nyckeln.

Du kan konfigurera nycklar för Säkerhetsassociationer för ett Service Bus-namnområde. Nyckeln gäller för alla meddelandeentiteter inom det här namnområdet. Du kan också konfigurera nycklar på Service Bus-köer och ämnen. SAS stöds även på [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md).

Om du vill använda SAS kan du konfigurera en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objekt i ett namnområde, en kö eller ett ämne. Den här regeln består av följande element:

* *KeyName*: identifierar regeln.
* *PrimaryKey*: en krypteringsnyckel som används för att logga/Validera SAS-token.
* *Sekundär nyckel*: en krypteringsnyckel som används för att logga/Validera SAS-token.
* *Rättigheter*: representerar mängden **lyssna**, **skicka**, eller **hantera** rättigheter.

Auktoriseringsregler som konfigurerats på namnområdesnivån kan bevilja åtkomst till alla entiteter i ett namnområde för klienter med token som signerats med motsvarande nyckel. Du kan konfigurera upp till 12 sådana auktoriseringsregler på Service Bus-namnområde, kö eller i avsnittet. Som standard en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) alla rättigheter som är konfigurerad för varje namnområde när det först har etablerats.

Om du vill få åtkomst till en entitet måste klienten kräver en SAS-token som skapats med en specifik [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). SAS-token genereras med en krypteringsnyckel som är kopplade till auktoriseringsregeln HMAC-SHA256 resurssträngen som består av resurs-URI som åtkomst begärs och en upphör att gälla.

Stöd för Service Bus SAS-autentisering ingår i Azure .NET SDK version 2.0 och senare. SAS har stöd för en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). API: er som accepterar en anslutningssträng som en parameter har stöd för SAS-anslutningssträngar.

## <a name="next-steps"></a>Nästa steg

- Läs [Service Bus-autentisering med signaturer för delad åtkomst](service-bus-sas.md) för mer information om SAS.
- Så här [migrera från Azure Active Directory Access Control (ACS) till signatur för delad åtkomst auktorisering](service-bus-migrate-acs-sas.md).
- [Ändringar ACS-aktiverade namnområden](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Motsvarande information om Azure Relay autentisering och auktorisering finns [Azure Relay autentisering och auktorisering](../service-bus-relay/relay-authentication-and-authorization.md). 

