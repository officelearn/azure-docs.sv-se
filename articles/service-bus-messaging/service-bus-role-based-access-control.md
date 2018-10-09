---
title: Förhandsversionen av Azure Service Bus Role-Based åtkomstkontroll (RBAC) | Microsoft Docs
description: Azure Service Bus-rollbaserad åtkomstkontroll
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: spelluru
ms.openlocfilehash: 0da4fdf5afc0cbe76e19260b486aa9112d0d74c8
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854328"
---
# <a name="active-directory-role-based-access-control-preview"></a>Aktiva Directory Role-Based Access Control (förhandsversion)

Microsoft Azure tillhandahåller integrerad hantering av åtkomstkontroll för resurser och program som baseras på Azure Active Directory (AD Azure). Med Azure AD, kan du hantera användarkonton och program specifikt för din Azure-baserade program, eller du kan federera din befintliga Active Directory-infrastruktur med Azure AD för företagsomfattande single-sign-on som även sträcker sig över Azure-resurser och Azure-värdbaserade program. Du kan sedan tilldela de Azure AD-identiteterna i användar- och globala och tjänstspecifika roller för att bevilja åtkomst till Azure-resurser.

För Azure Service Bus, hantering av namnområden och alla relaterade resurser via Azure portal och Azure resource Manager API redan skyddas med hjälp av den *rollbaserad åtkomstkontroll* (RBAC)-modellen. RBAC för runtime åtgärder ingår nu i offentlig förhandsversion. 

Ett program som använder Azure AD RBAC behöver inte hantera SAS regler och nycklar eller andra åtkomsttoken specifika för Service Bus. Klientappen interagerar med Azure AD för att upprätta en autentiseringskontext och hämtar en åtkomsttoken för Service Bus. Med domänanvändarkonton som kräver interaktiv inloggning, hanterar programmet aldrig autentiseringsuppgifter direkt.

## <a name="service-bus-roles-and-permissions"></a>Service Bus-roller och behörigheter

Den första offentliga förhandsversionen kan du bara lägga till Azure AD-konton och tjänstens huvudnamn till ”ägare” eller ”bidragsgivare” roller för ett namnområde för Service Bus-meddelanden. Den här åtgärden ger den identitet fullständig kontrollen över alla entiteter i namnområdet. Management-åtgärder som ändrar namnområde topologin är inledningsvis endast stöds dock Azure resource Manager och inte via gränssnittet för hantering av interna Service Bus REST. Det här stödet innebär att även .NET Framework-klienten [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objektet kan inte användas med en Azure AD-konto.  

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>Använda Service Bus med ett användarkonto för Azure AD-domän

I följande avsnitt beskrivs de steg som krävs för att skapa och kör ett exempelprogram som frågar efter en interaktiv Azure AD-användare att logga in, hur du ger Service Bus-åtkomst till det aktuella användarkontot och hur du använder den identiteten för att få åtkomst till Event Hubs. 

Den här introduktionen beskriver ett enkelt konsolprogram den [kod som finns på Github](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).

### <a name="create-an-active-directory-user-account"></a>Skapa ett Active Directory-användarkonto

Det här första steget är valfritt. Alla Azure-prenumerationer automatiskt är kopplad till en Azure Active Directory-klient och om du har åtkomst till en Azure-prenumeration, ditt användarkonto har redan registrerats. Det innebär att du kan bara använda ditt konto. 

Om du vill skapa ett särskilt konto för det här scenariot [gör så här](../automation/automation-create-aduser-account.md). Du måste ha behörighet att skapa konton i Azure Active Directory-klienten, vilket inte kanske är fallet med större företagsscenarier.

### <a name="create-a-service-bus-namespace"></a>Skapa ett namnområde för Service Bus

Nästa [skapa ett namnområde för Service Bus-meddelanden](service-bus-create-namespace-portal.md) i någon av de Azure-regioner som har förhandsversionsstöd för RBAC: **östra USA**, **USA, Öst 2**, eller **Europa, västra** . 

När namnområdet har skapats går du till dess **åtkomstkontroll (IAM)** på portalen och klicka sedan på **Lägg till** att lägga till Azure AD-användarkontot i rollen ägare. Om du använder ett eget användarkonto och du skapade namnområdet, är du redan rollen ägare. Om du vill lägga till ett annat konto för rollen, Sök efter namnet på webbprogrammet i den **Lägg till behörigheter** panelen **Välj** fältet och sedan klickar du på posten. Klicka sedan på **Spara**.

![](./media/service-bus-role-based-access-control/rbac1.PNG)

Användarkontot har nu tillgång till Service Bus-namnrymd och i kön du skapade tidigare.
 
### <a name="register-the-application"></a>Registrera programmet

Innan du kan köra exempelprogrammet måste registrera den i Azure AD och Godkänn medgivande som tillåter programmet att få åtkomst till Azure Service Bus för dess räkning. 

Eftersom exempelprogrammet som är ett konsolprogram, måste du registrera ett internt program och lägga till API-behörigheter för **Microsoft.ServiceBus** till uppsättningen med ”nödvändiga behörigheter”. Interna program behöver också en **omdirigerings-URI** i Azure AD som fungerar som en identifierare; URI: N behöver inte vara ett mål för nätverket. Använd `http://servicebus.microsoft.com` eftersom exemplet code redan det här exemplet använder du denna URI.

Detaljerad registrering stegen beskrivs i [den här självstudien](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Följ stegen för att registrera en **interna** appen, och följ sedan instruktionerna update att lägga till den **Microsoft.ServiceBus** API för att behörigheterna som krävs. Du följer stegen anteckna den **TenantId** och **ApplicationId**, eftersom du behöver dessa värden när du kör programmet.

### <a name="run-the-app"></a>Kör appen

Innan du kan köra det här exemplet redigera filen App.config och, beroende på ditt scenario, ange följande värden:

- `tenantId`: Ställ in på **TenantId** värde.
- `clientId`: Ställ in på **ApplicationId** värde. 
- `clientSecret`: Om du vill logga in med klienthemligheten, kan du skapa det i Azure AD. Kan också använda en webbapp eller API: et i stället för en inbyggd app. Lägg även till appen under **åtkomstkontroll (IAM)** i namnområdet som du skapade tidigare.
- `serviceBusNamespaceFQDN`: Ställ in på det fullständiga DNS-namnet på nyligen skapade Service Bus-namnområdet; till exempel `example.servicebus.windows.net`.
- `queueName`: Ange namnet på kön som du skapade.
- Omdirigerings-URI som du angav i din app i föregående steg.
 
När du kör konsolprogrammet, uppmanas du att välja ett scenario; Klicka på **interaktiv användarinloggning** genom att skriva dess nummer och trycka på RETUR. Programmet visas ett inloggningsfönster, begär tillstånd att komma åt Service Bus och sedan använder tjänsten för att gå igenom scenariot skicka och ta emot med hjälp av inloggningsidentitet.

## <a name="next-steps"></a>Nästa steg

I följande ämnen kan du lära dig mer om Service Bus-meddelanden.

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)