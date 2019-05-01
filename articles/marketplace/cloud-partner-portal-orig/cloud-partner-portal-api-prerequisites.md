---
title: API-krav | Azure Marketplace
description: 'Krav för bland annat med hjälp av Cloud Partner Portal-API: er.'
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: a973ab0a406168756af61900fd35947c8be6d03b
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935390"
---
<a name="api-prerequisites"></a>API-krav
================

Det finns två nödvändiga programmässiga tillgångar som du behöver använda Cloud Partner Portal-API: er: åtkomsttoken för ett huvudnamn för tjänsten och en Azure Active Directory (AD Azure).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Skapa ett huvudnamn för tjänsten i Azure Active Directory-klienten
----------------------------------------------------------------

Först måste du skapa ett huvudnamn för tjänsten i Azure AD-klienten. Den här klienten kommer att tilldelas en egen uppsättning behörigheter i Cloud Partner Portal. Koden anropar API: er med som den här klienten istället för att använda dina personliga autentiseringsuppgifter.  En fullständig förklaring för att skapa ett huvudnamn för tjänsten finns i [Använd portalen för att skapa en Azure Active Directory-program och tjänstens huvudnamn som kan komma åt resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).


<a name="add-the-service-principal-to-your-account"></a>Lägg till tjänstens huvudnamn till ditt konto
-----------------------------------------

Nu när du har skapat tjänsten huvudnamn i din klient, kan du lägga till den som en användare till ditt konto partnerportalen i molnet. Precis som en användare, kan tjänstens huvudnamn vara ägare eller deltagare till portalen.

Använd följande steg för att lägga till tjänstens huvudnamn:

1. Loggar in på partnerportalen i molnet. 
2. Klicka på **användare** på den vänstra menyraden och väljer **Lägg till användare**.

   ![Lägga till en användare på portalen](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. Från den **typ** listrutan **tjänstens huvudnamn** och Lägg till följande information:

-   En **eget namn** för tjänstens huvudnamn, till exempel `spAccount`.
-   Den **program-ID**. Du hittar den här identifieraren genom att gå till den [Azure-portalen](https://portal.azure.com), klickar du på **Azure Active Directory**, Välj **appregistreringar**, och klicka på din app.
-   Den **klient-ID**, även kallat den **katalog-ID**, för din Azure AD-klient. Du hittar den här identifieraren i Azure Active Directory-sidan i den [Azure-portalen](https://portal.azure.com)under **egenskaper**.
-   Den **objekt-ID** för din tjänstens huvudnamnsobjekt. Du kan hämta den här identifieraren i Azure Portal. Gå till **Azure Active Directory**, Välj **appregistreringar**, klicka på din app och klicka på namnet på under **hanterat program i den lokala katalogen**. Gå sedan till den **egenskaper** sidan för att hitta objekt-ID. Kontrollera att du inte grabbing första objekt-ID som finns på din app, men i stället objekt-ID i det hanterade programmet.
-   Den **rollen** som är associerade med kontot som ska användas för RBAC.

     ![Lägga till en hanterad app på portalen](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Klicka på **Lägg till** att lägga till tjänstens huvudnamn till ditt konto.

   ![Lägg till ett huvudnamn för tjänsten](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Hämta en Azure AD-token
----------------------------

Cloud Partner Portal-API: er kan du använda följande tillgångar och protokoll under autentisering:

- En ägartoken för JSON Web Token (JWT) för att begära åtkomst till resurser
- Den [OpenID Connect](https://openid.net/connect/) (OIDC)-protokollet för att verifiera identitet
- [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) som utfärdare för identitet

Det finns två princip metoder för att programmässigt skaffa en JWT-token:

- Använder sig av Microsoft Authentication Library för .NET ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Den här på högre nivå metoden rekommenderas för .NET-utvecklare. 
- Gör en **HTTP POST** begäran om att Azure AD oauth **token** slutpunkt i formatet:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Du kan nu skicka denna token som en del av auktoriseringsrubrik för API-förfrågningar.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> För alla API: erna i den här referensen antas auktoriseringsrubriken alltid skickas, så att det inte uttryckligen nämndes.

Om du stöter på autentiseringsfel i din begäran, se [felsökning autentiseringsfel](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
