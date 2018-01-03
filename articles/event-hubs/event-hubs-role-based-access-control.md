---
title: "Förhandsversionen av Azure Event Hubs Role-Based åtkomstkontroll (RBAC) | Microsoft Docs"
description: "Azure Event Hubs rollbaserad åtkomstkontroll"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 0d3a779eb2cccf242bcd42d82c1a90048b3512ab
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2017
---
# <a name="active-directory-role-based-access-control-preview"></a>Åtkomstkontroll för aktiva Directory Role-Based (förhandsgranskning)

Microsoft Azure tillhandahåller integrerad åtkomstkontrollhantering för resurser och program som baseras på Azure Active Directory (AD Azure). Med Azure AD, kan du hantera användarkonton program specifikt för din Azure-baserade program och du kan federera den befintliga Active Directory-infrastrukturen med Azure AD för hela företaget single-sign-on också sträcker sig över flera Azure-resurser och Azure program. Du kan sedan tilldela dessa Azure AD-användare och programmet identiteter global och tjänstspecifika roller för att bevilja åtkomst till Azure-resurser.

För Azure Event Hubs hanteringen av namnområden och alla relaterade resurser via Azure-portalen och Azure resource Manager API redan är skyddat med hjälp av den *rollbaserad åtkomstkontroll* (RBAC)-modellen. RBAC för körning åtgärder är en funktion som är nu tillgänglig som förhandsversion. 

Ett program som använder Azure AD RBAC behöver inte hantera SAS-regler och nycklar eller andra åtkomsttoken för Händelsehubbar. Klientappen interagerar med Azure AD för att upprätta en autentiseringskontext och hämtar en åtkomst-token för Händelsehubbar. Med domänanvändarkonton som kräver interaktiv inloggning, hanterar programmet aldrig alla eventuella autentiseringsuppgifter direkt.

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs roller och behörigheter

För inledande förhandsversion, du kan endast lägga till Azure AD-konton och tjänstens huvudnamn ”ägare” eller ”bidragsgivare” rollerna i ett namnområde för Händelsehubbar. Den här åtgärden ger identitet fullständig kontroll över alla entiteter i namnområdet. Management-åtgärder som ändrar namnområde topologin är från början bara stöds även om Azure resource Manager och inte via gränssnittet för hantering av inbyggda Event Hub REST. Det här stödet innebär att även .NET Framework-klienten [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objektet kan inte användas med ett konto i Azure AD.  

## <a name="use-event-hubs-with-an-azure-ad-domain-user-account"></a>Använda Händelsehubbar med ett domänanvändarkonto för Azure AD

I följande avsnitt beskrivs de steg som krävs för att skapa och köra ett exempelprogram som efterfrågar en interaktiv Azure AD-användare att logga in, hur du ger Händelsehubbar åtkomst till det aktuella användarkontot och hur du använder identitet för åtkomst till Händelsehubbar. 

Den här introduktionen beskriver ett enkelt konsolprogram i [kod som finns på Github](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Rbac/EventHubsSenderReceiverRbac/)

### <a name="create-an-active-directory-user-account"></a>Skapa ett användarkonto i Active Directory

Det här första steget är valfritt. Varje Azure-prenumeration automatiskt paras ihop med en Azure Active Directory-klient och om du har åtkomst till en Azure-prenumeration, ditt användarkonto har redan registrerats. Det innebär att du kan bara använda ditt konto. 

Om du vill skapa ett särskilt konto för det här scenariot [gör du följande](../automation/automation-create-aduser-account.md). Du måste ha behörighet att skapa konton i Azure Active Directory-klient, vilket inte kanske är fallet för större företagsscenarier.

### <a name="create-an-event-hubs-namespace"></a>Skapa ett namnområde för Händelsehubbar

Nästa [skapa ett namnområde för Händelsehubbar](event-hubs-create.md) i ett Azure-regioner som har stöd för Händelsehubbar förhandsgranskning för RBAC: **oss Öst**, **oss östra 2**, eller **västra Europa** . 

När namnområdet har skapats, navigera till dess **Access Control (IAM)** på portalen, och klickar sedan på **Lägg till** att lägga till Azure AD-användarkontot i rollen som ägare. Om du använder ditt eget konto och du har skapat namnområdet, är du redan rollen ägare. Om du vill lägga till ett annat konto för rollen, söka efter namnet på webbprogrammet på **lägga till behörigheter** panelen **Välj** fält och klicka på posten. Klicka sedan på **Spara**.
 
![](./media/event-hubs-role-based-access-control/rbac1.PNG)

Användarkontot har nu tillgång till namnområdet Händelsehubbar och till händelsehubben du skapade tidigare.
 
### <a name="register-the-application"></a>Registrera programmet

Innan du kan köra exempelprogrammet måste registrera den i Azure AD och godkänna medgivandetext som tillåter programmet åtkomst Händelsehubbar i dess ställe. 

Eftersom exempelprogrammet är ett konsolprogram, måste du registrera det ursprungliga programmet och lägga till API-behörigheter för **Microsoft.EventHub** till uppsättningen med ”nödvändiga behörigheter”. Interna program behöver också en **omdirigerings-URI** i Azure AD som fungerar som en identifierare; URI: N behöver inte vara ett mål i nätverket. Använd `http://eventhubs.microsoft.com` eftersom exemplet code redan i det här exemplet använder du den URI.

Detaljerad registrering stegen beskrivs i [självstudierna](../active-directory/develop/active-directory-integrating-applications.md). Följ stegen för att registrera en **interna** app, och följ sedan instruktionerna update att lägga till den **Microsoft.EventHub** API för att behörigheterna som krävs. När du följer stegen anteckna den **TenantId** och **ApplicationId**, eftersom du måste dessa värden för att köra programmet.

### <a name="run-the-app"></a>Kör appen

Innan du kan köra det här exemplet redigera filen App.config och, beroende på scenario, anger du följande värden:

- `tenantId`: Ange **TenantId** värde.
- `clientId`: Ange **ApplicationId** värde. 
- `clientSecret`: Om du vill logga in med klienthemligheten kan du skapa den i Azure AD. Använd en webbapp eller API i stället för en intern app. Lägg även till appen under **Access Control (IAM)** i namnområdet som du skapade tidigare.
- `eventHubNamespaceFQDN`: Ange som fullständig DNS-namnet på namnområdet nyskapade Händelsehubbar; till exempel `example.servicebus.windows.net`.
- `eventHubName`: Ange namnet på den event hub du skapat.
- Omdirigerings-URI som du angav i din app i föregående steg.
 
När du kör konsolprogrammet, uppmanas du att välja ett scenario; Klicka på **interaktiv användarinloggning** genom att skriva dess värde och trycka på RETUR. Programmet visas ett inloggningsfönster, uppmanas du att ange ditt medgivande till Händelsehubbar och använder tjänsten för att gå via ett scenario för skicka och ta emot med inloggningen identitet.

## <a name="next-steps"></a>Nästa steg

Besök följande länkar för mer utförlig information om Event Hubs:

* Kom igång med en [kurs om händelsehubbar](event-hubs-dotnet-standard-getstarted-send.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Priser för Händelsehubbar](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)