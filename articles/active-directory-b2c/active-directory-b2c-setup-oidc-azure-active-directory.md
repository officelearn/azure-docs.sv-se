---
title: Konfigurera inloggning för en Azure Active Directory organisation – Azure Active Directory B2C
description: Konfigurera inloggning för en speciell Azure Active Directory organisation i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 0c2e368b9c12d8ab673e5b8808632501de448b9a
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755755"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Konfigurera inloggning för en speciell Azure Active Directory organisation i Azure Active Directory B2C

Om du vill använda en Azure Active Directory (Azure AD) som [identitets leverantör](active-directory-b2c-reference-oauth-code.md) i Azure AD B2C måste du skapa ett program som representerar det. Den här artikeln visar hur du aktiverar inloggning för användare från en särskild Azure AD-organisation med hjälp av ett användar flöde i Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Skapa en Azure AD-App

Om du vill aktivera inloggning för användare från en specifik Azure AD-organisation måste du registrera ett program i Azure AD-klienten, som inte är samma som din Azure AD B2C klient organisation.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller din Azure AD-klient. Detta är inte samma klient som din Azure AD B2C klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
4. Välj **ny registrering**.
5. Ange ett namn för ditt program. Till exempel `Azure AD B2C App`.
6. Godkänn bara valet av **konton i den här organisations katalogen** för det här programmet.
7. För **omdirigerings-URI: n**, godkänn värdet för **webb**och ange följande URL i gemener, där `your-B2C-tenant-name` ersätts med namnet på din Azure AD B2C-klient. Till exempel `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Alla URL: er ska nu använda [b2clogin.com](b2clogin.md).

8. Klicka på **Registrera**. Kopiera **program-ID: t (klienten)** som ska användas senare.
9. Välj **certifikat & hemligheter** på program menyn och välj sedan **ny klient hemlighet**.
10. Ange ett namn för klient hemligheten. Till exempel `Azure AD B2C App Secret`.
11. Välj förfallo period. För det här programmet godkänner du valet på **1 år**.
12. Välj **Lägg till** och kopiera värdet för den nya klient hemlighet som visas senare.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurera Azure AD som en identitets leverantör

1. Kontrol lera att du använder den katalog som innehåller Azure AD B2C klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller Azure AD B2C klienten.
1. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer**och välj sedan **ny OpenID Connect-Provider**.
1. Ange ett **namn**. Ange till exempel *contoso Azure AD*.
1. För **metadata-URL**anger du följande URL och ersätter `your-AD-tenant-domain` med domän namnet för din Azure AD-klient:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Till exempel `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

    Använd **inte** Azure AD v 2.0-slut punkten för metadata, till exempel `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`. Detta resulterar i ett fel som liknar `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'` när du försöker logga in.

1. För **klient-ID**anger du det program-ID som du tidigare har registrerat.
1. För **klient hemlighet**anger du den klient hemlighet som du tidigare har registrerat.
1. Lämna standardvärdena för **omfång**, **svars typ**och **svars läge**.
1. Valfritt Ange ett värde för **Domain_hint**. Till exempel *ContosoAD*. Detta är det värde som ska användas för att referera till den här identitets leverantören med *domain_hint* i begäran.
1. Under **anspråks mappning för identitetsprovider**anger du följande anspråks mappnings värden:

    * **Användar-ID**: *OID*
    * **Visnings namn**: *namn*
    * **Angivet namn**: *given_name*
    * Efter **namn**: *family_name*
    * **E-post**: *unique_name*

1. Välj **Spara**.
