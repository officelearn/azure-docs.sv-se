---
title: 'Självstudiekurs: Azure Active Directory-integrering med MyWorkDrive | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: jeedes
ms.openlocfilehash: 60fdd9b0a8fb272da885df97e39804a98e48de67
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478862"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>Självstudiekurs: Integrera MyWorkDrive med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar MyWorkDrive med Azure Active Directory (Azure AD). När du integrerar MyWorkDrive med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till MyWorkDrive.
* Gör att användarna automatiskt loggas in på MyWorkDrive med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri provperiod [här.](https://azure.microsoft.com/pricing/free-trial/)
* En enkel inloggning (SSO) aktiverad prenumeration i MyWorkDrive.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. MyWorkDrive stöder **SP** och **IDP** initierad SSO

## <a name="adding-myworkdrive-from-the-gallery"></a>Lägga till MyWorkDrive från galleriet

Om du vill konfigurera integreringen av MyWorkDrive i Azure AD måste du lägga till MyWorkDrive från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **MyWorkDrive** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **MyWorkDrive** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med MyWorkDrive med en testanvändare som heter **Britta Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i MyWorkDrive.

Så här konfigurerar och testar du Azure AD SSO med MyWorkDrive:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera MyWorkDrive SSO](#configure-myworkdrive-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa MyWorkDrive-testanvändare](#create-myworkdrive-test-user)** – om du vill ha en motsvarighet till Britta Simon i MyWorkDrive som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **MyWorkDrive-programintegrering** på [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge på sidan **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Ange ditt eget företags Värdnamn för MyWorkDrive Server:t.
    > 
    > Svarswebbadress: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > Url för inloggning:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Kontakta [MyWorkDrive-supportteamet](mailto:support@myworkdrive.com) om du är osäker på hur du konfigurerar ditt eget värdnamn och TLS/SSL-certifikat för dessa värden.

1. Klicka på knappen Kopiera i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** för att kopiera Url till App **Federationsmetadata** till Urklipp.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>Konfigurera MyWorkDrive SSO

1. Om du vill automatisera konfigurationen i MyWorkDrive måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

1. När du har lagt till tillägget i webbläsaren klickar du på **Setup MyWorkDrive** leder dig till MyWorkDrive-programmet. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på MyWorkDrive. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-4.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

1. Om du vill konfigurera MyWorkDrive manuellt loggar du in på MyWorkDrive som säkerhetsadministratör i ett annat webbläsarfönster.

1. Klicka på **ENTERPRISE** på MyWorkDrive Server på administratörspanelen och utför följande:

    ![Administratören](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Aktivera **SAML/ADFS SSO**.

    b. Välj **SAML - Azure AD**

    c. Klistra in värdet för **Url-url för App Federation Metadata** som du har kopierat från Azure App Federation Metadata Url i textrutan för Azure App Federation **Metadata** Url.

    d. Klicka på **Spara**

    > [!NOTE]
    > Mer information finns i [supportartikeln För MyWorkDrive Azure AD](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter Britta Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till MyWorkDrive.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **MyWorkDrive**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-myworkdrive-test-user"></a>Skapa MyWorkDrive-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i MyWorkDrive. Arbeta med [MyWorkDrive-supportteamet](mailto:support@myworkdrive.com) för att lägga till användarna i MyWorkDrive-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen MyWorkDrive på åtkomstpanelen bör du automatiskt loggas in på den MyWorkDrive som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)