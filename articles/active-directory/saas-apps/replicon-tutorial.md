---
title: 'Självstudiekurs: Azure Active Directory-integrering med Replicon | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Replicon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27615b0c76b5c23bbc79788431b0e909b8bf22a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092771"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Självstudiekurs: Integrera Replicon med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Replicon med Azure Active Directory (Azure AD). När du integrerar Replicon med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Replicon.
* Aktivera dina användare så att de automatiskt loggas in på Replicon med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri provperiod [här.](https://azure.microsoft.com/pricing/free-trial/)
* Replicon enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. Replicon stöder **SP** initierade SSO.

## <a name="adding-replicon-from-the-gallery"></a>Lägga till Replicon från galleriet

Om du vill konfigurera integreringen av Replicon i Azure AD måste du lägga till Replicon från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Replicon** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Replicon** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Replicon med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Replicon.

Om du vill konfigurera och testa Azure AD SSO med Replicon slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Replicon SSO](#configure-replicon-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
5. **[Skapa Replicon-testanvändare](#create-replicon-test-user)** – om du vill ha en motsvarighet till B.Simon i Replicon som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan Hantera på sidan **Replicon** [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**. **Manage**
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    1. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. Skriv en URL med följande mönster i rutan **Identifierare:**`https://global.replicon.com/!/saml2/<client name>`

    1. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Replicon Client supportteam](https://www.replicon.com/customerzone/contact-support) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Klicka på redigerings-/pennikonen för **SAML-signeringscertifikat för** att redigera inställningarna.

    ![Signeringsalgoritm](common/signing-algorithm.png)

    1. Välj **Signera SAML-påstående** som **signeringsalternativ**.

    1. Välj **SHA-256** som **signeringsalgoritm**.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

   ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Konfigurera Replicon SSO

1. I ett annat webbläsarfönster loggar du in på webbplatsen För Replicon-företag som administratör.

2. Så här konfigurerar du SAML 2.0:

    ![Aktivera SAML-autentisering](./media/replicon-tutorial/ic777805.png "Aktivera SAML-autentisering")

    a. Om du vill visa dialogrutan **EnableSAML Authentication2** lägger du till följande i webbadressen efter företagsnyckeln:`/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Följande visar schemat för den fullständiga webbadressen:`https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Klicka **+** på om du vill expandera **avsnittet v20Konfigurering.**

   c. Klicka **+** på om du vill expandera avsnittet **metaDatakonfigurering.**

   d. Välj **SHA256** för xmlSignatureAlgorithm

   e. Klicka på **Välj fil**om du vill välja en XML-fil för identitetsprovidermetadata och klicka på **Skicka**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Replicon.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Replicon**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-replicon-test-user"></a>Skapa replicontestanvändare

Syftet med det här avsnittet är att skapa en användare som heter B.Simon i Replicon.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på webbplatsen Replicon som administratör i ett webbläsarfönster.

2. Gå till **Administrationsanvändare \> **.

    ![Användare](./media/replicon-tutorial/ic777806.png "Användare")

3. Klicka på **+Lägg till användare**.

    ![Lägg till användare](./media/replicon-tutorial/ic777807.png "Lägg till användare")

4. Gör följande i avsnittet **Användarprofil:**

    ![Användarprofil](./media/replicon-tutorial/ic777808.png "Användarprofil")

    a. Skriv **Azure** AD-e-postadressen för den Azure AD-användare som `B.Simon@contoso.com`du vill etablera som .

    > [!NOTE]
    > Inloggningsnamn måste matcha användarens e-postadress i Azure AD

    b. Som **autentiseringstyp**väljer du **SSO**.

    c. Ange autentiserings-ID till samma värde som inloggningsnamn (Azure AD-e-postadressen för användaren)

    d. Skriv användarens avdelning i textrutan **avdelning.**

    e. Som **medarbetartyp**väljer du **Administratör**.

    f. Klicka på **Spara användarprofil**.

> [!NOTE]
> Du kan använda andra verktyg för att skapa repliker eller API:er som tillhandahålls av Replicon för att etablera Azure AD-användarkonton.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Replicon på åtkomstpanelen bör du automatiskt loggas in på den Replicon som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)