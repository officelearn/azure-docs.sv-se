---
title: 'Självstudier: Azure Active Directory-integrering med LiquidFiles | Microsoft Docs'
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
ms.openlocfilehash: 5352040dbbe33569dfdb4e987d8bd84435702230
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917510"
---
# <a name="tutorial-azure-active-directory-integration-with-liquidfiles"></a>Självstudier: Azure Active Directory-integrering med LiquidFiles

I den här självstudien får du lära dig hur du integrerar LiquidFiles med Azure Active Directory (AD Azure).
Integrera LiquidFiles med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till LiquidFiles.
* Du kan aktivera användarna att vara automatiskt inloggad till LiquidFiles (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med LiquidFiles, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* LiquidFiles enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för LiquidFiles **SP** -initierad SSO

## <a name="adding-liquidfiles-from-the-gallery"></a>Att lägga till LiquidFiles från galleriet

För att konfigurera integrering av LiquidFiles i Azure AD, som du behöver lägga till LiquidFiles från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till LiquidFiles från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **LiquidFiles**väljer **LiquidFiles** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![LiquidFiles i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med LiquidFiles baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i LiquidFiles upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med LiquidFiles, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera LiquidFiles Single Sign-On](#configure-liquidfiles-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare LiquidFiles](#create-liquidfiles-test-user)**  – du har en motsvarighet för Britta Simon i LiquidFiles som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med LiquidFiles:

1. I den [Azure-portalen](https://portal.azure.com/)på den **LiquidFiles** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![LiquidFiles domän och URL: er med enkel inloggning för information](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<YOUR_SERVER_URL>/saml/init`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<YOUR_SERVER_URL>`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<YOUR_SERVER_URL>/saml/consume`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [LiquidFiles klienten supportteamet](https://www.liquidfiles.com/support.html) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. Kopiera **Tumavtryck** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera värdet för Tumavtryck](common/copy-thumbprint.png)

7. På den **konfigurera LiquidFiles** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-liquidfiles-single-sign-on"></a>Konfigurera LiquidFiles Single Sign-On

1. Inloggning till webbplatsen LiquidFiles företag som administratör.

1. Klicka på **enkel inloggning** i den **Admin > Configuration** på menyn.

1. På den **konfigurationen för enkel inloggning** utför följande steg

    ![Konfigurera enkel inloggning](./media/liquidfiles-tutorial/tutorial_single_01.png)

    a. Som **enkel inloggning på metoden**väljer **SAML 2**.

    b. I den **inloggnings-URL för IDP: N** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    c. I den **utloggnings-URL för IDP: N** textrutan klistra in värdet för **URL för utloggning**, som du har kopierat från Azure-portalen.

    d. I den **IDP Cert fingeravtryck** textrutan klistra in den **TUMAVTRYCK** värde som du har kopierat från Azure-portalen...

    e. Skriv ett värde i textrutan Format för namn på identifierare `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Skriv ett värde i textrutan Authn kontext `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport`.

    g. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LiquidFiles.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **LiquidFiles**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **LiquidFiles**.

    ![Länken LiquidFiles i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-liquidfiles-test-user"></a>Skapa LiquidFiles testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i LiquidFiles. Arbeta med serveradministratören LiquidFiles att hämta till dig själv som en användare innan du loggar in till ditt LiquidFiles program.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen LiquidFiles i åtkomstpanelen, bör det vara loggas in automatiskt till LiquidFiles som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

