---
title: API-krav – Azure Marketplace
description: 'Krav för att använda Cloud Partner Portal API: er.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 2cce5d3463ced126a3e6e77323110e4a70024acb
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292901"
---
<a name="api-prerequisites"></a>API-krav
================

> [!NOTE]
> Cloud Partner Portal API: er är integrerade i och fortsätter att fungera i Partner Center. Över gången introducerar små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](./cloud-partner-portal-api-overview.md) för att se till att koden fortsätter att fungera efter över gången till Partner Center. CPP-API: er bör endast användas för befintliga produkter som redan har integrerats före över gången till Partner Center. nya produkter bör använda API: er för överföring av Partner Center.

Det finns två obligatoriska programmerings resurser som du behöver använda för Cloud Partner Portal API: er: ett tjänst huvud namn och en Azure Active Directory (Azure AD)-åtkomsttoken.


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Skapa ett huvud namn för tjänsten i Azure Active Directory klient organisationen
----------------------------------------------------------------

Först måste du skapa ett huvud namn för tjänsten i din Azure AD-klient. Den här klienten tilldelas en egen uppsättning behörigheter i Cloud Partner Portal. Din kod anropar API: er med som den här klienten i stället för att använda dina personliga autentiseringsuppgifter.  En fullständig förklaring av hur du skapar ett huvud namn för tjänsten finns i [så här gör du: Använd portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md).


<a name="add-the-service-principal-to-your-account"></a>Lägg till tjänstens huvud namn i ditt konto
-----------------------------------------

Nu när du har skapat tjänstens huvud namn i din klient organisation kan du lägga till det som en användare i ditt Cloud Partner Portal-konto. Precis som en användare kan tjänstens huvud namn vara en ägare eller deltagare till portalen.

Använd följande steg för att lägga till tjänstens huvud namn:

1. Logga in på Cloud Partner Portal. 
2. Klicka på **användare** på den vänstra meny raden och välj **Lägg till användare**.

   ![Lägga till en användare i portalen](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. Välj **tjänstens huvud namn** i list rutan **typ** och Lägg till följande information:

-   Ett **eget namn** på tjänstens huvud namn, till exempel `spAccount` .
-   **Program-ID**. Du hittar den här identifieraren genom att gå till [Azure Portal](https://portal.azure.com), klicka på **Azure Active Directory**, välja **Appregistreringar**och klicka på din app.
-   **Klient-ID**, även kallat **katalog-ID**för din Azure AD-klient. Du hittar den här identifieraren på sidan Azure Active Directory i [Azure Portal](https://portal.azure.com)under **Egenskaper**.
-   **Objekt-ID** för ditt tjänst huvud objekt. Du kan hämta den här identifieraren från Azure Portal. Gå till **Azure Active Directory**, Välj **Appregistreringar**, klicka på din app och klicka på appens namn under **hanterat program i lokal katalog**. Gå sedan till sidan **Egenskaper** för att hitta objekt-ID: t. Se till att du inte bevisar det första objekt-ID: t som finns i appen, utan i stället objekt-ID: t i det hanterade programmet.
-   **Rollen** som associeras med kontot, som kommer att användas för RBAC.

     ![Lägg till en hanterad app i portalen](./media/cloud-partner-portal-api-prerequisites/managed-app.png)

1. Klicka på **Lägg** till för att lägga till tjänstens huvud namn i ditt konto.

   ![Lägg till ett huvud namn för tjänsten](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Hämta en Azure AD-åtkomsttoken
----------------------------

Cloud Partner Portal-API: erna använder följande till gångar och protokoll vid autentisering:

- En JSON Web Token (JWT) Bearer-token för att begära åtkomst till resurser
- [OpenID Connect](https://openid.net/connect/) -protokollet (OIDC) för att verifiera identitet
- [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) som identitets utfärdare

Det finns två sätt att skaffa en JWT-token genom programmering:

- Använd Microsoft Authentication Library för .NET ([MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Den här bättre metoden rekommenderas för .NET-utvecklare. 
- Gör en **http post-** begäran till Azure AD OAuth- **token** -slutpunkten, som tar formuläret:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Nu kan du skicka denna token som en del av Authorization-huvudet för API-begäranden.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> För alla API: er i denna referens antas Authorization-huvudet alltid skickas, vilket innebär att det inte uttryckligen anges.

Om du stöter på autentiseringsfel i din begäran, se [Felsöka autentiseringsfel](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
