---
title: 'Självstudiekurs: Azure Active Directory-integrering med LiquidFiles | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LiquidFiles.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cb517134-0b34-4a74-b40c-5a3223ca81b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6514594d3119ebf8fab774c3e84c85e34bdfeaf4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67097930"
---
# <a name="tutorial-azure-active-directory-integration-with-liquidfiles"></a>Självstudiekurs: Azure Active Directory-integrering med LiquidFiles

I den här självstudien får du lära dig hur du integrerar LiquidFiles med Azure Active Directory (Azure AD).
Genom att integrera LiquidFiles med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till LiquidFiles.
* Du kan aktivera dina användare så att de automatiskt loggas in på LiquidFiles (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med LiquidFiles behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* LiquidFiles enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* LiquidFiles stöder **SP** initierade SSO

## <a name="adding-liquidfiles-from-the-gallery"></a>Lägga till LiquidFiles från galleriet

Om du vill konfigurera integreringen av LiquidFiles i Azure AD måste du lägga till LiquidFiles från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till LiquidFiles från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **LiquidFiles**i sökrutan och välj **LiquidFiles** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

    ![LiquidFiles i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med LiquidFiles baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i LiquidFiles upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med LiquidFiles måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera LiquidFiles Single Sign-On](#configure-liquidfiles-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa LiquidFiles testanvändare](#create-liquidfiles-test-user)** - om du vill ha en motsvarighet till Britta Simon i LiquidFiles som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med LiquidFiles:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **LiquidFiles-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![LiquidFiles Domän och WEBBADRESSER enkel inloggningsinformation](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<YOUR_SERVER_URL>/saml/init`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<YOUR_SERVER_URL>`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<YOUR_SERVER_URL>/saml/consume`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [LiquidFiles Client support team](https://www.liquidfiles.com/support.html) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. Kopiera **Tumavtryck** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera LiquidFiles.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-liquidfiles-single-sign-on"></a>Konfigurera LiquidFiles enkel inloggning

1. Logga in på webbplatsen LiquidFiles som administratör.

1. Klicka på **Enkel inloggning** i **konfigurationen för admin >** på menyn.

1. På sidan **Konfiguration för enkel inloggning** utför du följande steg

    ![Konfigurera enkel inloggning](./media/liquidfiles-tutorial/tutorial_single_01.png)

    a. Som **enkel inloggningsmetod**väljer du **SAML 2**.

    b. Klistra in värdet **för inloggnings-URL**i textrutan **för IDP-inloggningsadress** , som du har kopierat från Azure-portalen.

    c. Klistra in värdet **för URL-url för utloggning**i **IDP-utloggning,** som du har kopierat från Azure-portalen.

    d. Klistra in **tumavtrycksvärdet** som du har kopierat från Azure portal i **textrutan IDP Cert Fingerprint.**

    e. Skriv värdet `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`i textrutan Namnidentifieringsformat .

    f. Skriv värdet `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport`i textrutan Authn Context .

    g. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LiquidFiles.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **LiquidFiles**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **LiquidFiles**i programlistan .

    ![Länken LiquidFiles i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-liquidfiles-test-user"></a>Skapa LiquidFiles testanvändare

Syftet med detta avsnitt är att skapa en användare som heter Britta Simon i LiquidFiles. Arbeta med din LiquidFiles serveradministratör för att få dig själv tillagd som användare innan du loggar in på ditt LiquidFiles-program.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen LiquidFiles på åtkomstpanelen ska du automatiskt loggas in på de LiquidFiles som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

