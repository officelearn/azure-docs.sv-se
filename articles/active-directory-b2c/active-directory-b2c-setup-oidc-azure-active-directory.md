---
title: Konfigurera inloggning Azure Active Directory-konton en inbyggd princip i Azure Active Directory B2C | Microsoft Docs
description: Konfigurera inloggning Azure Active Directory-konton en inbyggd princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 99e9edf0bb31ec0eab96e22e866d9602a26c2693
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47179926"
---
# <a name="set-up-sign-in-azure-active-directory-accounts-a-built-in-policy-in-azure-active-directory-b2c"></a>Konfigurera inloggning Azure Active Directory-konton en inbyggd princip i Azure Active Directory B2C

>[!NOTE]
> Den här funktionen är i offentlig förhandsversion. Använd inte funktionen i produktionsmiljöer.

Den här artikeln visar hur du aktiverar inloggning för användare från en viss Azure Active Directory (Azure AD)-organisation med hjälp av en inbyggd princip i Azure Active Directory (Azure AD) B2C.

## <a name="create-an-azure-ad-app"></a>Skapa en Azure AD-app

Aktivera inloggning för användare från en viss Azure AD-organisation kan du behöva registrera ett program i organisationen Azure AD-klient.

>[!NOTE]
>`Contoso.com` används för i organisationens Azure AD-klient och `fabrikamb2c.onmicrosoft.com` används som Azure AD B2C-klient i följande anvisningar.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrollera att du använder den katalog som innehåller organisationens Azure AD-klient (contoso.com) genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **appregistreringar**.
4. Välj **Ny programregistrering**.
5. Ange ett namn för ditt program. Till exempel `Azure AD B2C App`.
6. För den **programtyp**väljer `Web app / API`.
7. För den **inloggnings-URL**, ange följande URL i alla gemener, där `your-tenant` ersätts med namnet på din Azure AD B2C-klient (fabrikamb2c.onmicrosoft.com):

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```

8. Klicka på **Skapa**. Kopiera den **program-ID** som ska användas senare.
9. Välj programmet och välj sedan **inställningar**.
10. Välj **nycklar**, ange nyckelbeskrivningen, Välj en varaktighet och klicka sedan på **spara**. Kopiera värdet för den nyckel som visas för att användas senare.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>Konfigurera Azure AD som identitetsprovider i din klient

1. Kontrollera att du använder den katalog som innehåller organisationens Azure AD-klient (contoso.com) genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
2. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Azure AD B2C**.
3. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
4. Ange en **namn**. Ange till exempel ”Contoso Azure AD”.
5. Välj **identifiera providertyp**väljer **öppna ID Connect**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsprovidern**
7. För **metadata_url**, ange följande URL ersätter `your-tenant` med namnet på din Azure AD-klient. Till exempel contoso.com:

    ```
    https://login.microsoftonline.com/your-tenant/.well-known/openid-configuration
    ```

8. Alternativt kan du ange ett värde för **domän** (t.ex. `ContosoAD`). Det här är värdet som ska användas när du refererar till den här identitetsprovider som använder *domain_hint* i begäran. 
9. Klicka på **OK**.
10. Välj **mappa den här identitetsproviderns anspråk** och ange följande anspråk:
    
    - För **användar-ID**, ange `oid`.
    - För **visningsnamn**, ange `name`.
    - För **Förnamn**, ange `given_name`.
    - För **efternamn**, ange `family_name`.
    - För **e-post**, ange `unique_name`.

11. Klicka på **OK**, och sedan **skapa** att spara din konfiguration.
