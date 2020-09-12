---
title: Konfigurera inloggning för en Azure AD-organisation
titleSuffix: Azure AD B2C
description: Konfigurera inloggning för en speciell Azure Active Directory organisation i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: e4fa4b64c6519df90d5883e8c5760b3ed2ce0337
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "90004467"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Konfigurera inloggning för en speciell Azure Active Directory organisation i Azure Active Directory B2C

Om du vill använda en Azure Active Directory (Azure AD) som [identitets leverantör](authorization-code-flow.md) i Azure AD B2C måste du skapa ett program som representerar det. Den här artikeln visar hur du aktiverar inloggning för användare från en särskild Azure AD-organisation med hjälp av ett användar flöde i Azure AD B2C.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurera Azure AD som en identitets leverantör

1. Kontrol lera att du använder den katalog som innehåller Azure AD B2C klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller Azure AD B2C klienten.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer**och välj sedan **ny OpenID Connect-Provider**.
1. Ange ett **namn**. Ange till exempel *contoso Azure AD*.
1. För **metadata-URL**anger du följande URL `{tenant}` som ersätter med domän namnet för din Azure AD-klient:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Till exempel `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Till exempel `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. För **klient-ID**anger du det program-ID som du tidigare har registrerat.
1. För **klient hemlighet**anger du den klient hemlighet som du tidigare har registrerat.
1. För **scopet**anger du `openid profile` .
1. Lämna standardvärdena för **svars typ**och **svars läge**.
1. Valfritt I **domän tipset**anger du `contoso.com` . Mer information finns i [Konfigurera direkt inloggning med Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Under **identitets leverantör anspråks mappning**väljer du följande anspråk:

    * **Användar-ID**: *OID*
    * **Visnings namn**: *namn*
    * **Tilldelat namn**: *given_name*
    * Efter **namn**: *family_name*
    * **E-post**: *unique_name*

1. Välj **Spara**.
