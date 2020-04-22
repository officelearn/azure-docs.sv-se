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
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 5b21fcd2d3ec5560b01352b112e9ed1bb2404766
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678046"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Konfigurera inloggning för en specifik Azure Active Directory-organisation i Azure Active Directory B2C

Om du vill använda en Azure Active Directory (Azure AD) som [identitetsprovider](authorization-code-flow.md) i Azure AD B2C måste du skapa ett program som representerar den. Den här artikeln visar hur du aktiverar inloggning för användare från en viss Azure AD-organisation med hjälp av ett användarflöde i Azure AD B2C.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurera Azure AD som identitetsprovider

1. Kontrollera att du använder katalogen som innehåller Azure AD B2C-klient. Välj **katalog + prenumerationsfilter** i den övre menyn och välj den katalog som innehåller din Azure AD B2C-klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Identitetsleverantörer**och välj sedan **Ny OpenID Connect-provider**.
1. Ange ett **namn**. Ange till exempel *Contoso Azure AD*.
1. För **metadata-url**anger du `{tenant}` följande URL som ersätter med domännamnet för din Azure AD-klient:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Till exempel `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.

1. För **klient-ID**anger du det program-ID som du tidigare har registrerat.
1. För **Klienthemlighet**anger du klienthemligheten som du tidigare spelat in.
1. För **scope**anger `openid profile`du .
1. Lämna standardvärdena för **svarstyp**och **svarsläge**.
1. (Valfritt) För **tipset**Domain `contoso.com`anger du . Mer information finns [i Konfigurera direkt inloggning med Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Under **Identitetsproviderns anspråksmappning**väljer du följande anspråk:

    * **Användar-ID:** *oid*
    * **Visningsnamn**: *namn*
    * **Förnamn**: *given_name*
    * **Efternamn**: *family_name*
    * **E-post:** *unique_name*

1. Välj **Spara**.
