---
title: Autentisering och auktorisering av Azure Service Bus | Microsoft-dokument
description: Autentisera appar till Service Bus med SAS-autentisering (Shared Access Signature).
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 7234e33c04e742c77630f8d87481c7831fb00bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70013236"
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus, autentisering och auktorisering

Program får åtkomst till Azure Service Bus-resurser med SAS-tokenautentisering (Shared Access Signature). Med SAS presenterar program en token för Service Bus som har signerats med en symmetrisk nyckel som är känd både för tokenutfärdaren och Service Bus (därav "delad") och nyckeln är direkt kopplad till en regel som ger specifika åtkomsträttigheter, till exempel behörighet att ta emot/lyssna eller skicka meddelanden. SAS-regler konfigureras antingen på namnområdet eller direkt på entiteter som en kö eller ett ämne, vilket möjliggör finkornig åtkomstkontroll.

SAS-token kan antingen genereras av en Service Bus-klient direkt, eller så kan de genereras av någon mellanliggande tokenutgivningslutpunkt som klienten interagerar med. Ett system kan till exempel kräva att klienten anropar en slutpunkt för Active Directory-auktoriseringsskyddad webbtjänst för att bevisa sin identitet och systemåtkomsträttigheter, och webbtjänsten returnerar sedan lämplig Service Bus-token. Den här SAS-token kan enkelt genereras med hjälp av tjänstbusstoken providern som ingår i Azure SDK. 

> [!IMPORTANT]
> Om du använder Azure Active Directory Access Control (kallas även Åtkomstkontrolltjänst eller ACS) med Service Bus, observera att stödet för den här metoden nu är begränsat och du bör migrera ditt program för att använda SAS. Mer information finns i [det här blogginlägget](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) och [den här artikeln](service-bus-migrate-acs-sas.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory (Azure AD) integration för Service Bus-resurser ger rollbaserad åtkomstkontroll (RBAC) för finkornig kontroll över en klients åtkomst till resurser. Du kan använda rollbaserad åtkomstkontroll (RBAC) för att bevilja behörigheter till säkerhetsobjekt, som kan vara en användare, en grupp eller ett huvudnamn för programtjänsten. Säkerhetsobjektet autentiseras av Azure AD för att returnera en OAuth 2.0-token. Token kan användas för att auktorisera en begäran om åtkomst till en Service Bus-resurs (kö, ämne osv.).

Mer information om hur du autentisering med Azure AD finns i följande artiklar:

- [Autentisera med hanterade identiteter](service-bus-managed-service-identity.md)
- [Autentisera från ett program](authenticate-application.md)

> [!IMPORTANT]
> Att auktorisera användare eller program med OAuth 2.0-token som returneras av Azure AD ger överlägsen säkerhet och användarvänlighet över SIGNATURER för delad åtkomst (SAS). Med Azure AD behöver du inte lagra token i koden och riskera potentiella säkerhetsproblem. Vi rekommenderar att du använder Azure AD med dina Azure Service Bus-program när det är möjligt. 


## <a name="shared-access-signature"></a>Signatur för delad åtkomst
[MED SAS-autentisering](service-bus-sas.md) kan du ge en användare åtkomst till Service Bus-resurser med specifika rättigheter. SAS-autentisering i Service Bus innebär konfiguration av en kryptografisk nyckel med associerade rättigheter på en Service Bus-resurs. Klienter kan sedan få åtkomst till den resursen genom att presentera en SAS-token, som består av att resursen URI används och en förfallodatum som har signerats med den konfigurerade nyckeln.

Du kan konfigurera nycklar för SAS på ett servicebussnamnområde. Nyckeln gäller för alla meddelandeentiteter inom det namnområdet. Du kan också konfigurera nycklar i servicebussköer och ämnen. SAS stöds också på [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md).

Om du vill använda SAS kan du konfigurera ett [SharedAccessAuthorizationRule-objekt](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) i ett namnområde, en kö eller ett ämne. Denna regel består av följande element:

* *KeyName*: identifierar regeln.
* *PrimaryKey*: en kryptografisk nyckel som används för att signera/validera SAS-token.
* *SecondaryKey*: en kryptografisk nyckel som används för att signera/validera SAS-token.
* *Rättigheter*: representerar samlingen **av Lyssna,** **Skicka**eller **Hantera** rättigheter som beviljats.

Auktoriseringsregler som konfigurerats på namnområdesnivå kan ge åtkomst till alla entiteter i ett namnområde för klienter med token signerade med motsvarande nyckel. Du kan konfigurera upp till 12 sådana auktoriseringsregler för en servicebussnamnområde, kö eller ett ämne. Som standard konfigureras en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) med alla rättigheter för varje namnområde när det först etableras.

För att komma åt en entitet kräver klienten en SAS-token som genereras med hjälp av en specifik [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). SAS-token genereras med HMAC-SHA256 för en resurssträng som består av resurs-URI som åtkomst begärs till och en förfallodatum med en kryptografisk nyckel som är associerad med auktoriseringsregeln.

SAS-autentiseringsstöd för Service Bus ingår i Azure .NET SDK version 2.0 och senare. SAS innehåller stöd för en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Alla API:er som accepterar en anslutningssträng som en parameter innehåller stöd för SAS-anslutningssträngar.

## <a name="next-steps"></a>Nästa steg

- Fortsätt läsa [Service Bus-autentisering med signaturer](service-bus-sas.md) för delad åtkomst för mer information om SAS.
- Migrera [från Azure Active Directory Access Control (ACS) till auktorisering för delad åtkomstsignatur](service-bus-migrate-acs-sas.md).
- [Ändringar i ACS-aktiverade namnområden](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Mer information om Azure Relay-autentisering och auktorisering finns i [Azure Relay-autentisering och auktorisering](../service-bus-relay/relay-authentication-and-authorization.md). 

