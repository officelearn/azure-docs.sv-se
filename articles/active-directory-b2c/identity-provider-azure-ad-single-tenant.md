---
title: Konfigurera inloggning för en Azure AD-organisation
titleSuffix: Azure AD B2C
description: Konfigurera inloggning för en specifik Azure Active Directory-organisation i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 35fc4e1d64fa7df392fa878db14c0464da7dccf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188315"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Konfigurera inloggning för en specifik Azure Active Directory-organisation i Azure Active Directory B2C

Om du vill använda en Azure Active Directory (Azure AD) som [identitetsprovider](authorization-code-flow.md) i Azure AD B2C måste du skapa ett program som representerar den. Den här artikeln visar hur du aktiverar inloggning för användare från en viss Azure AD-organisation med hjälp av ett användarflöde i Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Skapa en Azure AD-app

Om du vill aktivera inloggning för användare från en viss Azure AD-organisation måste du registrera ett program inom den organisatoriska Azure AD-klienten, vilket inte är samma som din Azure AD B2C-klientorganisation.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrollera att du använder katalogen som innehåller din Azure AD-klientorganisation. Välj **katalog + prenumerationsfilter** i den övre menyn och välj den katalog som innehåller din Azure AD-klientorganisation. Detta är inte samma klient som din Azure AD B2C-klientorganisation.
3. Välj **Alla tjänster** i det övre vänstra hörnet på Azure-portalen och sök sedan efter och välj **Appregistreringar**.
4. Välj **Ny registrering**.
5. Ange ett namn för ditt program. Till exempel `Azure AD B2C App`.
6. Acceptera valet av **konton i den här organisationskatalogen endast** för det här programmet.
7. För **Omdirigera URI**godkänner du värdet **för webben**och anger följande `your-B2C-tenant-name` URL i alla gemener, där ersätts med namnet på din Azure AD B2C-klient. Till exempel: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Alla webbadresser ska nu använda [b2clogin.com](b2clogin.md).

8. Klicka på **Registrera**. Kopiera **programmets (klient)-ID** som ska användas senare.
9. Välj **Certifikat & hemligheter** på programmenyn och välj sedan Ny **klienthemlighet**.
10. Ange ett namn på klienthemligheten. Till exempel `Azure AD B2C App Secret`.
11. Välj utgångsperiod. För denna ansökan, acceptera valet av **I 1 år**.
12. Välj **Lägg till** och kopiera värdet för den nya klienthemligheten som visas som ska användas senare.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurera Azure AD som identitetsprovider

1. Kontrollera att du använder katalogen som innehåller Azure AD B2C-klient. Välj **katalog + prenumerationsfilter** i den övre menyn och välj den katalog som innehåller din Azure AD B2C-klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Identitetsleverantörer**och välj sedan **Ny OpenID Connect-provider**.
1. Ange ett **namn**. Ange till exempel *Contoso Azure AD*.
1. För **metadata-url**anger du `your-AD-tenant-domain` följande URL som ersätter med domännamnet för din Azure AD-klient:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Till exempel `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

    **Använd inte** slutpunkten för Azure AD v2.0-metadata, till exempel `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`. Detta resulterar i ett `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'` fel som liknar när du försöker logga in.

1. För **klient-ID**anger du det program-ID som du tidigare har registrerat.
1. För **Klienthemlighet**anger du klienthemligheten som du tidigare spelat in.
1. Lämna standardvärdena för **scope,** **svarstyp**och **svarsläge**.
1. (Valfritt) Ange ett värde för **Domain_hint**. Till exempel *ContosoAD*. Det här är värdet som ska användas när du refererar till den här identitetsprovidern med hjälp av *domain_hint* i begäran.
1. Under **Identitetsproviderns anspråksmappning**anger du följande värden för anspråksmappning:

    * **Användar-ID:** *oid*
    * **Visningsnamn**: *namn*
    * **Förnamn**: *given_name*
    * **Efternamn**: *family_name*
    * **E-post:** *unique_name*

1. Välj **Spara**.
