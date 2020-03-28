---
title: 'Självstudiekurs: Azure Active Directory-integrering med Saba TalentSpace | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Saba TalentSpace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c13a649e1c1888a11e02b83d969255615cdc67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77561389"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Saba TalentSpace

I den här självstudien får du lära dig hur du integrerar Saba TalentSpace med Azure Active Directory (Azure AD). När du integrerar Saba TalentSpace med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Saba TalentSpace.
* Gör att användarna automatiskt loggas in på Saba TalentSpace med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Saba TalentSpace enkel inloggning (SSO) aktiverat prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Saba TalentSpace stöder **SP** initierade SSO
* När du har konfigurerat Saba TalentSpace kan du framtvinga sessionskontroll, som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-saba-talentspace-from-the-gallery"></a>Lägga till Saba TalentSpace från galleriet

Om du vill konfigurera integreringen av Saba TalentSpace i Azure AD måste du lägga till Saba TalentSpace från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Saba TalentSpace** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Saba TalentSpace** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-saba-talentspace"></a>Konfigurera och testa en enda Azure AD-inloggning för Saba TalentSpace

Konfigurera och testa Azure AD SSO med Saba TalentSpace med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Saba TalentSpace.

Så här konfigurerar och testar du Azure AD SSO med Saba TalentSpace:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Saba TalentSpace SSO](#configure-saba-talentspace-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Saba TalentSpace-testanvändare](#create-saba-talentspace-test-user)** – om du vill ha en motsvarighet till B.Simon i Saba TalentSpace som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)på sidan Integrering av **Saba TalentSpace-program** hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://global.hgncloud.com/[companyname]/saml/login`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://global.hgncloud.com/[companyname]/saml/metadata`

    c. I textrutan **Svarsadress (Kontroll konsumenttjänst URL)** skriver du en URL med följande mönster:`https://global.hgncloud.com/[companyname]/saml/SSO`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Saba TalentSpace Client supportteam](https://support.saba.com/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Saba TalentSpace** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Saba TalentSpace.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Saba TalentSpace**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-saba-talentspace-sso"></a>Konfigurera Saba TalentSpace SSO

1. I ett annat webbläsarfönster loggar du in på ditt **Saba TalentSpace-program** som administratör.

2. Klicka på fliken **Alternativ**.
  
    ![Vad är Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. I det vänstra navigeringsfönstret klickar du på **SAML-konfiguration**.
  
    ![Vad är Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. I dialogrutan **SAML-konfiguration** utför du följande steg:

    ![Vad är Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. För **Unik identifierare** väljer du **NameID**.

    b. För **Unique Identifier Maps To** (Unik identifierare mappar till) väljer du **Användarnamn**.
  
    c. Om du vill ladda upp din nedladdade metadatafil klickar du på **Bläddra** för att välja filen och sedan på **Ladda upp fil**.

    d. Testa konfigurationen genom att klicka på **Kör test**.

    > [!NOTE]
    > Du måste vänta på meddelandet "*SAML-testet är slutfört. Stäng det här fönstret*". Stäng sedan det öppnade webbläsarfönstret. Kryssrutan **Aktivera SAML** är bara tillgänglig om testet har slutförts.

    e. Välj **Aktivera SAML**.

    f. Klicka på **Spara ändringar**.

### <a name="create-saba-talentspace-test-user"></a>Skapa Saba TalentSpace-testanvändare

Syftet med detta avsnitt är att skapa en användare som heter Britta Simon i Saba TalentSpace.

**Så här skapar du en användare som heter Britta Simon i Saba TalentSpace:**

1. Logga in på ditt **Saba TalentSpace-program** som administratör.

2. Klicka på fliken **User Center** (Användarcenter) och sedan på **Skapa användare**.

    ![Vad är Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. I dialogrutan **Ny användare** utför du följande steg:

    ![Vad är Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. Skriv förnamn för användaren som **B**i textrutan **Förnamn** .

    b. Skriv efternamn för användaren som **Simon**i textrutan **Efternamn** .

    c. Skriv **B.Simon**i textrutan **Användarnamn** , användarnamnet som i Azure-portalen.

    d. Skriv ett lösenord för B.Simon i textrutan **Lösenord.**

    e. Klicka på **Spara**.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Saba TalentSpace i åtkomstpanelen bör du automatiskt loggas in på den Saba TalentSpace som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Saba TalentSpace med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)