---
title: "Använda Azure Active Directory för att autentisera Batch hanteringslösningar | Microsoft Docs"
description: Program som skapats med Azure resource manager och Batch-resursprovidern autentisera med Azure AD.
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/27/2017
ms.author: tamram
ms.openlocfilehash: 26d4adf4f74f9aacc4cf8cf24be293ebdb4d63c8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Autentisera Batch hanteringslösningar med Active Directory

Program som anropar tjänsten Azure Batch Management autentiseras med [Azure Active Directory] [ aad_about] (Azure AD). Azure AD är Microsofts flera innehavare molnbaserad katalog och identity management-tjänsten. Azure själva använder Azure AD för autentisering av dess kunder, administratörer och användare i organisationer.

Batch Management .NET-biblioteket visar typer för att arbeta med Batch-konton, nycklar, program och programpaket. Batch Management .NET-biblioteket är en Azure resource provider-klient och används tillsammans med [Azure Resource Manager] [ resman_overview] att hantera resurserna via programmering. Azure AD för att kunna autentisera begäranden som görs via alla Azure-resurs providern klienter, inklusive Batch Management .NET-bibliotek och via [Azure Resource Manager][resman_overview].

I den här artikeln förklarar vi använda Azure AD för autentisering från program som använder Batch Management .NET-biblioteket. Vi hur du använder Azure AD för att autentisera en prenumeration administratör eller medadministratör, med integrerad autentisering. Vi använder den [AccountManagment] [ acct_mgmt_sample] exempelprojektet finns på GitHub, kan du gå igenom med hjälp av Azure AD med Batch Management .NET-biblioteket.

Mer information om hur du använder Batch Management .NET-bibliotek och AccountManagement-exempel finns [hantera Batch-konton och kvoter med Batch Management-klientbibliotek för .NET](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Registrera ditt program med Azure AD

Azure [Active Directory Authentication Library] [ aad_adal] (ADAL) tillhandahåller ett programmeringsgränssnitt till Azure AD för användning i dina program. För att anropa ADAL från ditt program, måste du registrera ditt program i en Azure AD-klient. När du registrerar ditt program kan ange du Azure AD med information om ditt program, inklusive ett namn för den i Azure AD-klient. Azure AD sedan innehåller ett program-ID som används för att associera ditt program med Azure AD under körning. Mer information om program-ID finns [program och tjänstens huvudnamn objekt i Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).

Om du vill registrera AccountManagement exempelprogrammet, följer du stegen i den [lägga till ett program](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) i avsnittet [integrera program med Azure Active Directory] [ aad_integrate]. Ange **internt klientprogram** för typ av program. Branschens standard OAuth 2.0-URI för den **omdirigerings-URI** är `urn:ietf:wg:oauth:2.0:oob`. Du kan dock ange en giltig URI (t.ex `http://myaccountmanagementsample`) för den **omdirigerings-URI**eftersom den inte behöver vara en verklig slutpunkt:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

När du har slutfört registreringsprocessen visas det program-ID och Objektidentifieraren (tjänstens huvudnamn) för ditt program.  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Ge Azure Resource Manager API-åtkomst till ditt program

Därefter måste du delegera åtkomst till ditt program till Azure Resource Manager API. Azure AD-identifieraren för Resource Manager API är **Windows Azure Service Management API**.

Följ dessa steg i Azure-portalen:

1. I det vänstra navigeringsfönstret i Azure portal väljer **fler tjänster**, klickar du på **App registreringar**, och klicka på **Lägg till**.
2. Sök efter namnet på programmet i listan över app registreringar:

    ![Sök efter programnamnet](./media/batch-aad-auth-management/search-app-registration.png)

3. Visa den **inställningar** bladet. I den **API-åtkomst** väljer **nödvändiga behörigheter**.
4. Klicka på **Lägg till** att lägga till en ny nödvändig behörighet. 
5. I steg 1, ange **Windows Azure Service Management API**, välja den API från listan över resultat och på den **Välj** knappen.
6. I steg 2, markerar du kryssrutan bredvid **åtkomst Azure klassiska distributionsmodellen som organisationen användare**, och klicka på den **Välj** knappen.
7. Klicka på den **klar** knappen.

Den **nödvändiga behörigheter** bladet visar nu att ditt program behörigheter till både ADAL och Resource Manager API: er. Behörigheter som ADAL som standard när du först registrera din app med Azure AD.

![Delegera behörigheter till Azure Resource Manager API](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Azure AD-slutpunkter

Du behöver två välkända slutpunkter för att autentisera dina lösningar för Batch-hantering med Azure AD.

- Den **vanliga Azure AD-slutpunkt** ger en allmän referens samla in gränssnittet när en viss klient inte tillhandahålls, när det gäller integrerad autentisering:

    `https://login.microsoftonline.com/common`

- Den **Azure Resource Manager-slutpunkt** används för att hämta en token för att autentisera förfrågningar till Batch management-tjänsten:

    `https://management.core.windows.net/`

Exempelprogrammet AccountManagement definierar konstanter för dessa slutpunkter. Lämna dessa konstanter oförändrade:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Referens-program-ID 

Klientprogrammet använder program-ID (kallas även för klient-ID) för att få åtkomst till Azure AD under körning. När du har registrerat ditt program i Azure-portalen, kan du uppdatera din kod för att använda det program-ID som tillhandahålls av Azure AD för registrerade programmet. Kopiera program-ID på exempelprogrammet AccountManagement från Azure portal till lämplig konstant:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Också kopiera omdirigerings-URI som du angav under registreringen. Omdirigerings-URI som angetts i din kod måste matcha omdirigerings-URI som du angav när du registrerade programmet.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Skaffa en Azure AD-token för autentisering

När du registrerar AccountManagement exemplet i Azure AD-klient och uppdatera källan exempelkoden med värdena är exemplet redo att autentisera med hjälp av Azure AD. När du kör exemplet försöker ADAL hämta en autentiseringstoken. I det här steget ombeds du ange autentiseringsuppgifterna för ditt Microsoft: 

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

När du har angett dina autentiseringsuppgifter kan exempelprogrammet fortsätta att utfärda autentiserade begäranden till Batch management-tjänsten. 

## <a name="next-steps"></a>Nästa steg

Mer information om körs den [AccountManagement exempelprogrammet][acct_mgmt_sample], se [hantera Batch-konton och kvoter med Batch Management-klientbibliotek för .NET](batch-management-dotnet.md) .

Mer information om Azure AD finns i [Azure Active Directory-dokumentationen](https://docs.microsoft.com/azure/active-directory/). Djupgående exempel som visar hur du använder ADAL finns i den [Azure kodexempel](https://azure.microsoft.com/resources/samples/?service=active-directory) bibliotek.

För att autentisera Batch-tjänstprogram med Azure AD finns [autentiserar Batch tjänstelösningar med Active Directory](batch-aad-auth.md). 


[aad_about]: ../active-directory/active-directory-whatis.md "Vad är Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Autentiseringsscenarier för Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrera program med Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md
