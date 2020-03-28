---
title: 'Självstudiekurs: Azure Active Directory-integrering med GitHub | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e812e1b03637a3ecd7a45f02664c4e3547f1aef1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79138985"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med GitHub

I den här självstudien får du lära dig hur du integrerar GitHub med Azure Active Directory (Azure AD). När du integrerar GitHub med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till din GitHub Enterprise Cloud Organization.
* Hantera åtkomst till din GitHub Enterprise Cloud Organization på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med GitHub behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En GitHub-organisation som har skapats i [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) och kräver [GitHub Enterprise-faktureringsplanen](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* GitHub stöder **IDP**-initierad enkel inloggning

* GitHub stöder [ **automatisk** användaretablering (organisationsanbjudningar)](github-provisioning-tutorial.md)
* När du har konfigurerat GitHub kan du framtvinga sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-github-from-the-gallery"></a>Lägga till GitHub från galleriet

För att konfigurera integreringen av GitHub till Azure AD behöver du lägga till GitHub från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **GitHub** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **GitHub** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-github"></a>Konfigurera och testa en azure AD-inloggning för GitHub

Konfigurera och testa Azure AD SSO med GitHub med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i GitHub.

Så här konfigurerar och testar du Azure AD SSO med GitHub:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera GitHub SSO](#configure-github-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa GitHub-testanvändare](#create-github-test-user)** – om du vill ha en motsvarighet till B.Simon i GitHub som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **GitHub** Hantera på sidan Hantera på sidan **Manage** [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

   a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://github.com/orgs/<entity-id>/sso`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Observera att detta inte är de verkliga värdena. Du behöver uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Här föreslår vi att du använder det unika värdet för strängen i identifieraren. Gå till GitHub-administrationsavsnittet för att hämta dessa värden.

5. GitHub-programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. GitHub-programmet förväntar sig att **nameidentifier** mappas med **user.mail**, så du behöver redigera attributmappningen genom att klicka på ikonen **Redigera** och ändra attributmappningen.

    ![image](common/edit-attribute.png)

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera GitHub** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till GitHub.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **GitHub**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-github-sso"></a>Konfigurera GitHub SSO

1. I ett annat webbläsarfönster loggar du in på din GitHub-organisationsplats som administratör.

2. Gå till **Inställningar** och klicka på **Säkerhet**

    ![Inställningar](./media/github-tutorial/tutorial_github_config_github_03.png)

3. Markera rutan **Aktivera SAML-autentisering**. Då visas konfigurationsfälten för enkel inloggning. Använd sedan URL-värdet för enkel inloggning för att uppdatera URL:en för enkel inloggning i Azure AD-konfiguration.

    ![Inställningar](./media/github-tutorial/tutorial_github_config_github_13.png)

4. Konfigurera följande fält:

    ![Inställningar](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. I textrutan **Inloggnings-URL** klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    b. I textrutan **Issuer** (Utfärdare) klistrar du in värdet för den **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    c. Öppna det nedladdade certifikatet från Azure-portalen i Anteckningar och klistra in innehållet i textrutan **Offentligt certifikat**.

    d. Klicka på ikonen **Redigera** för att redigera **Signature Method** (Signaturmetod) och **Digest Method** (Hashmetod) från **RSA-SHA1** och **SHA1** till **RSA-SHA256** och **SHA256** enligt nedan.
    
    e. Uppdatera **url:en för att skydda konsumenttjänsten (Svars-URL: en)** från standard-URL:en så att url:en i Github matchar URL:en i Azure-appregistreringen.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Klicka på **Testa SAML-konfiguration** för att bekräfta att inga verifieringsfel eller andra fel inträffade under enkel inloggning.

    ![Inställningar](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Klicka på **Spara**

> [!NOTE]
> Enkel inloggning i GitHub autentiserar till en specifik organisation i GitHub och ersätter inte autentisering av själva GitHub. Om användarens github.com-session har upphört att gälla kan det därför hända att du blir ombedd att autentisera med ID/lösenord för GitHub under processen för enkel inloggning.

### <a name="create-github-test-user"></a>Skapa GitHub-testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i GitHub. GitHub stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](github-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på din GitHub-företagsplats som administratör.

2. Klicka på **Personer**.

    ![Människor](./media/github-tutorial/tutorial_github_config_github_08.png "People")

3. Klicka på **Invite member** (Bjud in medlem).

    ![Bjud in användare](./media/github-tutorial/tutorial_github_config_github_09.png "Bjud in användare")

4. På dialogrutan **Invite member** (Bjud in medlem) utför du följande steg:

    a. I textrutan **E-post** anger du e-postadressen för Britta Simon-kontot.

    ![Bjud in personer](./media/github-tutorial/tutorial_github_config_github_10.png "Bjud in personer")

    b. Klicka på **Skicka inbjudan**.

    ![Bjud in personer](./media/github-tutorial/tutorial_github_config_github_11.png "Bjud in personer")

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på GitHub-panelen i åtkomstpanelen bör du automatiskt loggas in på GitHub som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova GitHub med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
