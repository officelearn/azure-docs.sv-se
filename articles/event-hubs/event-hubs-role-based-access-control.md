---
title: Förhandsversionen av Azure Event Hubs Role-Based åtkomstkontroll (RBAC) | Microsoft Docs
description: Azure Event Hubs rollbaserad åtkomstkontroll
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: sethm
ms.openlocfilehash: 9c38f74cd4499fad1feaadb6c1bbc99da791ebd6
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888375"
---
# <a name="active-directory-role-based-access-control-preview"></a>Aktiva Directory Role-Based Access Control (förhandsversion)

Microsoft Azure tillhandahåller integrerad hantering av åtkomstkontroll för resurser och program som baseras på Azure Active Directory (AD Azure). Du kan antingen hantera användarkonton och -program specifikt för dina Azure-baserade program med Azure AD, eller du kan federera din befintliga Active Directory-infrastruktur med Azure AD för företagsomfattande single-sign-on som även sträcker sig över Azure-resurser och Azure-värdbaserade program. Du kan sedan tilldela de Azure AD-identiteterna i användar- och globala och tjänstspecifika roller för att bevilja åtkomst till Azure-resurser.

För Azure Event Hubs, hantering av namnområden och alla relaterade resurser via Azure portal och Azure resource Manager API redan skyddas med hjälp av den *rollbaserad åtkomstkontroll* (RBAC)-modellen. RBAC för runtime åtgärder ingår nu i offentlig förhandsversion. 

Ett program som använder Azure AD RBAC behöver inte hantera SAS regler och nycklar eller andra åtkomsttoken specifika för Event Hubs. Klientappen interagerar med Azure AD för att upprätta en autentiseringskontext och hämtar en åtkomsttoken för Event Hubs. Med domänanvändarkonton som kräver interaktiv inloggning, hanterar programmet aldrig autentiseringsuppgifter direkt.

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs roller och behörigheter

Den första offentliga förhandsversionen kan du bara lägga till Azure AD-konton och tjänstens huvudnamn till ”ägare” eller ”bidragsgivare” roller för ett namnområde för Event Hubs. Den här åtgärden ger den identitet fullständig kontrollen över alla entiteter i namnområdet. Management-åtgärder som ändrar namnområde topologin är inledningsvis endast stöds dock Azure resource Manager och inte via gränssnittet för hantering av inbyggda Event Hubs REST. Det här stödet innebär att även .NET Framework-klienten [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objektet kan inte användas med en Azure AD-konto.  

## <a name="use-event-hubs-with-an-azure-ad-domain-user-account"></a>Använda Event Hubs med ett användarkonto för Azure AD-domän

I följande avsnitt beskrivs de steg som krävs för att skapa och kör ett exempelprogram som frågar efter en interaktiv Azure AD-användare att logga in, hur du ger Händelsehubbar åtkomst till det aktuella användarkontot och hur du använder den identiteten för att få åtkomst till Event Hubs. 

Den här introduktionen beskriver ett enkelt konsolprogram i [kod som finns på Github](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Rbac/EventHubsSenderReceiverRbac/)

### <a name="create-an-active-directory-user-account"></a>Skapa ett Active Directory-användarkonto

Det här första steget är valfritt. Alla Azure-prenumerationer automatiskt är kopplad till en Azure Active Directory-klient och om du har åtkomst till en Azure-prenumeration, ditt användarkonto har redan registrerats. Det innebär att du kan bara använda ditt konto. 

Om du vill skapa ett särskilt konto för det här scenariot [gör så här](../automation/automation-create-aduser-account.md). Du måste ha behörighet att skapa konton i Azure Active Directory-klienten, vilket inte kanske är fallet med större företagsscenarier.

### <a name="create-an-event-hubs-namespace"></a>Skapa ett namnområde för Event Hubs

Nästa [skapa ett namnområde för Event Hubs](event-hubs-create.md) i någon av de Azure-regioner som har stöd för förhandsversionen av Event Hubs för RBAC: **östra USA**, **USA, Öst 2**, eller **Europa, västra** . 

När namnområdet har skapats går du till dess **åtkomstkontroll (IAM)** på portalen och klicka sedan på **Lägg till** att lägga till Azure AD-användarkontot i rollen ägare. Om du använder ett eget användarkonto och du skapade namnområdet, är du redan rollen ägare. Om du vill lägga till ett annat konto för rollen, Sök efter namnet på webbprogrammet i den **Lägg till behörigheter** panelen **Välj** fältet och sedan klickar du på posten. Klicka sedan på **Spara**.
 
![](./media/event-hubs-role-based-access-control/rbac1.PNG)

Användarkontot har nu tillgång till Event Hubs-namnområdet och till händelsehubben du skapade tidigare.
 
### <a name="register-the-application"></a>Registrera programmet

Innan du kan köra exempelprogrammet måste registrera den i Azure AD och Godkänn medgivande som tillåter programmet att få åtkomst till Event Hubs för dess räkning. 

Eftersom exempelprogrammet som är ett konsolprogram, måste du registrera ett internt program och lägga till API-behörigheter för **Microsoft.EventHub** till uppsättningen med ”nödvändiga behörigheter”. Interna program behöver också en **omdirigerings-URI** i Azure AD som fungerar som en identifierare; URI: N behöver inte vara ett mål för nätverket. Använd `http://eventhubs.microsoft.com` eftersom exemplet code redan det här exemplet använder du denna URI.

Detaljerad registrering stegen beskrivs i [den här självstudien](../active-directory/develop/active-directory-integrating-applications.md). Följ stegen för att registrera en **interna** appen, och följ sedan instruktionerna update att lägga till den **Microsoft.EventHub** API för att behörigheterna som krävs. Du följer stegen anteckna den **TenantId** och **ApplicationId**, eftersom du behöver dessa värden när du kör programmet.

### <a name="run-the-app"></a>Kör appen

Innan du kan köra det här exemplet redigera filen App.config och, beroende på ditt scenario, ange följande värden:

- `tenantId`: Ställ in på **TenantId** värde.
- `clientId`: Ställ in på **ApplicationId** värde. 
- `clientSecret`: Om du vill logga in med klienthemligheten, kan du skapa det i Azure AD. Kan också använda en webbapp eller API: et i stället för en inbyggd app. Lägg även till appen under **åtkomstkontroll (IAM)** i namnområdet som du skapade tidigare.
- `eventHubNamespaceFQDN`: Ställ in på nya Event Hubs-namnområdet; fullständigt kvalificerat DNS-namn till exempel `example.servicebus.windows.net`.
- `eventHubName`: Ange namnet på den händelsehubb som du skapade.
- Omdirigerings-URI som du angav i din app i föregående steg.
 
När du kör konsolprogrammet, uppmanas du att välja ett scenario; Klicka på **interaktiv användarinloggning** genom att skriva dess nummer och trycka på RETUR. Programmet visas ett fönster för inloggning, begär tillstånd att få åtkomst till Event Hubs och sedan använder tjänsten för att gå igenom scenariot skicka och ta emot under inloggning identiteten.

## <a name="next-steps"></a>Nästa steg

Besök följande länkar för mer utförlig information om Event Hubs:

* Kom igång med en [kurs om händelsehubbar](event-hubs-dotnet-standard-getstarted-send.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Prisinformation om Händelsehubbar](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)