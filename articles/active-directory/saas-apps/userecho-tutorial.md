---
title: 'Självstudiekurs: Azure Active Directory-integrering med UserEcho | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och UserEcho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 59d61eda7002fe46cf99fac63822b2333b2d64b5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67087770"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Självstudiekurs: Azure Active Directory-integrering med UserEcho

I den här självstudien får du lära dig hur du integrerar UserEcho med Azure Active Directory (Azure AD).
Genom att integrera UserEcho med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till UserEcho.
* Du kan aktivera dina användare så att de automatiskt loggas in på UserEcho (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med UserEcho behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* UserEcho enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* UserEcho stöder **SP** initierade SSO

## <a name="adding-userecho-from-the-gallery"></a>Lägga till UserEcho från galleriet

Om du vill konfigurera integreringen av UserEcho i Azure AD måste du lägga till UserEcho från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till UserEcho från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **UserEcho**i sökrutan och välj **UserEcho** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![UserEcho i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med UserEcho baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i UserEcho upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med UserEcho måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera UserEcho Single Sign-On](#configure-userecho-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa UserEcho-testanvändare](#create-userecho-test-user)** – om du vill ha en motsvarighet till Britta Simon i UserEcho som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med UserEcho:

1. Välj Enkel inloggning på sidan **UserEcho-programintegrering** på [Azure-portalen](https://portal.azure.com/). **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![UserEcho-domän och webbadresser med enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.userecho.com/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [UserEcho Client supportteam](https://feedback.userecho.com/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera UserEcho.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-userecho-single-sign-on"></a>Konfigurera UserEcho Enkel inloggning

1. I ett annat webbläsarfönster loggar du in på UserEcho-företagets webbplats som administratör.

2. Klicka på användarnamnet i verktygsfältet högst upp för att expandera menyn och klicka sedan på **Installationsprogrammet**.
   
    ![Konfigurera enkel inloggning](./media/userecho-tutorial/tutorial_userecho_06.png) 

3. Klicka på **Integrationer**.
   
    ![Konfigurera enkel inloggning](./media/userecho-tutorial/tutorial_userecho_07.png) 

4. Klicka på **Webbplats**och sedan **på Enkel inloggning (SAML2)**.
   
    ![Konfigurera enkel inloggning](./media/userecho-tutorial/tutorial_userecho_08.png) 

5. Gör följande på sidan **Saml (Single sign-on):**
   
    ![Konfigurera enkel inloggning](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. Som **SAML-aktiverad**väljer du **Ja**.
    
    b. Klistra in **inloggnings-URL**, som du har kopierat från Azure-portalen till **SMS-textrutan FÖR SAML SSO.**
    
    c. Klistra in **URL för utloggning**, som du har kopierat från Azure-portalen till **url-textrutan för fjärrinloggning.**
    
    d. Öppna det nedladdade certifikatet i Anteckningar, kopiera innehållet och klistra sedan in det i textrutan **X.509 Certificate.**
    
    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till UserEcho.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **UserEcho**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **UserEcho**i programlistan .

    ![Länken UserEcho i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-userecho-test-user"></a>Skapa UserEcho-testanvändare

Syftet med detta avsnitt är att skapa en användare som heter Britta Simon i UserEcho.

**Så här skapar du en användare som heter Britta Simon i UserEcho:**

1. Logga in på UserEcho-företagets webbplats som administratör.

2. Klicka på användarnamnet i verktygsfältet högst upp för att expandera menyn och klicka sedan på **Installationsprogrammet**.
   
    ![Konfigurera enkel inloggning](./media/userecho-tutorial/tutorial_userecho_06.png)

3. Klicka på **Användare**om du vill expandera avsnittet **Användare.**
   
    ![Konfigurera enkel inloggning](./media/userecho-tutorial/tutorial_userecho_10.png)

4. Klicka på **Användare**.
   
    ![Konfigurera enkel inloggning](./media/userecho-tutorial/tutorial_userecho_11.png)

5. Klicka på **Bjud in en ny användare**.
   
    ![Konfigurera enkel inloggning](./media/userecho-tutorial/tutorial_userecho_12.png)

6. Gör följande i dialogrutan **Bjud in en ny användare:**
   
    ![Konfigurera enkel inloggning](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. Skriv namnet på användaren som Britta Simon i textrutan **Namn.**
    
    b.  I textrutan **E-post** skriver du e-postadressen för användaren: Brittasimon@contoso.com.
    
    c. Klicka på **Bjud in**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen UserEcho på åtkomstpanelen ska du automatiskt loggas in på den UserEcho som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

