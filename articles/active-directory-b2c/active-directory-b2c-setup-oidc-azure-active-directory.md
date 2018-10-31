---
title: Konfigurera inloggning Azure Active Directory-konton en inbyggd princip i Azure Active Directory B2C | Microsoft Docs
description: Konfigurera inloggning Azure Active Directory-konton en inbyggd princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9b9754c9087f2d0064cc1aa75e76520731dfb3a9
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242866"
---
# <a name="set-up-sign-in-azure-active-directory-accounts-a-built-in-policy-in-azure-active-directory-b2c"></a>Konfigurera inloggning Azure Active Directory-konton en inbyggd princip i Azure Active Directory B2C

>[!NOTE]
> Den här funktionen är i offentlig förhandsversion. Använd inte funktionen i produktionsmiljöer.

Den här artikeln visar hur du aktiverar inloggning för användare från en viss Azure Active Directory (Azure AD)-organisation med hjälp av en inbyggd princip i Azure Active Directory (Azure AD) B2C.

## <a name="create-an-azure-ad-app"></a>Skapa en Azure AD-app

Aktivera inloggning för användare från en viss Azure AD-organisation kan du behöva registrera ett program i organisationen Azure AD-klient som inte är samma som du Azure AD B2C-klient.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrollera att du använder den katalog som innehåller Azure AD-klienten genom att klicka på katalog- och prenumerationsfilter i menyn längst upp till den katalog som innehåller din Azure AD-klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **appregistreringar**.
4. Välj **Ny programregistrering**.
5. Ange ett namn för ditt program. Till exempel `Azure AD B2C App`.
6. För den **programtyp**väljer `Web app / API`.
7. För den **inloggnings-URL**, ange följande URL i alla gemener, där `your-B2C-tenant-name` ersätts med namnet på din Azure AD B2C-klient. Till exempel `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Alla URL: er bör nu använda [b2clogin.com](b2clogin.md).

8. Klicka på **Skapa**. Kopiera den **program-ID** som ska användas senare.
9. Välj programmet och välj sedan **inställningar**.
10. Välj **nycklar**, ange nyckelbeskrivningen, Välj en varaktighet och klicka sedan på **spara**. Kopiera värdet för den nyckel som visas för att användas senare.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurera Azure AD som identitetsprovider

1. Kontrollera att du använder den katalog som innehåller Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din Azure AD B2C-klient.
2. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Azure AD B2C**.
3. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
4. Ange en **namn**. Ange till exempel ”Contoso Azure AD”.
5. Välj **identifiera providertyp**väljer **öppna ID Connect (förhandsversion)**, och klicka sedan på **OK**.
6. Klicka på **ställa in den här identitetsprovidern**
7. För **metadata_url**, ange följande URL ersätter `your-AD-tenant-domain` med domännamnet för din Azure AD-klient. Till exempel `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. För **klient-id**, ange program-ID som du sparade tidigare och för **klienthemlighet**, Ange nyckelvärdet som du antecknade tidigare.
9. Alternativt kan du ange ett värde för **Domain_hint**. Till exempel `ContosoAD`. Det här är värdet som ska användas när du refererar till den här identitetsprovider som använder *domain_hint* i begäran. 
10. Klicka på **OK**.
11. Välj **mappa den här identitetsproviderns anspråk** och ange följande anspråk:
    
    - För **användar-ID**, ange `oid`.
    - För **visningsnamn**, ange `name`.
    - För **Förnamn**, ange `given_name`.
    - För **efternamn**, ange `family_name`.
    - För **e-post**, ange `unique_name`.

12. Klicka på **OK**, och klicka sedan på **skapa** att spara din konfiguration.
