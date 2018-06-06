---
title: Lägg till en Azure AD-provider med principer för inbyggda i Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du lägger till en identitetsleverantör för öppna ID Connect (Azure AD).
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4e756a4bf3d9e42b47c0b96a6bf73e8e2b267b77
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712393"
---
# <a name="azure-active-directory-b2c-sign-in-using-azure-ad-accounts-through-a-built-in-policy"></a>Azure Active Directory B2C: Logga in med Azure AD-konton via en inbyggd princip

>[!NOTE]
> Den här funktionen är tillgänglig som förhandsversion. Använd inte funktionen i produktionsmiljöer.

Den här artikeln visar hur du aktiverar inloggning för användare från en specifik Azure Active Directory (AD Azure) organisation inbyggda principer.

## <a name="create-an-azure-ad-app"></a>Skapa en Azure AD-app

Aktivera inloggning för användare från en specifik Azure AD-organisation, måste du registrera ett program i organisations Azure AD-klient.

>[!NOTE]
> Vi använder ”contoso.com” för organisations Azure AD-klient och ”fabrikamb2c.onmicrosoft.com” som Azure AD B2C-klient i följande instruktioner.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj ditt konto på den översta raden. Från den **Directory** Välj organisations Azure AD-klient där du ska registrera ditt program (contoso.com).
1. Välj **alla tjänster** i den vänstra rutan och söka efter ”App registreringar”.
1. Välj **Ny programregistrering**.
1. Ange ett namn för ditt program (till exempel `Azure AD B2C App`).
1. Välj **Webbapp/API** som programtyp.
1. För **inloggnings-URL**, ange följande URL, där `yourtenant` ersättas med namnet på din Azure AD B2C-klient (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Värdet för ”yourtenant” måste vara gemener i den **inloggnings-URL**.

    ```Console
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Spara det program-ID som du vill använda i nästa avsnitt som klient-ID.
1. Under den **inställningar** bladet väljer **nycklar**.
1. Ange en **nyckeln beskrivning** under den **lösenord** och ange den **varaktighet** till ”upphör aldrig att gälla”. 
1. Klicka på **spara**, och Skriv ner den resulterande nyckeln **värdet**, som du vill använda i nästa avsnitt som klienthemligheten.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>Konfigurera Azure AD som en identitetsleverantör i din klientorganisation

1. Välj ditt konto på den översta raden. Från den **Directory** Välj Azure AD B2C-klient (fabrikamb2c.onmicrosoft.com).
1. [Gå till menyn Azure AD B2C inställningar](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) i Azure-portalen.
1. På menyn Azure AD B2C-inställningar klickar du på **identitetsleverantörer**.
1. Klicka på **+Lägg till** överst på bladet.
1. Ange ett eget **namn** för providerkonfigurationen identitet. Ange till exempel ”Contoso Azure AD”.
1. Klicka på **identitet providertyp**väljer **öppna ID Connect**, och klicka på **OK**.
1. Klicka på **ställa in den här identitetsleverantören.**
1. För **url för tjänstmetadata**, ange följande URL, där `yourtenant` ersättas med namnet på Azure AD-klienten (t.ex. `contoso.com`):

    ```Console
    https://login.microsoftonline.com/yourtenant/.well-known/openid-configuration
    ```
1. För den **klient-ID** och **klienthemlighet**, ange program-ID och nyckel från föregående avsnitt.
1. Behåll standardvärdet för **omfång**, som ska anges till `openid`.
1. Behåll standardvärdet för **svarstyp**, som ska anges till `code`.
1. Behåll standardvärdet för **svar läge**, som ska anges till `form_post`.
1. Du kan också ange ett värde för **domän** (t.ex. `ContosoAD`). Detta är värdet som ska användas när du refererar till den här identitetsleverantör med *domain_hint* i begäran. 
1. Klicka på **OK**.
1. Klicka på **mappa den här identitetsleverantör anspråk**.
1. För **användar-ID**, ange `oid`.
1. För **visningsnamn**, ange `name`.
1. För **Förnamn**, ange `given_name`.
1. För **efternamn**, ange `family_name`.
1. För **e-post**, ange `unique_name`
1. Klicka på **OK**, och sedan **skapa** att spara konfigurationen.

## <a name="next-steps"></a>Nästa steg

Lägg till den nyligen skapade Azure AD-identitetsprovider till en inbyggd princip och ge feedback till [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
