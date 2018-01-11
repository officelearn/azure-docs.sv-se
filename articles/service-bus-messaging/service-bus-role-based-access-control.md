---
title: "Förhandsversionen av Azure Service Bus Role-Based åtkomstkontroll (RBAC) | Microsoft Docs"
description: "Azure Service Bus-rollbaserad åtkomstkontroll"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 729d6db6b2fc6495ffb0f4fbe4d545d7ad953cef
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2017
---
# <a name="active-directory-role-based-access-control-preview"></a>Åtkomstkontroll för aktiva Directory Role-Based (förhandsgranskning)

Microsoft Azure tillhandahåller integrerad åtkomstkontrollhantering för resurser och program som baseras på Azure Active Directory (AD Azure). Med Azure AD, kan du hantera användarkonton program specifikt för din Azure-baserade program och du kan federera den befintliga Active Directory-infrastrukturen med Azure AD för hela företaget single-sign-on också sträcker sig över flera Azure-resurser och Azure program. Du kan sedan tilldela dessa Azure AD-användare och programmet identiteter global och tjänstspecifika roller för att bevilja åtkomst till Azure-resurser.

För Azure Service Bus hanteringen av namnområden och alla relaterade resurser via Azure-portalen och Azure resource Manager API redan är skyddat med hjälp av den *rollbaserad åtkomstkontroll* (RBAC)-modellen. RBAC för körning åtgärder är en funktion som är nu tillgänglig som förhandsversion. 

Ett program som använder Azure AD RBAC behöver inte hantera SAS-regler och nycklar eller andra åtkomsttoken specifika för Service Bus. Klientappen interagerar med Azure AD för att upprätta en autentiseringskontext och hämtar en åtkomst-token för Service Bus. Med domänanvändarkonton som kräver interaktiv inloggning, hanterar programmet aldrig alla eventuella autentiseringsuppgifter direkt.

## <a name="service-bus-roles-and-permissions"></a>Service Bus-roller och behörigheter

För inledande förhandsversion, du kan bara lägga till Azure AD-konton och tjänstens huvudnamn ”ägare” eller ”bidragsgivare” rollerna i ett namnområde för Service Bus-meddelanden. Den här åtgärden ger identitet fullständig kontroll över alla entiteter i namnområdet. Management-åtgärder som ändrar namnområde topologin är från början bara stöds även om Azure resource Manager och inte via gränssnittet för hantering av interna Service Bus REST. Det här stödet innebär att även .NET Framework-klienten [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objektet kan inte användas med ett konto i Azure AD.  

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>Använda Service Bus med ett domänanvändarkonto för Azure AD

I följande avsnitt beskrivs de steg som krävs för att skapa och köra ett exempelprogram som efterfrågar en interaktiv Azure AD-användare att logga in, hur du ger Service Bus-åtkomst till det aktuella användarkontot och hur du använder identitet för åtkomst till Händelsehubbar. 

Den här introduktionen beskriver ett enkelt konsolprogram i [kod som finns på Github](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).

### <a name="create-an-active-directory-user-account"></a>Skapa ett användarkonto i Active Directory

Det här första steget är valfritt. Varje Azure-prenumeration automatiskt paras ihop med en Azure Active Directory-klient och om du har åtkomst till en Azure-prenumeration, ditt användarkonto har redan registrerats. Det innebär att du kan bara använda ditt konto. 

Om du vill skapa ett särskilt konto för det här scenariot [gör du följande](../automation/automation-create-aduser-account.md). Du måste ha behörighet att skapa konton i Azure Active Directory-klient, vilket inte kanske är fallet för större företagsscenarier.

### <a name="create-a-service-bus-namespace"></a>Skapa ett namnområde för Service Bus

Nästa [skapa ett namnområde för Service Bus-meddelanden](service-bus-create-namespace-portal.md) i ett Azure-regioner som har stöd för förhandsgranskning för RBAC: **oss Öst**, **oss östra 2**, eller **västra Europa** . 

När namnområdet har skapats, navigera till dess **Access Control (IAM)** på portalen, och klickar sedan på **Lägg till** att lägga till Azure AD-användarkontot i rollen som ägare. Om du använder ditt eget konto och du har skapat namnområdet, är du redan rollen ägare. Om du vill lägga till ett annat konto för rollen, söka efter namnet på webbprogrammet på **lägga till behörigheter** panelen **Välj** fält och klicka på posten. Klicka sedan på **Spara**.

![](./media/service-bus-role-based-access-control/rbac1.PNG)

Användarkontot har nu tillgång till Service Bus-namnrymd och till kön du skapade tidigare.
 
### <a name="register-the-application"></a>Registrera programmet

Innan du kan köra exempelprogrammet måste registrera den i Azure AD och godkänna medgivandetext som tillåter programmet att få åtkomst till Azure Service Bus i dess ställe. 

Eftersom exempelprogrammet är ett konsolprogram, måste du registrera det ursprungliga programmet och lägga till API-behörigheter för **Microsoft.ServiceBus** till uppsättningen med ”nödvändiga behörigheter”. Interna program behöver också en **omdirigerings-URI** i Azure AD som fungerar som en identifierare; URI: N behöver inte vara ett mål i nätverket. Använd `http://servicebus.microsoft.com` eftersom exemplet code redan i det här exemplet använder du den URI.

Detaljerad registrering stegen beskrivs i [självstudierna](../active-directory/develop/active-directory-integrating-applications.md). Följ stegen för att registrera en **interna** app, och följ sedan instruktionerna update att lägga till den **Microsoft.ServiceBus** API för att behörigheterna som krävs. När du följer stegen anteckna den **TenantId** och **ApplicationId**, eftersom du måste dessa värden för att köra programmet.

### <a name="run-the-app"></a>Kör appen

Innan du kan köra det här exemplet redigera filen App.config och, beroende på scenario, anger du följande värden:

- `tenantId`: Ange **TenantId** värde.
- `clientId`: Ange **ApplicationId** värde. 
- `clientSecret`: Om du vill logga in med klienthemligheten kan du skapa den i Azure AD. Använd en webbapp eller API i stället för en intern app. Lägg även till appen under **Access Control (IAM)** i namnområdet som du skapade tidigare.
- `serviceBusNamespaceFQDN`: Inställd på fullständig DNS-namnet på den nyligen skapade Service Bus-namnrymden; till exempel `example.servicebus.windows.net`.
- `queueName`: Ange namnet på den kö som du skapade.
- Omdirigerings-URI som du angav i din app i föregående steg.
 
När du kör konsolprogrammet, uppmanas du att välja ett scenario; Klicka på **interaktiv användarinloggning** genom att skriva dess värde och trycka på RETUR. Programmet visas ett inloggningsfönster, uppmanas du att ange ditt medgivande till Service Bus och använder tjänsten för att gå via ett scenario för skicka och ta emot med inloggningen identitet.

## <a name="next-steps"></a>Nästa steg

I följande ämnen kan du lära dig mer om Service Bus-meddelanden.

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)