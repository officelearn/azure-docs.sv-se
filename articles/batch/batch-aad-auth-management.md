---
title: Använd Azure Active Directory för att autentisera lösningar för Batch | Microsoft Docs
description: Program som skapats med Azure resource manager och Batch-resursprovidern autentisera med Azure AD.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/27/2017
ms.author: lahugh
ms.openlocfilehash: 67aced11d885be949e7e7dc14ba7aa59e903716a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467857"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Autentisera lösningar för Batch med Active Directory

Program som anropar Azure Batch Management-tjänsten autentisera med [Azure Active Directory] [ aad_about] (Azure AD). Azure AD är Microsofts flera innehavare molnbaserad katalog- och identity management-tjänsten. Azure själva använder Azure AD för autentisering av dess kunder, administratörer och användare i organisationer.

Batch Management .NET-biblioteket visar typer för att arbeta med Batch-konton, nycklar, program och programpaket. Batch Management .NET-biblioteket är en Azure resource provider-klient och används tillsammans med [Azure Resource Manager] [ resman_overview] att hantera dessa resurser programmässigt. Azure AD som krävs för att autentisera begäranden som görs via alla Azure resource provider postklienter, inklusive Batch Management .NET-biblioteket och via [Azure Resource Manager][resman_overview].

I den här artikeln förklarar vi med Azure AD för att autentisera från program som använder Batch Management .NET-biblioteket. Vi visar hur du använder Azure AD för att autentisera en administratör för prenumerationen eller delad administratör, med integrerad autentisering. Vi använder den [AccountManagement] [ acct_mgmt_sample] exempelprojektet, finns på GitHub för att gå igenom med hjälp av Azure AD med Batch Management .NET-biblioteket.

Mer information om hur du använder Batch Management .NET-biblioteket och AccountManagement exemplet finns [hantera Batch-konton och kvoter med Batch Management-klientbiblioteket för .NET](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Registrera ditt program med Azure AD

Azure [Active Directory Authentication Library] [ aad_adal] (ADAL) innehåller ett programmeringsgränssnitt till Azure AD för användning i dina program. För att anropa ADAL från ditt program, måste du registrera ditt program i en Azure AD-klient. När du registrerar ditt program kan ange du Azure AD med information om ditt program, inklusive ett namn för den i Azure AD-klient. Sedan Azure AD tillhandahåller ett program-ID som används för att associera ditt program med Azure AD vid körning. Läs mer om program-ID i [program och tjänstobjekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Om du vill registrera AccountManagement exempelprogrammet, följer du stegen i den [lägga till ett program](../active-directory/develop/quickstart-v1-add-azure-ad-app.md) i avsnittet [integrera program med Azure Active Directory] [ aad_integrate]. Ange **internt klientprogram** för typ av program. Branschens standard OAuth 2.0-URI för den **omdirigerings-URI** är `urn:ietf:wg:oauth:2.0:oob`. Du kan dock ange en giltig URI (till exempel `http://myaccountmanagementsample`) för den **omdirigerings-URI**, som den inte behöver vara en verklig slutpunkt:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

När du har slutfört registreringsprocessen ser du det program-ID och Objektidentifieraren (tjänstens huvudnamn) för ditt program.  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Ge Azure Resource Manager API-åtkomst till ditt program

Därefter måste du delegera åtkomst till programmet till Azure Resource Manager-API. Azure AD-identifieraren för Resource Manager-API är **Windows Azure Service Management API**.

Följ dessa steg i Azure portal:

1. I det vänstra navigeringsfönstret i Azure-portalen, väljer **alla tjänster**, klickar du på **Appregistreringar**, och klicka på **Lägg till**.
2. Sök efter namnet på ditt program i listan över app-registreringar:

    ![Sök efter namnet på appen](./media/batch-aad-auth-management/search-app-registration.png)

3. Visa den **inställningar** bladet. I den **API-åtkomst** väljer **behörigheter som krävs för**.
4. Klicka på **Lägg till** att lägga till en ny nödvändig behörighet. 
5. I steg 1, ange **Windows Azure Service Management API**, Välj det API: et i resultatlistan och klicka på den **Välj** knappen.
6. I steg 2, markera kryssrutan bredvid **åtkomst Azure klassiska distributionsmodellen som organisationsanvändare**, och klicka på den **Välj** knappen.
7. Klicka på den **klar** knappen.

Den **nödvändiga behörigheter** bladet visas nu att programmet behörighet beviljas både ADAL och Resource Manager API: er. Behörigheterna har beviljats till ADAL som standard när du först registrera din app med Azure AD.

![Delegera behörigheter till Azure Resource Manager API](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Azure AD-slutpunkter

Du behöver två välkända slutpunkter för att autentisera dina Batch Management-lösningar med Azure AD.

- Den **vanliga Azure AD-slutpunkten** ger en allmän referens samla in gränssnittet när en specifik klient inte anges när det gäller integrerad autentisering:

    `https://login.microsoftonline.com/common`

- Den **Azure Resource Manager-slutpunkten** används för att hämta en token för autentisering av förfrågningar till Batch management-tjänsten:

    `https://management.core.windows.net/`

Exempelprogrammet AccountManagement definierar konstanter för de här slutpunkterna. Lämna konstanterna oförändrade:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Referera till ditt program-ID 

Klientprogrammet använder program-ID (kallas även för klient-ID) för att få åtkomst till Azure AD vid körning. När du har registrerat ditt program i Azure-portalen, kan du uppdatera din kod för att använda program-ID som tillhandahålls av Azure AD för ditt registrerade program. Kopiera ditt program-ID i exempelprogrammet AccountManagement från Azure-portalen till lämplig konstant:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Kopiera också omdirigeringen-URI som du angav under registreringen. Omdirigerings-URI som angetts i din kod måste matcha omdirigeringen-URI som du angav när du har registrerat programmet.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Hämta en Azure AD-autentiseringstoken

När du registrerar AccountManagement exemplet i Azure AD-klient och uppdatera exempelkod för källan med dina värden kan är exemplet redo att autentisera med hjälp av Azure AD. När du kör exemplet försöker ADAL hämta en autentiseringstoken. I det här steget ombeds du ange autentiseringsuppgifterna för ditt Microsoft: 

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

När du har angett dina autentiseringsuppgifter för kan exempelprogrammet fortsätta att utfärda autentiserade begäranden till Batch management-tjänsten. 

## <a name="next-steps"></a>Nästa steg

Mer information om körs den [AccountManagement exempelprogrammet][acct_mgmt_sample], se [hantera Batch-konton och kvoter med Batch Management-klientbiblioteket för .NET](batch-management-dotnet.md) .

Läs mer om Azure AD i den [Azure Active Directory-dokumentationen](https://docs.microsoft.com/azure/active-directory/). Djupgående exempel som visar hur du använder ADAL finns i den [kodexempel för Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) biblioteket.

För att autentisera Batch service-program med hjälp av Azure AD, se [autentisera Batch service-lösningar med Active Directory](batch-aad-auth.md). 


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Vad är Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Autentiseringsscenarier för Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrera program med Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md
