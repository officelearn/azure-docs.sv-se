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
ms.date: 08/09/2017
ms.author: sethm
ms.openlocfilehash: b4b9d5d272bdb172f1d40db379a519a4f617550a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus, autentisering och auktorisering

Program får tillgång till Azure Service Bus-funktioner med hjälp av delade signatur åtkomst (SAS) tokenautentisering. Med SAS, program kan utgöra en token till Service Bus som har signerats med en symmetrisk nyckel kända både token utfärdaren och Service Bus (”delad”) och nyckeln är direkt som associeras med en regel som beviljar specifika åtkomsträttigheter som behörighet att ta emot / lyssna eller skicka meddelanden. SAS-regler är antingen konfigureras på namnområdet eller direkt på enheter som en kö eller ett ämne, vilket ger detaljerade åtkomstkontroll.

SAS-token kan antingen genereras av en Service Bus-klient direkt eller genereras av vissa mellanliggande token utfärdas slutpunkt som klienten interagerar med. Till exempel ett system kan kräva klienten för att anropa en Active Directory auktorisering skyddas slutpunkt för webbtjänsten för att bevisa sin identitet och Systeminformation åtkomsträttigheter och webbtjänsten returneras sedan lämplig Service Bus-token. SAS-token kan genereras enkelt använda Service Bus-token providern ingår i SDK. 

> [!IMPORTANT]
> Om du använder Azure Active Directory-åtkomstkontroll (även kallat åtkomstkontrolltjänsten eller ACS) tillsammans med Service Bus, Observera att stöd för den här metoden är nu begränsad och du bör migrera programmet att med hjälp av SAS. Mer information finns i [i det här blogginlägget](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).

## <a name="shared-access-signature-authentication"></a>Autentisering med delad Åtkomstsignatur

[SAS-autentisering](service-bus-sas.md) gör att du kan ge en användaråtkomst till Service Bus-resurser med specifika rättigheter. SAS-autentisering i Service Bus omfattar konfiguration av en krypteringsnyckel med associerade behörigheter på en Service Bus-resurs. Klienterna kan sedan komma åt resursen genom att presentera en SAS-token som består av resurs-URI som används och en utgången signeras med den konfigurerade nyckeln.

Du kan konfigurera nycklar för Säkerhetsassociationer för ett Service Bus-namnområde. Nyckeln gäller för alla meddelandeentiteter i namnutrymmet. Du kan också konfigurera nycklar på Service Bus-köer och ämnen. SAS stöds även på [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md).

Om du vill använda SAS kan du konfigurera en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objekt i ett namnområde, en kö eller ett ämne. Den här regeln består av följande element:

* *KeyName* som identifierar regeln.
* *PrimaryKey* är en krypteringsnyckel som används för att logga/Validera SAS-token.
* *Sekundär nyckel* är en krypteringsnyckel som används för att logga/Validera SAS-token.
* *Rättigheter* som representerar mängden lyssna skicka eller hantera rättigheter.

Auktoriseringsregler som konfigurerats på namnområdesnivån kan bevilja åtkomst till alla entiteter i ett namnområde för klienter med token som signerats med motsvarande nyckel. Regler kan konfigureras på en Service Bus-namnrymd, kö eller avsnittet upp till 12 detta tillstånd. Som standard en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) alla rättigheter som är konfigurerad för varje namnområde när det först har etablerats.

Om du vill få åtkomst till en entitet måste klienten kräver en SAS-token som skapats med en specifik [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). SAS-token genereras med en krypteringsnyckel som är kopplade till auktoriseringsregeln HMAC-SHA256 resurssträngen som består av resurs-URI som åtkomst begärs och en upphör att gälla.

Stöd för Service Bus SAS-autentisering ingår i Azure .NET SDK version 2.0 och senare. SAS har stöd för en [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). API: er som accepterar en anslutningssträng som en parameter har stöd för SAS-anslutningssträngar.

## <a name="next-steps"></a>Nästa steg

- Läs [Service Bus-autentisering med signaturer för delad åtkomst](service-bus-sas.md) för mer information om SAS.
- [Ändringar ACS-aktiverade namnområden.](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)
- Motsvarande information om Azure Relay autentisering och auktorisering finns [Azure Relay autentisering och auktorisering](../service-bus-relay/relay-authentication-and-authorization.md). 

