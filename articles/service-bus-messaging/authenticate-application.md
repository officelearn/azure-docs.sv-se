---
title: Autentisera ett program för åtkomst till Azure Service Bus-entiteter
description: Den här artikeln innehåller information om hur du autentiserar ett program med Azure Active Directory för att komma åt Azure Service Bus-entiteter (köer, ämnen osv.)
services: service-bus-messaging
ms.service: event-hubs
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 6a78e4d81921fae8dcb325e9d72df1eee7b99a3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259297"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Autentisera och auktorisera ett program med Azure Active Directory för åtkomst till Azure Service Bus-entiteter
Azure Service Bus stöder användning av Azure Active Directory (Azure AD) för att auktorisera begäranden till Service Bus-entiteter (köer, ämnen, prenumerationer eller filter). Med Azure AD kan du använda rollbaserad åtkomstkontroll (RBAC) för att bevilja behörigheter till ett säkerhetsobjekt, som kan vara ett användar-, grupp- eller programtjänsthuvudnamn. Mer information om roller och rolltilldelningar finns i [Förstå de olika rollerna](../role-based-access-control/overview.md).

## <a name="overview"></a>Översikt
När ett säkerhetsobjekt (en användare, grupp eller ett program) försöker komma åt en Service Bus-enhet måste begäran godkännas. Med Azure AD är åtkomst till en resurs en tvåstegsprocess. 

 1. Först autentiseras säkerhetsobjektets identitet och en OAuth 2.0-token returneras. Resursnamnet som begär en `https://servicebus.azure.net`token är .
 1. Därefter skickas token som en del av en begäran till servicebusstjänsten om att auktorisera åtkomst till den angivna resursen.

Autentiseringssteget kräver att en programbegäran innehåller en OAuth 2.0-åtkomsttoken vid körning. Om ett program körs inom en Azure-entitet, till exempel en Azure VM, en skaluppsättning för virtuella datorer eller en Azure Function-app, kan den använda en hanterad identitet för att komma åt resurserna. Mer information om hur du autentiserar begäranden som gjorts av en hanterad identitet till Service Bus-tjänsten finns i [Autentisera åtkomst till Azure Service Bus-resurser med Azure Active Directory och hanterade identiteter för Azure Resources](service-bus-managed-service-identity.md). 

Auktoriseringssteget kräver att en eller flera RBAC-roller tilldelas säkerhetsobjektet. Azure Service Bus tillhandahåller RBAC-roller som omfattar behörighetsuppsättningar för Service Bus-resurser. De roller som tilldelas ett säkerhetsobjekt avgör vilka behörigheter huvudmannen ska ha. Mer information om hur du tilldelar RBAC-roller till Azure Service Bus finns [i Inbyggda RBAC-roller för Azure Service Bus](#built-in-rbac-roles-for-azure-service-bus). 

Inbyggda program och webbprogram som gör förfrågningar till Service Bus kan också auktorisera med Azure AD. Den här artikeln visar hur du begär en åtkomsttoken och använder den för att auktorisera begäranden om Service Bus-resurser. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Tilldela RBAC-roller för åtkomsträttigheter
Azure Active Directory (Azure AD) godkänner åtkomsträttigheter till skyddade resurser via [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md). Azure Service Bus definierar en uppsättning inbyggda RBAC-roller som omfattar vanliga behörighetsuppsättningar som används för att komma åt Service Bus-entiteter och du kan också definiera anpassade roller för åtkomst till data.

När en RBAC-roll tilldelas ett Azure AD-säkerhetsobjekt beviljar Azure åtkomst till dessa resurser för det säkerhetsobjektet. Åtkomst kan begränsas till prenumerationsnivån, resursgruppen eller servicebussnamnområdet. Ett Azure AD-säkerhetsobjekt kan vara en användare, en grupp, ett programtjänsthuvudnamn eller en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Inbyggda RBAC-roller för Azure Service Bus
För Azure Service Bus är hanteringen av namnområden och alla relaterade resurser via Azure-portalen och Azure-resurshanterings-API:et redan skyddad med hjälp av den *rollbaserade åtkomstkontrollmodellen* (RBAC). Azure tillhandahåller nedanstående inbyggda RBAC-roller för att auktorisera åtkomst till ett servicebussnamnområde:

- [Azure Service Bus Data Owner](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Aktiverar dataåtkomst till Service Bus-namnområdet och dess entiteter (köer, ämnen, prenumerationer och filter)
- [Azure Service Bus Data Sender](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Använd den här rollen för att ge skicka åtkomst till Service Bus-namnområde och dess entiteter.
- [Azure Service Bus Data Receiver](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Använd den här rollen för att ge mottagande åtkomst till Service Bus-namnområde och dess entiteter. 

## <a name="resource-scope"></a>Resursomfattning 
Innan du tilldelar en RBAC-roll till ett säkerhetsobjekt bestämmer du omfattningen av åtkomsten som säkerhetsobjektet ska ha. Bästa praxis dikterar att det alltid är bäst att bara bevilja minsta möjliga omfattning.

I följande lista beskrivs de nivåer där du kan begränsa åtkomsten till Service Bus-resurser, med början med det smalaste omfånget:

- **Kö,** **ämne**eller **prenumeration**: Rolltilldelning gäller för den specifika Service Bus-entiteten. Azure-portalen stöder för närvarande inte tilldelning av användare/grupper/hanterade identiteter till Service Bus RBAC-roller på prenumerationsnivå. 
- **Service Bus namnområde**: Rolltilldelning sträcker sig över hela topologin för Service Bus under namnområdet och till konsumentgruppen som är associerad med den.
- **Resursgrupp**: Rolltilldelning gäller för alla Service Bus-resurser under resursgruppen.
- **Prenumeration**: Rolltilldelning gäller för alla Service Bus-resurser i alla resursgrupper i prenumerationen.

> [!NOTE]
> Tänk på att RBAC-rolltilldelningar kan ta upp till fem minuter att sprida. 

Mer information om hur inbyggda roller definieras finns i [Förstå rolldefinitioner](../role-based-access-control/role-definitions.md#management-and-data-operations). Information om hur du skapar anpassade RBAC-roller finns i [Skapa anpassade roller för Azure Role-Based Access Control](../role-based-access-control/custom-roles.md).


## <a name="assign-rbac-roles-using-the-azure-portal"></a>Tilldela RBAC-roller med Azure-portalen  
Mer information om hur du hanterar åtkomst till Azure-resurser med HJÄLP AV RBAC och Azure-portalen finns i den [här artikeln](..//role-based-access-control/role-assignments-portal.md). 

När du har fastställt lämpligt scope för en rolltilldelning navigerar du till den resursen i Azure-portalen. Visa inställningarna för åtkomstkontroll (IAM) för resursen och följ dessa instruktioner för att hantera rolltilldelningar:

> [!NOTE]
> Stegen som beskrivs nedan tilldelar en roll till din Service Bus-namnområde. Du kan följa samma steg för att tilldela en roll till andra scope som stöds (resursgrupp, prenumeration osv.).

1. Navigera till namnområdet Service Bus i [Azure-portalen.](https://portal.azure.com/) Välj **Access Control (IAM)** på den vänstra menyn för att visa åtkomstkontrollinställningar för namnområdet. Om du behöver skapa ett servicebussnamnområde följer du instruktionerna från den här artikeln: [Skapa ett namnområde för Service Bus Messaging](service-bus-create-namespace-portal.md).

    ![Välj Åtkomstkontroll på vänster meny](./media/authenticate-application/select-access-control-menu.png)
1. Välj fliken **Rolltilldelningar** om du vill visa listan över rolltilldelningar. Markera knappen **Lägg till** i verktygsfältet och välj sedan Lägg **till rolltilldelning**. 

    ![Knappen Lägg till i verktygsfältet](./media/authenticate-application/role-assignments-add-button.png)
1. Gör följande på sidan **Lägg till rolltilldelning:**
    1. Välj den **servicebussroll** som du vill tilldela. 
    1. Sök för att hitta **det säkerhetsobjekt** (användare, grupp, tjänsthuvudnamn) som du vill tilldela rollen till.
    1. Välj **Spara** om du vill spara rolltilldelningen. 

        ![Tilldela en användare roll](./media/authenticate-application/assign-role-to-user.png)
    4. Identiteten som du har tilldelat rollen visas under den rollen. Följande avbildning visar till exempel att Azure-användare finns i azure service bus data ägare roll. 
        
        ![Användare i listan](./media/authenticate-application/user-in-list.png)

Du kan följa liknande steg för att tilldela en roll som omfattas av en resursgrupp eller en prenumeration. När du har definierat rollen och dess omfattning kan du testa det här beteendet med [exemplen på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).


## <a name="authenticate-from-an-application"></a>Autentisera från ett program
En viktig fördel med att använda Azure AD med Service Bus är att dina autentiseringsuppgifter inte längre behöver lagras i din kod. I stället kan du begära en OAuth 2.0-åtkomsttoken från Microsofts identitetsplattform. Azure AD autentiserar säkerhetsobjektet (en användare, en grupp eller tjänsthuvudnamn) som kör programmet. Om autentiseringen lyckas returnerar Azure AD åtkomsttoken till programmet och programmet kan sedan använda åtkomsttoken för att auktorisera begäranden till Azure Service Bus.

I följande avsnitt visas hur du konfigurerar ditt inbyggda program eller webbprogram för autentisering med Microsoft identity platform 2.0. Mer information om Microsoft identity platform 2.0 finns i [översikt över Microsoft identity platform (v2.0).](../active-directory/develop/v2-overview.md)

En översikt över OAuth 2.0-kodbidragsflödet finns i [Auktorisera åtkomst till Azure Active Directory-webbprogram med hjälp av OAuth 2.0-kodbidragsflödet](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrera ditt program hos en Azure AD-klient
Det första steget i att använda Azure AD för att auktorisera Service Bus-entiteter registrerar ditt klientprogram med en Azure AD-klientorganisation från [Azure-portalen](https://portal.azure.com/). När du registrerar ditt klientprogram lämnar du information om programmet till AD. Azure AD tillhandahåller sedan ett klient-ID (kallas även ett program-ID) som du kan använda för att associera ditt program med Azure AD-körning. Mer information om klient-ID finns [i Program- och tjänsthuvudobjekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Följande bilder visar steg för att registrera ett webbprogram:

![Registrera ett program](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Om du registrerar ditt program som ett inbyggt program kan du ange en giltig URI för Redirect URI. För inbyggda program behöver det här värdet inte vara en riktig URL. För webbprogram måste omdirigerings-URI:n vara en giltig URI, eftersom den anger webbadressen till vilken token tillhandahålls.

När du har registrerat programmet visas **programmets (klient)-ID** under **Inställningar:**

![Ansökande-ID för den registrerade ansökan](./media/authenticate-application/application-id.png)

Mer information om hur du registrerar ett program med Azure AD finns i [Integrera program med Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md).

> [!IMPORTANT]
> Anteckna **TenantId** och **ApplicationId**. Du behöver dessa värden för att köra programmet.

### <a name="create-a-client-secret"></a>Skapa en klienthemlighet   
Programmet behöver en klienthemlighet för att bevisa sin identitet när du begär en token. Så här lägger du till klienthemligheten.

1. Navigera till din appregistrering i Azure-portalen om du inte redan finns på sidan.
1. Välj **Certifikat & hemligheter** på den vänstra menyn.
1. Under **Klienthemligheter**väljer du **Ny klienthemlighet** för att skapa en ny hemlighet.

    ![Ny klient hemlighet - knappen](./media/authenticate-application/new-client-secret-button.png)
1. Ange en beskrivning av hemligheten och välj önskat förfallointervall och välj sedan **Lägg till**.

    ![Lägg till klienthemlighetssida](./media/authenticate-application/add-client-secret-page.png)
1. Kopiera omedelbart värdet på den nya hemligheten till en säker plats. Fyllningsvärdet visas bara en gång för dig.

    ![Klienthemlighet](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Behörigheter för Service Bus API
Om ditt program är ett konsolprogram måste du registrera ett inbyggt program och lägga till API-behörigheter för **Microsoft.ServiceBus** i den **behörighetsuppsättning som krävs.** Inbyggda program behöver också en **omdirigering-URI** i Azure AD, som fungerar som en identifierare. URI behöver inte vara ett nätverksmål. Använd `https://servicebus.microsoft.com` för det här exemplet eftersom exempelkoden redan använder URI:n.

### <a name="client-libraries-for-token-acquisition"></a>Klientbibliotek för tokenförvärv  
När du har registrerat ditt program och gett det behörighet att skicka/ta emot data i Azure Service Bus kan du lägga till kod i ditt program för att autentisera ett säkerhetsobjekt och hämta OAuth 2.0-token. Om du vill autentisera och hämta token kan du använda antingen ett av [Microsofts identitetsplattformsautentiseringsbibliotek](../active-directory/develop/reference-v2-libraries.md) eller ett annat bibliotek med öppen källkod som stöder OpenID eller Connect 1.0. Ditt program kan sedan använda åtkomsttoken för att auktorisera en begäran mot Azure Service Bus.

En lista över scenarier som det stöds att hämta token finns i avsnittet [Scenarier](https://aka.ms/msal-net-scenarios) i [MICROSOFT Authentication Library (MSAL) för .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub-databasen.

## <a name="sample-on-github"></a>Exempel på GitHub
Se följande exempel på GitHub: [Åtkomstkontroll för rollbas för Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl). 

Använd alternativet **Klienthemlig inloggning,** inte alternativet **Interaktiv användarlogin.** När du använder alternativet klienthemlighet visas inget popup-fönster. Programmet använder klient-ID och app-ID för autentisering. 

### <a name="run-the-sample"></a>Kör exemplet

Innan du kan köra exemplet redigerar du **filen app.config** och ställer in följande värden, beroende på scenariot:

- `tenantId`: Ange till **TenantId-värde.**
- `clientId`: Ange till **ApplicationId-värde.**
- `clientSecret`: Om du vill logga in med klienthemligheten skapar du den i Azure AD. Använd också en webbapp eller ett API i stället för en inbyggd app. Lägg också till appen under **Åtkomstkontroll (IAM)** i namnområdet som du tidigare skapade.
- `serviceBusNamespaceFQDN`: Ange det fullständiga DNS-namnet på det nyskapade servicebussnamnområdet. till exempel `example.servicebus.windows.net`.
- `queueName`: Ange namnet på kön som du skapade.
- Den omdirigerings-URI som du angav i appen i föregående steg.

När du kör konsolprogrammet uppmanas du att välja ett scenario. Välj **Interaktiv användarlogin** genom att skriva numret och trycka på RETUR. Programmet visar ett inloggningsfönster, ber om ditt samtycke till att komma åt Service Bus och använder sedan tjänsten för att köra igenom scenariot skicka/ta emot med hjälp av inloggningsidentiteten.


## <a name="next-steps"></a>Nästa steg
- Mer information om RBAC finns i [Vad är rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md)?
- Mer information om hur du tilldelar och hanterar RBAC-rolltilldelningar med Azure PowerShell, Azure CLI eller REST API finns i följande artiklar:
    - [Hantera rollbaserad åtkomstkontroll (RBAC) med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Hantera rollbaserad åtkomstkontroll (RBAC) med Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Hantera rollbaserad åtkomstkontroll (RBAC) med REST API](../role-based-access-control/role-assignments-rest.md)
    - [Hantera rollbaserad åtkomstkontroll (RBAC) med Azure Resource Manager-mallar](../role-based-access-control/role-assignments-template.md)

I följande ämnen kan du lära dig mer om Service Bus-meddelanden.

- [RBAC-exempel för servicebuss](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
- [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)
- [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
