---
title: Konfigurera inloggning för en Azure Active Directory-organisation – Azure Active Directory B2C | Microsoft Docs
description: Konfigurera inloggning för en viss Azure Active Directory-organisation i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 28dbf0382ac151857e72d4bb59e207f07c8ad3f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508425"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Konfigurera inloggning för en viss Azure Active Directory-organisation i Azure Active Directory B2C

>[!NOTE]
> Den här funktionen är en allmänt tillgänglig förhandsversion. Använd inte funktionen i produktionsmiljöer.

Att använda en Azure Active Directory (Azure AD) som en [identitetsprovider](active-directory-b2c-reference-oauth-code.md) i Azure AD B2C måste du skapa ett program som representerar den. Den här artikeln visar hur du aktiverar inloggning för användare från en viss Azure AD organisation med hjälp av en användare för flow i Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Skapa en Azure AD-app

Aktivera inloggning för användare från en viss Azure AD-organisation kan du behöva registrera ett program i organisationen Azure AD-klient som inte är samma som din Azure AD B2C-klient.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrollera att du använder den katalog som innehåller din Azure AD-klient. Välj den **katalog- och prenumerationsfilter** på den översta menyn och välj den katalog som innehåller din Azure AD-klient. Detta är inte samma klient som din Azure AD B2C-klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **appregistreringar**.
4. Välj **ny registrering**.
5. Ange ett namn för ditt program. Till exempel `Azure AD B2C App`.
6. Acceptera valet av **konton i den här organisationens katalogen** för det här programmet.
7. För den **omdirigerings-URI**, acceptera värdet för **Web**, och ange följande URL i alla gemener, där `your-B2C-tenant-name` ersätts med namnet på din Azure AD B2C-klient. Till exempel `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your--B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Alla URL: er bör nu använda [b2clogin.com](b2clogin.md).

8. Klicka på **registrera**. Kopiera den **(klient)-ID: T** som ska användas senare.
9. Välj **certifikat och hemligheter** i program-menyn och välj sedan **nya klienthemligheten**.
10. Ange ett namn för klienthemligheten. Till exempel `Azure AD B2C App Secret`.
11. Välj giltighetsperiod. Det här programmet accepterar valet av **i 1 år**.
12. Välj **Lägg till** och kopiera värdet för den nya klienthemligheten som visas för att användas senare.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurera Azure AD som identitetsprovider

1. Kontrollera att du använder den katalog som innehåller Azure AD B2C-klient. Välj den **katalog- och prenumerationsfilter** på den översta menyn och välj den katalog som innehåller din Azure AD B2C-klient.
2. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
3. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
4. Ange en **namn**. Ange till exempel `Contoso Azure AD`.
5. Välj **identifiera providertyp**väljer **öppna ID Connect (förhandsversion)** , och klicka sedan på **OK**.
6. Välj **ställa in den här identitetsprovidern**
7. För **metadata_url**, ange följande URL ersätter `your-AD-tenant-domain` med domännamnet för din Azure AD-klient. Till exempel `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. För **klient-ID**, ange program-ID som du sparade tidigare och för **klienthemlighet**, ange klienthemligheten som du antecknade tidigare.
9. Alternativt kan du ange ett värde för **Domain_hint**. Till exempel `ContosoAD`. Det här är värdet som ska användas när du refererar till den här identitetsprovider som använder *domain_hint* i begäran. 
10. Klicka på **OK**.
11. Välj **mappa den här identitetsproviderns anspråk** och ange följande anspråk:
    
    - För **användar-ID**, ange `oid`.
    - För **visningsnamn**, ange `name`.
    - För **Förnamn**, ange `given_name`.
    - För **efternamn**, ange `family_name`.
    - För **e-post**, ange `unique_name`.

12. Klicka på **OK**, och klicka sedan på **skapa** att spara din konfiguration.
