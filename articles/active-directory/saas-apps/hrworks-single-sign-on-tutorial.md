---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med HRworks Single Sign-On | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HRworks Enkel inloggning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c4c5d434-3f8a-411e-83a5-c3d5276ddc0a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b20ce5f754333aec78513e32901b0608f8bee3b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75638779"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med HRworks Enkel inloggning

I den här självstudien får du lära dig hur du integrerar HRworks Enkel inloggning med Azure Active Directory (Azure AD). När du integrerar HRworks Enkel inloggning med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till HRworks Enkel inloggning.
* Gör att användarna automatiskt loggas in på HRworks Single Sign-On med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* HRworks Enkel inloggning enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* HRworks Single Sign-On stöder **SP** initierade SSO

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Lägga till HRworks Single Sign-On från galleriet

Om du vill konfigurera integreringen av HRworks Single Sign-On i Azure AD måste du lägga till HRworks Single Sign-On från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **HRworks Enkel inloggning** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **HRworks Enkel inloggning** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>Konfigurera och testa en enkel Azure AD-inloggning för HRworks Enkel inloggning

Konfigurera och testa Azure AD SSO med HRworks Single Sign-On med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i HRworks Single Sign-On.

Om du vill konfigurera och testa Azure AD SSO med HRworks Single Sign-On slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera HRworks Single Sign-On SSO](#configure-hrworks-single-sign-on-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa HRworks Single Sign-On testanvändare](#create-hrworks-single-sign-on-test-user)** - att ha en motsvarighet till B.Simon i HRworks Single Sign-On som är kopplad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet **Hantera** på sidan **HRworks Single Sign-On-programintegration** och väljer **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [HRworks Supportteam för enkel inloggningsklient](mailto:nadja.sommerfeld@hrworks.de) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera HRworks Enkel inloggning.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till HRworks Enkel inloggning.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **HRworks Single Sign-On**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-hrworks-single-sign-on-sso"></a>Konfigurera HRworks Single Sign-On SSO

1. Om du vill automatisera konfigurationen i HRworks Single Sign-On måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

1. När du har lagt till tillägget i webbläsaren klickar du på **Konfigurera HRworks Single Sign-On** leder dig till HRworks Single Sign-On-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på HRworks Single Sign-On. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-4.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

1. Om du vill konfigurera HRworks Single Sign-On manuellt öppnar du ett nytt webbläsarfönster och loggar in på din HRworks Single Sign-On-företagswebbplats som administratör och utför följande steg:

1. Klicka på **Administratör** > **Basics** > **Security** > Enkel**inloggning** från vänster sida av menyraden och utför följande steg:

    ![Konfigurera enkel inloggning](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Markera rutan **Använd enkel inloggning.**

    b. Välj **XML-metadata** som **inmatningsmetod för metadata**.

    c. Välj **Individuell nameID-identifierare** som **värde för NameID**.

    d. Öppna metadata-XML-koden som du hämtade från Azure-portalen i Anteckningar, kopiera dess innehåll och klistra sedan in den i textrutan **Metadata.**

    e. Klicka på **Spara**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Skapa HRworks Single Sign-On test användare

Om du vill aktivera Azure AD-användare loggar du in på HRworks Single Sign-On, de måste etableras i HRworks Single Sign-On. I HRworks Single Sign-On är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på HRworks Enkel inloggning som administratör.

1. Klicka på > **Administratörspersoner** > **Ny** > **person** från vänster i menyraden. **Administrator**

     ![Konfigurera enkel inloggning](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. Klicka på **Nästa**på popup-videon .

    ![Konfigurera enkel inloggning](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. Fyll i respektive information som **Förnamn**, **Efternamn** och klicka på **Skapa**i popup-fältet Skapa ny person med land **för juridiska termer.**

    ![Konfigurera enkel inloggning](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på hrworks enkel inloggningspanel på åtkomstpanelen ska du automatiskt loggas in på den enkel inloggning för HRworks som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova HRworks Enkel inloggning med Azure AD](https://aad.portal.azure.com/)