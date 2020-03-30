---
title: Konfigurera SaaS-appar för B2B-samarbete – Azure AD
description: Kod- och PowerShell-exempel för Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c73a14c2a8cd063672bd0998368ca660f52cd5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272958"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Konfigurera SaaS-appar för B2B-samarbete

Azure Active Directory (Azure AD) B2B-samarbete fungerar med de flesta appar som integreras med Azure AD. I det här avsnittet går vi igenom instruktioner för att konfigurera några populära SaaS-appar för användning med Azure AD B2B.

Innan du tittar på appspecifika instruktioner, här är några tumregler:

* För de flesta av apparna måste användarinställningarna ske manuellt. Det vill säga, användare måste skapas manuellt i appen också.

* För appar som stöder automatisk installation, till exempel Dropbox, skapas separata inbjudningar från apparna. Användarna måste vara säkra på att acceptera varje inbjudan.

* I användarattributen anger du alltid **användaridentifieraren** på **user.mail**för att minska eventuella problem med den manglade användarprofildisken (UPD) hos gästanvändare .


## <a name="dropbox-business"></a>Dropbox-företag

Om du vill att användare ska kunna logga in med sitt organisationskonto måste du konfigurera Dropbox Business manuellt så att de använder Azure AD som en SAML-identitetsleverantör (Security Assertion Markup Language). Om Dropbox Business inte har konfigurerats för att göra det kan det inte fråga eller på annat sätt tillåta användare att logga in med Azure AD.

1. Om du vill lägga till Dropbox Business-appen i Azure AD väljer du **Enterprise-program** i den vänstra rutan och klickar sedan på **Lägg till**.

   ![Knappen "Lägg till" på sidan Företagsprogram](media/configure-saas-apps/add-dropbox.png)

2. I fönstret **Lägg till ett program** anger du **dropbox** i sökrutan och väljer sedan Dropbox **för företag** i resultatlistan.

   ![Sök efter "dropbox" på sidan Lägg till ett program](media/configure-saas-apps/add-app-dialog.png)

3. På sidan **Enkel inloggning** väljer du **Enkel inloggning** i den vänstra rutan och anger sedan **user.mail** i rutan **Användaridentifierare.** (Den är inställd som UPN som standard.)

   ![Konfigurera enkel inloggning för appen](media/configure-saas-apps/configure-app-sso.png)

4. Om du vill hämta certifikatet som ska användas för Dropbox-konfiguration väljer du **Konfigurera DropBox**och väljer sedan URL för **simla tjänster** med enkel inloggning i listan.

   ![Ladda ned certifikatet för Dropbox-konfiguration](media/configure-saas-apps/download-certificate.png)

5. Logga in på Dropbox med inloggningsadressen från sidan **Enkel inloggning.**

   ![Skärmbild som visar inloggningssidan för Dropbox](media/configure-saas-apps/sign-in-to-dropbox.png)

6. Välj **Admin Console**på menyn .

   ![Länken "Administratörskonsol" på Dropbox-menyn](media/configure-saas-apps/dropbox-menu.png)

7. Välj **Mer**i dialogrutan **Autentisering** och överför sedan certifikatet och anger sedan den gemensamma inloggningsadressen i rutan **Logga in.**

   ![Länken "Mer" i dialogrutan Komprimerad autentisering](media/configure-saas-apps/dropbox-auth-01.png)

   !["Logga in URL" i dialogrutan Utökad autentisering](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Om du vill konfigurera automatisk användarkonfiguration i Azure-portalen väljer du **Etablering** i den vänstra rutan, väljer **Automatisk** i rutan **Etableringsläge** och väljer sedan **Auktorisera**.

   ![Konfigurera automatisk användaretablering i Azure-portalen](media/configure-saas-apps/set-up-automatic-provisioning.png)

När gäst- eller medlemsanvändare har konfigurerats i Dropbox-appen får de en separat inbjudan från Dropbox. Om du vill använda Dropbox enkel inloggning måste inbjudna acceptera inbjudan genom att klicka på en länk i den.

## <a name="box"></a>Box
Du kan göra det möjligt för användare att autentisera Box-gästanvändare med sitt Azure AD-konto med hjälp av federation som baseras på SAML-protokollet. I den här proceduren laddar du upp metadata till Box.com.

1. Lägg till Box-appen från företagsapparna.

2. Konfigurera enkel inloggning i följande ordning:

   ![Skärmbild som visar konfigurationsinställningarna för enkel inloggning](media/configure-saas-apps/configure-box-sso.png)

   a. Kontrollera att inloggnings-URL:en är korrekt inställd för Box i Azure-portalen i rutan **Logga in.** Den här webbadressen är webbadressen till din Box.com-klient. Den bör följa namngivningskonventionen *https://.box.com*.  
   **Identifieraren** gäller inte för den här appen, men den visas fortfarande som ett obligatoriskt fält.

   b. I rutan **Användaridentifierare** anger du **user.mail** (för SSO för gästkonton).

   c. Klicka på **Skapa nytt certifikat**under **SAML-signeringscertifikat**.

   d. Om du vill börja konfigurera din Box.com-klientorganisation så att den använder Azure AD som identitetsleverantör laddar du ned metadatafilen och sparar den sedan på din lokala enhet.

   e. Vidarebefordra metadatafilen till supportteamet för rutan, som konfigurerar enkel inloggning åt dig.

3. För automatisk användarkonfiguration för Azure AD väljer du **Etablering**i den vänstra rutan och väljer sedan **Auktorisera**.

   ![Auktorisera Azure AD för anslutning till Box](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Precis som Dropbox-inbjudna måste Box-inbjudna lösa in sin inbjudan från Box-appen.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Dynamiska grupper och B2B-samarbete](use-dynamic-groups.md)
- [B2B-mappning av användaranspråk för samarbete](claims-mapping.md)
- [Extern delning av Office 365](o365-external-user.md)

