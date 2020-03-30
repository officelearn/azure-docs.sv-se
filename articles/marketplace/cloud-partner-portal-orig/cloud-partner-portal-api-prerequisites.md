---
title: API-förutsättningar | Azure Marketplace
description: Förutsättningar gäller att använda API:erna för Cloud Partner Portal.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 2a1022c6d041bf645b43dfed391a489de30b2fce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288571"
---
<a name="api-prerequisites"></a>API-förutsättningar
================

Det finns två programmatiska resurser som krävs för att använda Api:erna för Cloud Partner Portal: ett tjänsthuvudnamn och en Azure Active Directory -åtkomsttoken (Azure Active Directory).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Skapa ett tjänsthuvudnamn i din Azure Active Directory-klient
----------------------------------------------------------------

Först måste du skapa ett tjänsthuvudnamn i din Azure AD-klientorganisation. Den här klienten tilldelas sin egen uppsättning behörigheter i Cloud Partner Portal. Koden anropar API:er som den här klienten i stället för att använda dina personliga autentiseringsuppgifter.  En fullständig förklaring av att skapa ett huvudnamn för tjänsten finns i [Använda portal för att skapa ett Azure Active Directory-program och tjänsthuvudnamn som kan komma åt resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).


<a name="add-the-service-principal-to-your-account"></a>Lägg till tjänstens huvudnamn i ditt konto
-----------------------------------------

Nu när du har skapat tjänstens huvudnamn i din klientorganisation kan du lägga till det som användare i ditt Cloud Partner Portal-konto. Precis som en användare kan tjänstens huvudnamn vara en ägare eller en bidragsgivare till portalen.

Följ följande steg för att lägga till tjänstens huvudnamn:

1. Logga in på molnpartnerportalen. 
2. Klicka på **Användare** i det vänstra menyfältet och välj **Lägg till användare**.

   ![Lägga till en användare i portalen](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. Välj **Tjänsthuvudnamn** i listrutan **Typ** och lägg till följande information:

-   Ett **eget namn** för tjänstens `spAccount`huvudnamn, till exempel .
-   **Program-ID**. Om du vill hitta den här identifieraren går du till [Azure Portal,](https://portal.azure.com)klickar på **Azure Active Directory,** väljer **Appregistreringar**och klickar på din app.
-   **Klient-ID**, även känt som **katalog-ID**, för din Azure AD-klientorganisation. Du hittar den här identifieraren på sidan Azure Active Directory i [Azure-portalen](https://portal.azure.com)under **Egenskaper**.
-   **Objekt-ID** för tjänstens huvudobjekt. Du kan hämta den här identifieraren från Azure-portalen. Gå till **Azure Active Directory**, välj **Appregistreringar,** klicka på din app och klicka på appnamnet under **Hanterat program i lokal katalog**. Gå sedan till sidan **Egenskaper** och leta reda på objekt-ID: et. Kontrollera att du inte tar det ursprungliga objekt-ID:et som finns i appen, utan i stället objekt-ID:n i det hanterade programmet.
-   Rollen **som** är associerad med kontot, som ska användas för RBAC.

     ![Lägga till en hanterad app i portalen](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Klicka på **Lägg till** om du vill lägga till tjänstens huvudnamn i ditt konto.

   ![Lägga till ett huvudnamn för tjänsten](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Hämta en Azure AD-åtkomsttoken
----------------------------

Api:erna för Cloud Partner Portal använder följande tillgångar och protokoll under autentiseringen:

- En JSON Web Token (JWT) bärare token för att begära åtkomst till resurser
- [OpenID Connect-protokollet](https://openid.net/connect/) (OIDC) för att verifiera identitet
- [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) som identitetsmyndighet

Det finns två principmetoder för att programmässigt förvärva en JWT-token:

- Använd Microsoft Authentication Library för .NET ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Den här metoden på högre nivå rekommenderas för .NET-utvecklare. 
- Gör en **HTTP POST-begäran** till slutpunkten för Azure AD-oauth-token, som har formen: **token**

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Nu kan du skicka den här token som en del av auktoriseringshuvudet för API-begäranden.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> För alla API:er i den här referensen antas auktoriseringshuvudet alltid skickas, så det nämns inte uttryckligen.

Om du stöter på autentiseringsfel i din begäran läser du [Felsöka autentiseringsfel](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
