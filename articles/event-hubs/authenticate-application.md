---
title: Autentisera ett program för åtkomst till Azure Event Hubs-resurser
description: Den här artikeln innehåller information om hur du autentiserar ett program med Azure Active Directory för att komma åt Azure Event Hubs-resurser
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 4cef49f138b96848b8e59cb5b2d0b185d4568aa9
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521000"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Autentisera ett program med Azure Active Directory för att komma åt eventhubbar-resurser
Microsoft Azure tillhandahåller integrerad hantering av åtkomstkontroll för resurser och program som baseras på Azure Active Directory (Azure AD). En viktig fördel med att använda Azure AD med Azure Event Hubs är att du inte behöver lagra dina autentiseringsuppgifter i koden längre. I stället kan du begära en OAuth 2.0-åtkomsttoken från Microsoft Identity-plattformen. Resursnamnet som begär en `https://eventhubs.azure.net/` token är (För Kafka-klienter `https://<namespace>.servicebus.windows.net`är resursen att begära en token ). Azure AD autentiserar säkerhetsobjektet (en användare, grupp eller tjänsthuvudnamn) som kör programmet. Om autentiseringen lyckas returnerar Azure AD en åtkomsttoken till programmet och programmet kan sedan använda åtkomsttoken för att auktorisera begäran till Azure Event Hubs-resurser.

När en roll tilldelas ett Azure AD-säkerhetsobjekt beviljar Azure åtkomst till dessa resurser för det säkerhetsobjektet. Åtkomst kan begränsas till prenumerationsnivån, resursgruppen, namnområdet Event Hubs eller någon resurs under den. En Azure AD-säkerhet kan tilldela roller till en användare, en grupp, ett programtjänsthuvudnamn eller en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md). 

> [!NOTE]
> En rolldefinition är en samling behörigheter. Rollbaserad åtkomstkontroll (RBAC) styr hur dessa behörigheter tillämpas via rolltilldelning. En rolltilldelning består av tre delar: säkerhetsobjekt, rolldefinition och omfång. Mer information finns i [Förstå de olika rollerna](../role-based-access-control/overview.md).

## <a name="built-in-roles-for-azure-event-hubs"></a>Inbyggda roller för Azure Event Hubs
Azure tillhandahåller följande inbyggda RBAC-roller för att auktorisera åtkomst till eventhubbardata med Azure AD och OAuth:

- [Azure Event Hubs Data Owner](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Använd den här rollen för att ge fullständig åtkomst till eventhubbar-resurser.
- [Azure Event Hubs Data Sender](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): Använd den här rollen för att ge skicka åtkomst till eventhubbar-resurser.
- [Azure Event Hubs Data Receiver](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): Använd den här rollen för att ge mottagande åtkomst till eventhubbar-resurser.   

> [!IMPORTANT]
> Vår förhandsversion stöds lägga till Event Hubs dataåtkomstbehörighet till ägare eller deltagare roll. Dataåtkomstbehörigheter för rollen Ägare och Deltagare respekteras dock inte längre. Om du använder rollen Ägare eller Deltagare växlar du till att använda azure event hubs-dataägarrollen.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Tilldela RBAC-roller med Azure-portalen  
Mer information om hur du hanterar åtkomst till Azure-resurser med HJÄLP AV RBAC och Azure-portalen finns i den [här artikeln](..//role-based-access-control/role-assignments-portal.md). 

När du har fastställt lämpligt scope för en rolltilldelning navigerar du till den resursen i Azure-portalen. Visa inställningarna för åtkomstkontroll (IAM) för resursen och följ dessa instruktioner för att hantera rolltilldelningar:

> [!NOTE]
> Stegen nedan tilldelar en roll till din händelsehubb under namnområdena Event Hubs, men du kan följa samma steg för att tilldela en roll som begränsas till alla Event Hubs-resurser.

1. Navigera till namnområdet Event Hubs i [Azure-portalen.](https://portal.azure.com/)
2. På sidan **Översikt** väljer du den händelsehubb som du vill tilldela en roll för.

    ![Välj din händelsehubb](./media/authenticate-application/select-event-hub.png)
1. Välj **Åtkomstkontroll (IAM)** om du vill visa åtkomstkontrollinställningar för händelsehubben. 
1. Välj fliken **Rolltilldelningar** om du vill visa listan över rolltilldelningar. Markera knappen **Lägg till** i verktygsfältet och välj sedan Lägg **till rolltilldelning**. 

    ![Knappen Lägg till i verktygsfältet](./media/authenticate-application/role-assignments-add-button.png)
1. Gör följande på sidan **Lägg till rolltilldelning:**
    1. Välj den **roll för händelsehubbar** som du vill tilldela. 
    1. Sök för att hitta **det säkerhetsobjekt** (användare, grupp, tjänsthuvudnamn) som du vill tilldela rollen till.
    1. Välj **Spara** om du vill spara rolltilldelningen. 

        ![Tilldela en användare roll](./media/authenticate-application/assign-role-to-user.png)
    4. Identiteten som du har tilldelat rollen visas under den rollen. Följande avbildning visar till exempel att Azure-användare finns i azure event hubs dataägare. 
        
        ![Användare i listan](./media/authenticate-application/user-in-list.png)

Du kan följa liknande steg för att tilldela en roll som omfattas av namnområdet Event Hubs, resursgrupp eller prenumeration. När du har definierat rollen och dess omfattning kan du testa det här beteendet med exempel [på den här GitHub-platsen](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).


## <a name="authenticate-from-an-application"></a>Autentisera från ett program
En viktig fördel med att använda Azure AD med eventhubbar är att dina autentiseringsuppgifter inte längre behöver lagras i din kod. I stället kan du begära en OAuth 2.0-åtkomsttoken från Microsofts identitetsplattform. Azure AD autentiserar säkerhetsobjektet (en användare, en grupp eller tjänsthuvudnamn) som kör programmet. Om autentiseringen lyckas returnerar Azure AD åtkomsttoken till programmet och programmet kan sedan använda åtkomsttoken för att auktorisera begäranden till Azure Event Hubs.

I följande avsnitt visas hur du konfigurerar ditt inbyggda program eller webbprogram för autentisering med Microsoft identity platform 2.0. Mer information om Microsoft identity platform 2.0 finns i [översikt över Microsoft identity platform (v2.0).](../active-directory/develop/v2-overview.md)

En översikt över OAuth 2.0-kodbidragsflödet finns i [Auktorisera åtkomst till Azure Active Directory-webbprogram med hjälp av OAuth 2.0-kodbidragsflödet](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrera ditt program hos en Azure AD-klient
Det första steget i att använda Azure AD för att auktorisera Event Hubs-resurser registrerar ditt klientprogram med en Azure AD-klient från [Azure-portalen](https://portal.azure.com/). När du registrerar ditt klientprogram lämnar du information om programmet till AD. Azure AD tillhandahåller sedan ett klient-ID (kallas även ett program-ID) som du kan använda för att associera ditt program med Azure AD-körning. Mer information om klient-ID finns [i Program- och tjänsthuvudobjekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Följande bilder visar steg för att registrera ett webbprogram:

![Registrera ett program](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Om du registrerar ditt program som ett inbyggt program kan du ange en giltig URI för Redirect URI. För inbyggda program behöver det här värdet inte vara en riktig URL. För webbprogram måste omdirigerings-URI:n vara en giltig URI, eftersom den anger webbadressen till vilken token tillhandahålls.

När du har registrerat programmet visas **programmets (klient)-ID** under **Inställningar:**

![Ansökande-ID för den registrerade ansökan](./media/authenticate-application/application-id.png)

Mer information om hur du registrerar ett program med Azure AD finns i [Integrera program med Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md).


### <a name="create-a-client-secret"></a>Skapa en klienthemlighet   
Programmet behöver en klienthemlighet för att bevisa sin identitet när du begär en token. Så här lägger du till klienthemligheten.

1. Navigera till din appregistrering i Azure-portalen.
1. Välj inställningen **Certifikat & hemligheter.**
1. Under **Klienthemligheter**väljer du **Ny klienthemlighet** för att skapa en ny hemlighet.
1. Ange en beskrivning av hemligheten och välj önskat förfallointervall.
1. Kopiera omedelbart värdet på den nya hemligheten till en säker plats. Fyllningsvärdet visas bara en gång för dig.

    ![Klienthemlighet](./media/authenticate-application/client-secret.png)


### <a name="client-libraries-for-token-acquisition"></a>Klientbibliotek för tokenförvärv  
När du har registrerat ditt program och gett det behörighet att skicka/ta emot data i Azure Event Hubs kan du lägga till kod i ditt program för att autentisera ett säkerhetsobjekt och hämta OAuth 2.0-token. Om du vill autentisera och hämta token kan du använda antingen ett av [Microsofts identitetsplattformsautentiseringsbibliotek](../active-directory/develop/reference-v2-libraries.md) eller ett annat bibliotek med öppen källkod som stöder OpenID eller Connect 1.0. Ditt program kan sedan använda åtkomsttoken för att auktorisera en begäran mot Azure Event Hubs.

En lista över scenarier som det stöds att hämta token finns i avsnittet [Scenarier](https://aka.ms/msal-net-scenarios) i [MICROSOFT Authentication Library (MSAL) för .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub-databasen.

## <a name="samples"></a>Exempel
- [Exempel på Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Dessa exempel använder det gamla **Microsoft.Azure.EventHubs-biblioteket,** men du kan enkelt uppdatera det till det senaste **Azure.Messaging.EventHubs-biblioteket.** Information om hur du flyttar exemplet från att använda det gamla biblioteket till ett nytt finns i [guiden för att migrera från Microsoft.Azure.EventHubs till Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Exempel på Azure.Messaging.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Det här exemplet har uppdaterats för att använda det senaste **Azure.Messaging.EventHubs-biblioteket.**

## <a name="next-steps"></a>Nästa steg
- Mer information om RBAC finns i [Vad är rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md)?
- Mer information om hur du tilldelar och hanterar RBAC-rolltilldelningar med Azure PowerShell, Azure CLI eller REST API finns i följande artiklar:
    - [Hantera rollbaserad åtkomstkontroll (RBAC) med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Hantera rollbaserad åtkomstkontroll (RBAC) med Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Hantera rollbaserad åtkomstkontroll (RBAC) med REST API](../role-based-access-control/role-assignments-rest.md)
    - [Hantera rollbaserad åtkomstkontroll (RBAC) med Azure Resource Manager-mallar](../role-based-access-control/role-assignments-template.md)

Se följande relaterade artiklar:
- [Autentisera en hanterad identitet med Azure Active Directory för att komma åt eventhubbarresurser](authenticate-managed-identity.md)
- [Autentisera begäranden till Azure Event Hubs med signaturer för delad åtkomst](authenticate-shared-access-signature.md)
- [Auktorisera åtkomst till eventhubbar-resurser med Azure Active Directory](authorize-access-azure-active-directory.md)
- [Auktorisera åtkomst till eventhubbar-resurser med signaturer för delad åtkomst](authorize-access-shared-access-signature.md)

