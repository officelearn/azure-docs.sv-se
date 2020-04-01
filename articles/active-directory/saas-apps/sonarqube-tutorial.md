---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Sonarqube | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Sonarqube.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b01665e7-c3d0-4393-9286-d5bcf8cf6add
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 571d8849fd7cae5c872a56182858848dbb43ef42
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026681"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sonarqube"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Sonarqube

I den här självstudien får du lära dig hur du integrerar Sonarqube med Azure Active Directory (Azure AD). När du integrerar Sonarqube med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Sonarqube.
* Gör att användarna automatiskt loggas in på Sonarqube med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Sonarqube enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Sonarqube stöder **SP** initierade SSO

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-sonarqube-from-the-gallery"></a>Lägga till Sonarqube från galleriet

Om du vill konfigurera integreringen av Sonarqube i Azure AD måste du lägga till Sonarqube från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Sonarqube** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Sonarqube** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sonarqube"></a>Konfigurera och testa en azure AD-inloggning för Sonarqube

Konfigurera och testa Azure AD SSO med Sonarqube med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Sonarqube.

Om du vill konfigurera och testa Azure AD SSO med Sonarqube slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Sonarqube SSO](#configure-sonarqube-sso)** – för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Sonarqube-testanvändare](#create-sonarqube-test-user)** – om du vill ha en motsvarighet till B.Simon i Sonarqube som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Hantera** på sidan [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    I rutan **Inloggnings-URL** anger du en URL: 

    * **För produktionsmiljö**

        `https://servicessonar.corp.microsoft.com/`

    * **För dev-miljö**

        `https://servicescode-dev.westus.cloudapp.azure.com`

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Sonarqube** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Sonarqube.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Sonarqube**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-sonarqube-sso"></a>Konfigurera Sonarqube SSO

1. Öppna ett nytt webbläsarfönster och logga in på din Sonarqube-företagswebbplats som administratör.

2. Installera SAML plugin från sonarqube marknaden.

3. Längst upp till vänster på sidan, klicka på **ADMIN** och navigera sedan till **SAML**.

4. Gör följande på **SAML-sidan:**

    ![Konfiguration av Sonarqube](./media/sonarqube-tutorial/config01.png)

    a. Växla alternativet **Aktiverad** till **ja**.

    b. I textrutan **Program-ID** anger du namnet som **sonarqube**.

    c. Ange namnet som **SAML**i textrutan **Providernamn** .

    d. Klistra in värdet för **Azure AD-identifierare**i textrutan **Provider ID** som du har kopierat från Azure-portalen.

    e. Klistra in värdet för **inloggnings-URL**i textrutan **SAML-inloggningsadress** , som du har kopierat från Azure-portalen.

    f. Öppna Base64-kodat certifikat i anteckningar, kopiera innehållet och klistra in det i textrutan **Providercertifikat.**

    g. Ange värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`i textrutan **för användarloginattribut i SAML-användarlogin.**

    h. Ange värdet `http://schemas.microsoft.com/identity/claims/displayname`i textrutan FÖR ATTRIBUT FÖR **SIM-användarnamn** .

    i. Ange värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`i textrutan **för användarmeddelandet för e-post.**

    j. Klicka på **Spara**.

### <a name="create-sonarqube-test-user"></a>Skapa Sonarqube-testanvändare

I det här avsnittet skapar du en användare som heter B.Simon i Sonarqube. Arbeta med [Sonarqube Client supportteam](https://www.sonarsource.com/support/) för att lägga till användarna i Sonarqube-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Sonarqube på åtkomstpanelen bör du automatiskt loggas in på den Sonarqube som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Sonarqube med Azure AD](https://aad.portal.azure.com/)

