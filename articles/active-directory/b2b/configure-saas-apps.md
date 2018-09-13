---
title: Konfigurera SaaS-appar för B2B-samarbete i Azure Active Directory | Microsoft Docs
description: Kod och PowerShell-exempel för Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 409cce16d256c408aaa245b97d17171669a2cf41
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35647270"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Konfigurera SaaS-appar för B2B-samarbete

Azure Active Directory (Azure AD) B2B-samarbete fungerar med de flesta appar som integreras med Azure AD. I det här avsnittet ska går vi igenom anvisningarna för att konfigurera vissa populära SaaS-appar för användning med Azure AD B2B.

Innan du tittar på app-specifika anvisningar, är här några tumregel:

* För de flesta appar måste användare ske manuellt. Det vill säga måste användare skapas manuellt i appen även.

* För appar som stöder automatisk installation, till exempel Dropbox, skapas separat inbjudningar från apparna. Användarna måste vara noga med att godkänna varje inbjudan.

* I användarattribut för att lösa eventuella problem med felaktig användarprofil-disk (UPD) i gästanvändare, alltid ange **användaridentifierare** till **user.mail**.


## <a name="dropbox-business"></a>Dropbox-företag

Om du vill att användarna ska logga in med deras organisation, måste du manuellt konfigurera Dropbox företag för att använda Azure AD som en identitetsprovider för Security Assertion Markup Language (SAML). Om Dropbox företag inte har konfigurerats för att göra det, kan inte fråga eller annars tillåter användare att logga in med Azure AD.

1. Om du vill lägga till Dropbox Business-app till Azure AD, Välj **företagsprogram** i den vänstra rutan och klicka sedan på **Lägg till**.

  ![Knappen ”Lägg till” på sidan för Enterprise-program](media/configure-saas-apps/add-dropbox.png)

2. I den **lägga till ett program** fönstret, ange **dropbox** i sökrutan och välj sedan **Dropbox for Business** i resultatlistan.

  ![Sök efter ”dropbox” på bladet Lägg till en sida för programmet](media/configure-saas-apps/add-app-dialog.png)

3. På den **enkel inloggning** väljer **enkel inloggning** i den vänstra rutan och sedan ange **user.mail** i den **användaridentifierare** box. (Den är inställd som UPN som standard.)

  ![Konfigurera enkel inloggning för appen](media/configure-saas-apps/configure-app-sso.png)

4. För att hämta certifikatet som ska användas för konfiguration av Dropbox, Välj **konfigurera DropBox**, och välj sedan **SAML enkel inloggning på tjänstens URL** i listan.

  ![Nedladdningen av certifikatet för Dropbox-konfiguration](media/configure-saas-apps/download-certificate.png)

5. Logga in på Dropbox med inloggnings-URL: en från den **enkel inloggning** sidan.

  ![Inloggningssidan för Dropbox](media/configure-saas-apps/sign-in-to-dropbox.png)

6. På menyn, Välj **administratörskonsolen**.

  ![Länken ”administratörskonsolen” på menyn Dropbox](media/configure-saas-apps/dropbox-menu.png)

7. I den **autentisering** dialogrutan **mer**, ladda upp certifikatet och klicka sedan på **loggar du in URL: en** anger SAML enkel inloggnings-URL: en.

  ![”Mer”-länken i dialogrutan för komprimerad autentisering](media/configure-saas-apps/dropbox-auth-01.png)

  ![Den ”logga in URL: en” i dialogrutan för utökade autentisering](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Om du vill konfigurera automatisk användarinställningar i Azure portal, Välj **etablering** i den vänstra rutan väljer **automatisk** i den **etablering läge** och sedan väljer  **Auktorisera**.

  ![Konfigurera automatisk användaretablering i Azure portal](media/configure-saas-apps/set-up-automatic-provisioning.png)

När användare Gäst eller medlem har lagts upp i Dropbox-app, får de en separat inbjudan från Dropbox. Om du vill använda Dropbox enkel inloggning, tacka inbjudna Ja till inbjudan genom att klicka på en länk i den.

## <a name="box"></a>Box
Du kan låta användare autentisera Box gästanvändare med sina Azure AD-konto med hjälp av federation som baseras på SAML-protokoll. I den här proceduren måste överföra du metadata till Box.com.

1. Lägg till Box-app från enterprise-appar.

2. Konfigurera enkel inloggning i följande ordning:

  ![Konfigurera Box enkel inloggning](media/configure-saas-apps/configure-box-sso.png)

 a. I den **inloggnings-URL** ser du till att inloggnings-URL: en har angetts på rätt sätt för Box i Azure-portalen. Denna URL är Webbadressen till din Box.com-klient. Den bör följa namngivningskonventionen *https://.box.com*.  
 Den **identifierare** gäller inte för den här appen, men den fortfarande visas som ett obligatoriskt fält.

 b. I den **användaridentifierare** anger **user.mail** (för enkel inloggning för gästkonton).

 c. Under **SAML-signeringscertifikat**, klickar du på **Skapa nytt certifikat**.

 d. Om du vill börja konfigurera din Box.com-klient för att använda Azure AD som identitetsprovider och hämta för metadatafilen och spara den på den lokala enheten.

 e. Vidarebefordra metadatafilen rutan supportteam, som konfigurerar enkel inloggning för dig.

3. Azure AD automatisk användarinställningar, i den vänstra rutan, Välj **etablering**, och välj sedan **auktorisera**.

  ![Auktorisera Azure AD för att ansluta till Box](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

T.ex. Dropbox inbjudna Box inbjudna måste lösa in sin inbjudan från Box-app.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar på Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Dynamiska grupper och B2B-samarbete](use-dynamic-groups.md)
- [B2B-samarbete mappning av användaranspråk](claims-mapping.md)
- [Extern delning av Office 365](o365-external-user.md)

