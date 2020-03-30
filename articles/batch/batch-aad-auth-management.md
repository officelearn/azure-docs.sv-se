---
title: Använda Azure Active Directory för att autentisera batchhanteringslösningar
description: Utforska med Azure Active Directory för att autentisera från program som använder BATCH Management .NET-biblioteket.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/27/2017
ms.author: labrenne
ms.openlocfilehash: 5c217971bd213c97a2ee31a0a1f513b601d14df9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472987"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Autentisera batchhanteringslösningar med Active Directory

Program som anropar Azure Batch Management-tjänsten autentiseras med [Azure Active Directory][aad_about] (Azure AD). Azure AD är Microsofts molnbaserade katalog- och identitetshanteringstjänst för flera innehavare. Azure använder azure AD för autentisering av sina kunder, tjänstadministratörer och organisationsanvändare.

Batchhantering .NET-biblioteket visar typer för arbete med batchkonton, kontonycklar, program och programpaket. Batch Management .NET-biblioteket är en Azure-resursproviderklient och används tillsammans med [Azure Resource Manager][resman_overview] för att hantera dessa resurser programmässigt. Azure AD krävs för att autentisera begäranden som görs via en Azure-resursproviderklient, inklusive Batch Management .NET-biblioteket och via [Azure Resource Manager][resman_overview].

I den här artikeln utforskar vi hur du använder Azure AD för att autentisera från program som använder BATCH Management .NET-biblioteket. Vi visar hur du använder Azure AD för att autentisera en prenumerationsadministratör eller medadministratör med hjälp av integrerad autentisering. Vi använder exempelprojektet [AccountManagement,][acct_mgmt_sample] som är tillgängligt på GitHub, för att gå igenom med Azure AD med batchhantering .NET-biblioteket.

Mer information om hur du använder BATCHHantering .NET-biblioteket och exemplet AccountManagement finns i [Hantera batchkonton och kvoter med batchhanteringsklientbiblioteket för .NET](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Registrera ditt program med Azure AD

Azure [Active Directory Authentication Library][aad_adal] (ADAL) tillhandahåller ett programmatiskt gränssnitt till Azure AD för användning i dina program. Om du vill ringa ADAL från ditt program måste du registrera ditt program i en Azure AD-klientorganisation. När du registrerar ditt program förser du Azure AD med information om ditt program, inklusive ett namn för det i Azure AD-klienten. Azure AD tillhandahåller sedan ett program-ID som du använder för att associera ditt program med Azure AD vid körning. Mer information om program-ID finns [i Program- och tjänsthuvudobjekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Om du vill registrera exempelprogrammet AccountManagement följer du anvisningarna i avsnittet [Lägga till ett program](../active-directory/develop/quickstart-register-app.md) i Integrera program med Azure Active [Directory][aad_integrate]. Ange **native klientprogram** för typen av program. Branschstandarden OAuth 2.0 URI för `urn:ietf:wg:oauth:2.0:oob` **Redirect URI** är . Du kan dock ange en giltig `http://myaccountmanagementsample`URI (till exempel ) för **Redirect URI**, eftersom det inte behöver vara en riktig slutpunkt:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

När du har slutfört registreringsprocessen visas program-ID:t och objektets (tjänstens huvudnamn) id som anges för ditt program.  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Bevilja Azure Resource Manager-API-åtkomst till ditt program

Därefter måste du delegera åtkomst till ditt program till Azure Resource Manager API. Azure AD-identifieraren för Resurshanterarens API är **Windows Azure Service Management API**.

Följ dessa steg i Azure-portalen:

1. I det vänstra navigeringsfönstret i Azure-portalen väljer du **Alla tjänster,** klickar på **Appregistreringar**och klickar på **Lägg till**.
2. Sök efter namnet på ditt program i listan över appregistreringar:

    ![Sök efter ditt programnamn](./media/batch-aad-auth-management/search-app-registration.png)

3. Visa **bladet Inställningar.** I avsnittet **API Access** väljer du **Krävs behörigheter**.
4. Klicka på **Lägg till** om du vill lägga till en ny behörighet som krävs. 
5. I steg 1 anger du **Windows Azure Service Management API**, väljer det API:et i resultatlistan och klickar på knappen **Välj.**
6. I steg 2 markerar du kryssrutan bredvid Åtkomst till **Azure klassisk distributionsmodell som organisationsanvändare**och klickar på knappen **Välj.**
7. Klicka på knappen **Klar.**

Bladet **Nödvändiga behörigheter** visar nu att behörigheter till ditt program beviljas både ADAL- och Resource Manager-API:erna. Behörigheter beviljas ADAL som standard när du först registrerar din app med Azure AD.

![Delegera behörigheter till Azure Resource Manager-API:et](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Slutpunkter för Azure AD

För att autentisera dina batchhanteringslösningar med Azure AD behöver du två välkända slutpunkter.

- **Den gemensamma slutpunkten för Azure AD** tillhandahåller ett allmänt autentiseringsuppgifter insamlingsgränssnitt när en viss klient inte tillhandahålls, som i fallet med integrerad autentisering:

    `https://login.microsoftonline.com/common`

- **Slutpunkten för Azure Resource Manager** används för att hämta en token för att autentisera begäranden till batchhanteringstjänsten:

    `https://management.core.windows.net/`

Exempelprogrammet AccountManagement definierar konstanter för dessa slutpunkter. Lämna dessa konstanter oförändrade:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Referera till ditt program-ID 

Klientprogrammet använder program-ID :t (kallas även klient-ID) för att komma åt Azure AD vid körning. När du har registrerat ditt program i Azure-portalen uppdaterar du koden för att använda program-ID:t som tillhandahålls av Azure AD för ditt registrerade program. Kopiera ditt program-ID från Azure-portalen till lämplig konstant i exempelprogrammet AccountManagement:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Graph API. For information
// about registering an application in Azure Active Directory, please see "Register an application with the Microsoft identity platform" here:
// https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app
private const string ClientId = "<application-id>";
```
Kopiera även den omdirigera URI som du angav under registreringsprocessen. Den omdirigerings-URI som anges i koden måste matcha den omdirigera URI som du angav när du registrerade programmet.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Hämta en Azure AD-autentiseringstoken

När du har registrerat exemplet AccountManagement i Azure AD-klienten och uppdaterat exempelkod med dina värden, är exemplet redo att autentiseras med Azure AD. När du kör exemplet försöker ADAL hämta en autentiseringstoken. I det här steget uppmanas du att ange dina Microsoft-autentiseringsuppgifter: 

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

När du har ange dina autentiseringsuppgifter kan exempelprogrammet fortsätta att utfärda autentiserade begäranden till batchhanteringstjänsten. 

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kör [exempelprogrammet AccountManagement][acct_mgmt_sample]finns i [Hantera batchkonton och kvoter med batchhanteringsklientbiblioteket för .NET](batch-management-dotnet.md).

Mer information om Azure AD finns i [Azure Active Directory-dokumentationen](https://docs.microsoft.com/azure/active-directory/). Djupgående exempel som visar hur du använder ADAL är tillgängliga i [Azure Code Samples-biblioteket.](https://azure.microsoft.com/resources/samples/?service=active-directory)

Information om hur du autentiserar batchtjänstprogram med Azure AD finns i [Autentisera batch-tjänstlösningar med Active Directory](batch-aad-auth.md). 


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Vad är Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Autentiseringsscenarier för Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrera program med Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: https://portal.azure.com
[resman_overview]: ../azure-resource-manager/management/overview.md
