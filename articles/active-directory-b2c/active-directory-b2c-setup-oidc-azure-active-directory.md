---
title: Lägg till en Azure AD-provider med hjälp av inbyggda principer i Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du lägger till identitetsprovider öppna ID Connect (Azure AD).
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e09ad89f3225af9de40781fafc022c8326f80619
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338646"
---
# <a name="azure-active-directory-b2c-sign-in-using-azure-ad-accounts-through-a-built-in-policy"></a>Azure Active Directory B2C: Logga in med Azure AD-konton via en inbyggd princip

>[!NOTE]
> Den här funktionen är i offentlig förhandsversion. Använd inte funktionen i produktionsmiljöer.

Den här artikeln visar hur du aktiverar inloggning för användare från en specifik Azure Active Directory (Azure AD) organisation inbyggda principer.

## <a name="create-an-azure-ad-app"></a>Skapa en Azure AD-app

Aktivera inloggning för användare från en viss Azure AD-organisation kan du behöva registrera ett program i organisationen Azure AD-klient.

>[!NOTE]
> Vi använder ”contoso.com” för i organisationens Azure AD-klient och ”fabrikamb2c.onmicrosoft.com” som Azure AD B2C-klient i följande anvisningar.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj ditt konto i det översta fältet. Från den **Directory** väljer du den organisationens Azure AD-klient där du ska registrera programmet (contoso.com).
1. Välj **alla tjänster** i det vänstra fönstret och Sök efter ”appregistreringar”.
1. Välj **Ny programregistrering**.
1. Ange ett namn för ditt program (till exempel `Azure AD B2C App`).
1. Välj **Webbapp/API** som programtyp.
1. För **inloggnings-URL**, ange följande URL, där `yourtenant` ersätts med namnet på din Azure AD B2C-klient (`fabrikamb2c`):

    >[!NOTE]
    >Värdet för ”yourtenant” måste skrivas med små bokstäver i de **inloggnings-URL**.

    ```Console
    https://yourtenant.b2clogin.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Spara program-ID som du vill använda i nästa avsnitt som klient-ID.
1. Under den **inställningar** bladet väljer **nycklar**.
1. Ange en **nyckelbeskrivning** under den **lösenord** och ange den **varaktighet** till ”upphör aldrig att gälla”. 
1. Klicka på **spara**, och Skriv ned den resulterande nyckeln **värdet**, som du vill använda i nästa avsnitt som klienthemlighet.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>Konfigurera Azure AD som identitetsprovider i din klient

1. Välj ditt konto i det översta fältet. Från den **Directory** väljer Azure AD B2C-klient (fabrikamb2c.onmicrosoft.com).
1. [Gå till menyn för Azure AD B2C-inställningar](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) i Azure-portalen.
1. På menyn Azure AD B2C-inställningar klickar du på **identitetsprovidrar**.
1. Klicka på **+Lägg till** överst på bladet.
1. Ange ett eget **namn** för konfigurationen av identity-providern. Ange till exempel ”Contoso Azure AD”.
1. Klicka på **identifiera providertyp**väljer **öppna ID Connect**, och klicka på **OK**.
1. Klicka på **ställa in den här identitetsprovidern**
1. För **metadata_url**, ange följande URL, där `yourtenant` ersätts med namnet på Azure AD-klienten (t.ex. `contoso.com`):

    ```Console
    https://login.microsoftonline.com/yourtenant/.well-known/openid-configuration
    ```
1. För den **klient-ID** och **klienthemlighet**, ange program-ID och nyckel i föregående avsnitt.
1. Behåll standardvärdet för **omfång**, vilket ska vara inställd på `openid`.
1. Behåll standardvärdet för **svarstypen**, vilket ska vara inställd på `code`.
1. Behåll standardvärdet för **svarsläget**, vilket ska vara inställd på `form_post`.
1. Alternativt kan du ange ett värde för **domän** (t.ex. `ContosoAD`). Det här är värdet som ska användas när du refererar till den här identitetsprovider som använder *domain_hint* i begäran. 
1. Klicka på **OK**.
1. Klicka på **mappa den här identitetsproviderns anspråk**.
1. För **användar-ID**, ange `oid`.
1. För **visningsnamn**, ange `name`.
1. För **Förnamn**, ange `given_name`.
1. För **efternamn**, ange `family_name`.
1. För **e-post**, ange `unique_name`
1. Klicka på **OK**, och sedan **skapa** att spara din konfiguration.

## <a name="next-steps"></a>Nästa steg

Lägg till den nyligen skapade Azure AD-identitetsprovider till en inbyggd princip och ge feedback till [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
