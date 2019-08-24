---
title: Azure Service Bus autentisering och auktorisering | Microsoft Docs
description: Autentisera appar för att Service Bus med autentisering med signatur för delad åtkomst (SAS).
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
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013236"
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus, autentisering och auktorisering

Program får till gång till Azure Service Bus resurser med hjälp av autentisering med signatur för delad åtkomst (SAS). Med SAS visar program en token för att Service Bus som har signerats med en symmetrisk nyckel som är känd både till token Issuer och Service Bus (delas därför ut) och den nyckeln är direkt kopplad till en regel som beviljar vissa åtkomst rättigheter, till exempel behörighet att ta emot/lyssna eller skicka meddelanden. SAS-regler konfigureras antingen i namn området eller direkt på entiteter som en kö eller ett ämne, vilket ger detaljerad åtkomst kontroll.

SAS-token kan antingen genereras av en Service Bus-klient direkt, eller så kan de genereras av en del mellanliggande token-utfärdande slut punkt som klienten interagerar med. Ett system kan till exempel kräva att klienten anropar en Active Directory Authorization Protected Web Service-slutpunkt för att bevisa sin identitets-och system åtkomst och att webb tjänsten returnerar rätt Service Bus token. Den här SAS-token kan skapas enkelt med Service Bus token-providern som ingår i Azure SDK. 

> [!IMPORTANT]
> Observera att stödet för den här metoden är begränsat och du bör migrera ditt program för att använda SAS, om du använder Azure Active Directory Access Control (även kallat Access Control Service eller ACS) med Service Bus. Mer information finns i [det här blogg inlägget](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) och [den här artikeln](service-bus-migrate-acs-sas.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory (Azure AD)-integration för Service Bus resurser ger rollbaserad åtkomst kontroll (RBAC) för detaljerad kontroll över en klients åtkomst till resurser. Du kan använda rollbaserad åtkomst kontroll (RBAC) för att bevilja behörighet till säkerhets objekt, som kan vara en användare, en grupp eller ett huvud namn för en program tjänst. Säkerhets objekt autentiseras av Azure AD för att returnera en OAuth 2,0-token. Token kan användas för att auktorisera en begäran om åtkomst till en Service Bus resurs (kö, ämne osv.).

Mer information om hur du autentiserar med Azure AD finns i följande artiklar:

- [Autentisera med hanterade identiteter](service-bus-managed-service-identity.md)
- [Autentisera från ett program](authenticate-application.md)

> [!IMPORTANT]
> Auktorisering av användare eller program med OAuth 2,0-token som returnerades av Azure AD ger överlägsen säkerhet och enkel användning över signaturer för delad åtkomst (SAS). Med Azure AD behöver du inte lagra tokens i din kod och potentiella säkerhets risker. Vi rekommenderar att du använder Azure AD med dina Azure Service Bus-program när det är möjligt. 


## <a name="shared-access-signature"></a>Signatur för delad åtkomst
[SAS-autentisering](service-bus-sas.md) gör att du kan ge en användare åtkomst till Service Bus resurser med vissa rättigheter. SAS-autentisering i Service Bus inbegriper konfigurationen av en krypterings nyckel med tillhör ande rättigheter på en Service Bus resurs. Klienter kan sedan få åtkomst till den resursen genom att presentera en SAS-token som består av resurs-URI: n som används och förfallo datum signerad med den konfigurerade nyckeln.

Du kan konfigurera nycklar för SAS på en Service Bus-namnrymd. Nyckeln gäller för alla meddelande enheter inom det namn området. Du kan också konfigurera nycklar i Service Bus köer och ämnen. SAS stöds också på [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md).

Om du vill använda SAS kan du konfigurera ett [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -objekt i ett namn område, en kö eller ett ämne. Den här regeln består av följande element:

* *Namn*: identifierar regeln.
* *PrimaryKey*: en kryptografisk nyckel som används för att signera/validera SAS-token.
* *SecondaryKey*: en kryptografisk nyckel som används för att signera/validera SAS-token.
* *Rättigheter*: representerar samlingen av beviljade behörigheterna **Lyssna**, **Skicka**eller **Hantera** .

Auktoriseringsregler som kon figurer ATS på namn områdes nivån kan ge åtkomst till alla entiteter i ett namn område för klienter med token som signerats med motsvarande nyckel. Du kan konfigurera upp till 12 sådana auktoriseringsregler på en Service Bus namnrymd, kö eller ämne. Som standard konfigureras en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) med alla rättigheter för varje namnrymd när den först har tillhandahållits.

För att få åtkomst till en entitet kräver klienten en SAS-token som genereras med en speciell [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). SAS-token genereras med hjälp av HMAC-SHA256 för en resurs sträng som består av resurs-URI till vilken åtkomst begärs, och ett förfallo datum med en kryptografisk nyckel som är kopplad till auktoriseringsregeln.

Stöd för SAS-autentisering för Service Bus ingår i Azure .NET SDK-versionerna 2,0 och senare. SAS har stöd för en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Alla API: er som accepterar en anslutnings sträng som parameter är stöd för SAS-anslutningssträngar.

## <a name="next-steps"></a>Nästa steg

- Fortsätt att läsa [Service Bus autentisering med signaturer för delad åtkomst](service-bus-sas.md) för mer information om SAS.
- [Migrera från Azure Active Directory Access Control (ACS) till signaturen för delad åtkomst](service-bus-migrate-acs-sas.md).
- [Ändringar av ACS-aktiverade namn rymder](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- För motsvarande information om Azure Relay autentisering och auktorisering, se [Azure Relay autentisering och auktorisering](../service-bus-relay/relay-authentication-and-authorization.md). 

