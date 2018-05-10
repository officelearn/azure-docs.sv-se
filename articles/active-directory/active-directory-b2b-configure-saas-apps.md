---
title: Konfigurera SaaS-appar för B2B-samarbete i Azure Active Directory | Microsoft Docs
description: Koden och PowerShell-exempel för Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 36fe82311b180a1dbe4fb81b78f10482cb8bb4ac
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Konfigurera SaaS-appar för B2B-samarbete

Azure Active Directory (AD Azure) B2B-samarbete fungerar med de flesta appar som integreras med Azure AD. I det här avsnittet går vi igenom instruktioner för att konfigurera vissa populära SaaS-appar för användning med Azure AD B2B.

Innan du tittar på app-specifik anvisningarna är här några tumregel:

* För de flesta appar måste användaren ske manuellt. Det vill säga skapas användare manuellt i appen även.

* För appar som har stöd för automatisk installation, till exempel Dropbox, skapas separat inbjudningar från apparna. Användare måste vara säker på att du accepterar varje inbjudan.

* I användarattributen för att åtgärda eventuella problem med felaktig användarprofil-disk (UPD) i gästanvändare, alltid ange **användar-ID** till **user.mail**.


## <a name="dropbox-business"></a>Dropbox företag

Om du vill att användarna ska logga in med sina organisation, måste du manuellt konfigurera Dropbox företag om du vill använda Azure AD som en identitetsleverantör Security Assertion Markup Language (SAML). Om Dropbox företag inte har konfigurerats för att göra det, kan inte fråga eller annars tillåter användare att logga in med Azure AD.

1. Om du vill lägga till Dropbox Business-app till Azure AD, Välj **företagsprogram** i den vänstra rutan, och klicka sedan på **Lägg till**.

  ![Knappen ”Lägg till” på sidan Enterprise program](media/active-directory-b2b-configure-saas-apps/add-dropbox.png)

2. I den **lägga till ett program** fönstret Ange **dropbox** i sökrutan och väljer sedan **Dropbox för företag** i resultatlistan.

  ![Sök efter ”dropbox” på Lägg till en program-sida](media/active-directory-b2b-configure-saas-apps/add-app-dialog.png)

3. På den **enkel inloggning** väljer **enkel inloggning** i den vänstra rutan och ange sedan **user.mail** i den **användar-ID** rutan. (Det är angivet som UPN som standard.)

  ![Konfigurera enkel inloggning för appen](media/active-directory-b2b-configure-saas-apps/configure-app-sso.png)

4. För att hämta certifikatet som ska användas för Dropbox-konfiguration, markera **konfigurera DropBox**, och välj sedan **SAML logga på tjänst-URL för enkel** i listan.

  ![Hämta certifikatet för Dropbox-konfiguration](media/active-directory-b2b-configure-saas-apps/download-certificate.png)

5. Logga in till Dropbox med den inloggnings-URL från den **enkel inloggning** sidan.

  ![Sidan för Dropbox](media/active-directory-b2b-configure-saas-apps/sign-in-to-dropbox.png)

6. Välj på menyn **administratörskonsolen**.

  ![Länken ”Admin Console” på Dropbox-menyn](media/active-directory-b2b-configure-saas-apps/dropbox-menu.png)

7. I den **autentisering** dialogrutan **mer**, ladda upp certifikatet och sedan, i den **logga in URL: en** ange SAML enkel inloggning URL: en.

  ![Länken ”Mer” i dialogrutan minimerade autentisering](media/active-directory-b2b-configure-saas-apps/dropbox-auth-01.png)

  ![I ”logga in URL: en” i dialogrutan för utökade autentisering](media/active-directory-b2b-configure-saas-apps/paste-single-sign-on-URL.png)

8. Om du vill konfigurera automatisk användarinställningar i Azure portal, Välj **etablering** i den vänstra rutan, Välj **automatisk** i den **etablering läge** och välj sedan **auktorisera**.

  ![Konfigurera automatisk användaretablering i Azure-portalen](media/active-directory-b2b-configure-saas-apps/set-up-automatic-provisioning.png)

När gästen eller medlem användare har ställts in i appen Dropbox, får de en separat inbjudan från Dropbox. Om du vill använda Dropbox enkel inloggning acceptera inbjudna vill inbjudan genom att klicka på en länk i den.

## <a name="box"></a>Box
Du kan låta användare autentisera rutan gästanvändare med sina Azure AD-kontot med hjälp av federation som baseras på SAML-protokoll. Den här metoden Överför metadata till Box.com.

1. Lägg till Box-app från enterprise-appar.

2. Konfigurera enkel inloggning i följande ordning:

  ![Konfigurera rutan enkel inloggning](media/active-directory-b2b-configure-saas-apps/configure-box-sso.png)

 a. I den **inloggning URL** kontrollerar du att den inloggnings-URL är inställd på rätt sätt för rutan i Azure-portalen. Denna URL är Webbadressen till din Box.com-klient. Det bör följa en namngivningskonvention *https://.box.com*.  
 Den **identifierare** gäller inte för den här appen, men den fortfarande visas som ett obligatoriskt fält.

 b. I den **användar-ID** ange **user.mail** (för enkel inloggning för gästkonton).

 c. Under **SAML-signeringscertifikat**, klickar du på **Skapa nytt certifikat**.

 d. Om du vill börja konfigurera din Box.com klient om du vill använda Azure AD som en identitetsleverantör och hämta metadatafilen och spara den på din lokala enhet.

 e. Vidarebefordra metadatafil till rutan supportteam, som konfigurerar enkel inloggning för dig.

3. Azure AD automatisk användarinställningar, i den vänstra rutan, Välj **etablering**, och välj sedan **auktorisera**.

  ![Verifiera Azure AD för att ansluta till rutan](media/active-directory-b2b-configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Som Dropbox inbjudna lösa rutan inbjudna vill sina inbjudan från Box-app.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Dynamiska grupper och B2B-samarbete](active-directory-b2b-dynamic-groups.md)
- [B2B-samarbete användaranspråk mappning](active-directory-b2b-claims-mapping.md)
- [Extern delning av Office 365](active-directory-b2b-o365-external-user.md)

