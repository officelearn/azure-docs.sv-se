---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med 8x8 | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och 8x8.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c598222978a1c831be6f5e9db9eb87b2d6b6b96
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78968655"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>Självstudiekurs: Azure Active Directory enkel inloggning (SSO) integration med 8x8

I den här självstudien får du lära dig hur du integrerar 8x8 med Azure Active Directory (Azure AD). När du integrerar 8x8 med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till 8x8.
* Gör att användarna automatiskt loggas in på 8x8 med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En 8x8-prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* 8x8 stöder **SP och IDP** initierade SSO

* När du har konfigurerat 8x8 kan du framtvinga sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-8x8-from-the-gallery"></a>Lägga till 8x8 från galleriet

Om du vill konfigurera integreringen av 8x8 i Azure AD måste du lägga till 8x8 från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **8x8** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **8x8** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-8x8"></a>Konfigurera och testa en azure AD-inloggning för 8x8

Konfigurera och testa Azure AD SSO med 8x8 med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i 8x8.

Om du vill konfigurera och testa Azure AD SSO med 8x8 slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera 8x8 SSO](#configure-8x8-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa 8x8-testanvändare](#create-8x8-test-user)** – om du vill ha en motsvarighet till B.Simon i 8x8 som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan **8x8-programintegrering** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**. **8x8**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. Skriv en URL i textrutan **Identifierare:**`https://sso.8x8.com/saml2`

    b. Skriv en URL i textrutan **Svars-URL**: `https://sso.8x8.com/saml2`

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn. Du kommer att använda certifikatet senare i självstudien i avsnittet **Konfigurera 8x8 SSO.**

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera 8x8** kopierar du webbadresserna och använder dessa URL-värden senare i självstudien.

    ![Kopiera konfigurations-URL:er](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till 8x8.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **8x8**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-8x8-sso"></a>Konfigurera 8x8 SSO

Nästa del av handledningen beror på vilken typ av prenumeration du har med 8x8.

* För kunder i 8x8-versioner och X-serien som använder Configuration Manager för administration finns [i Konfigurera 8x8 Configuration Manager](#configure-8x8-configuration-manager).
* För virtuella Office-kunder som använder Account Manager för administration läser du [Konfigurera 8x8 Account Manager](#configure-8x8-account-manager).

### <a name="configure-8x8-configuration-manager"></a>Konfigurera konfigurationshanteraren för 8x8

1. Logga in på 8x8 [Configuration Manager](https://vo-cm.8x8.com/).

1. Klicka på **Identitetshantering**på startsidan.

    ![Konfigurationshanteraren för 8x8](./media/8x8virtualoffice-tutorial/configure1.png)

1. Kontrollera **Enkel inloggning (SSO)** och välj sedan **Microsoft Azure AD**.

    ![Konfigurationshanteraren för 8x8](./media/8x8virtualoffice-tutorial/configure2.png)

1. Kopiera de tre url:erna och signera certifikat från sidan **Konfigurera enkel inloggning med SAML** i Azure AD till avsnittet Microsoft Azure AD **SAML-inställningar** i konfigurationshanteraren för 8x8.

    ![Konfigurationshanteraren för 8x8](./media/8x8virtualoffice-tutorial/configure3.png)

    a. Kopiera **inloggningsadress** till **IDP-inloggnings-URL.**

    b. Kopiera **Azure AD-identifierare** till **URL för IDP-utfärdare/URN**.

    c. Kopiera **URL för utloggning** till **URL för IDP-utloggning**.

    d. Ladda ner **certifikat (Base64)** och ladda upp till **Certifikat**.

    e. Klicka på **Spara**.

### <a name="configure-8x8-account-manager"></a>Konfigurera 8x8-kontohanteraren

1. Logga in på 8x8 Virtual Office-klienten som administratör.

1. Välj **Virtual Office Account Mgr** (Kontohanteraren i Virtual Office) i programfönstret.

    ![Konfigurera på programsidan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Välj **Business** (Företag) som kontotyp för hantering och klicka på **Sign In** (Logga in).

    ![Konfigurera på programsidan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Klicka på fliken **ACCOUNTS** (KONTON) i menylistan.

    ![Konfigurera på programsidan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Klicka på **Single Sign On** (Enkel inloggning) i listan över konton.

    ![Konfigurera på programsidan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. Välj autentiseringsmetoden **Single Sign On** (Enkel inloggning) och klicka på **SAML**.

    ![Konfigurera på programsidan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. Utför följande steg i avsnittet **SAML Single Sign on** (Enkel inloggning med SAML):

    ![Konfigurera på programsidan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. I textrutan **Sign In URL** (Inloggnings-URL) klistrar du in **inloggnings-URL-värdet** som du har kopierat från Azure-portalen.

    b. I textrutan **Sign Out URL** (Utloggnings-URL) klistrar du in **utloggnings-URL-värdet** som du har kopierat från Azure-portalen.

    c. I textrutan **Issuer URL** (Utfärdar-URL) klistrar du in det värde för **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    d. Klicka på **Browse** (Bläddra) för att ladda upp det certifikat som du har laddat ned från Azure-portalen.

    e. Klicka på knappen **Spara**.

### <a name="create-8x8-test-user"></a>Skapa 8x8-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i 8x8. Arbeta med [8x8 supportteam](https://www.8x8.com/about-us/contact-us) för att lägga till användarna i 8x8-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på 8x8-panelen på åtkomstpanelen ska du automatiskt loggas in på den 8x8 som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova 8x8 med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hur man skyddar 8x8 med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)