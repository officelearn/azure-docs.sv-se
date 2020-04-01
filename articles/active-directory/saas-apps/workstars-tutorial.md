---
title: 'Självstudiekurs: Azure Active Directory-integrering med Workstars | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workstars.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 51a4a4e4-ff60-4971-b3f8-a0367b70d220
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: c13c6ab180a172c034d25ac84781f5d3f83ae186
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086641"
---
# <a name="tutorial-azure-active-directory-integration-with-workstars"></a>Självstudiekurs: Azure Active Directory-integrering med Workstars

I den här självstudien får du lära dig hur du integrerar Workstars med Azure Active Directory (Azure AD).
Genom att integrera Workstars med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Workstars.
* Du kan aktivera dina användare automatiskt inloggade på Workstars (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Workstars behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Workstars enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Workstars stöder **IDP** initierad SSO

## <a name="adding-workstars-from-the-gallery"></a>Lägga till Workstars från galleriet

Om du vill konfigurera integreringen av Workstars i Azure AD måste du lägga till Workstars från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Workstars från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Workstars**i sökrutan och välj **Workstars** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![Workstars i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Workstars baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Workstars upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Workstars måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Workstars Single Sign-On](#configure-workstars-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Workstars testanvändare](#create-workstars-test-user)** - att ha en motsvarighet till Britta Simon i Workstars som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Workstars:

1. Välj [Azure portal](https://portal.azure.com/)Enkel inloggning på sidan **Workstars-programintegration på Sidan För Arbetsstjärnors** program. **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Workstars Domän och webbadresser enkel inloggningsinformation](common/idp-intiated.png)

    a. Skriv en URL i textrutan **Identifierare:**`https://workstars.com`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<subdomain>.workstars.com/saml/login_check`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [Workstars Client supportteam](https://support.workstars.com/) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Workstars.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-workstars-single-sign-on"></a>Konfigurera Workstars enkel inloggning

1. I ett annat webbläsarfönster loggar du in på workstars-företagets webbplats som administratör.

2. Klicka på **Inställningar**i huvudverktygsfältet .

    ![Inställningar för Workstars](./media/workstars-tutorial/tutorial_workstars_sett.png)

3. Gå till **Logga in** > **inställningar**.

    ![Workstars signon](./media/workstars-tutorial/tutorial_workstars_signon.png)

    ![Inställningar för Workstars](./media/workstars-tutorial/tutorial_workstars_settings.png)

4. På sidan **Saml (Single Sign On) – Inställningar** gör du följande:
    
    ![Workstars saml](./media/workstars-tutorial/tutorial_workstars_saml.png)

    a. Skriv **Office 365**i textrutan **Identitetsprovidernamn** .

    b. Klistra in värdet för **Azure AD-identifierare**i textrutan **identitetsproviderns entitets-ID** som du har kopierat från Azure-portalen.

    c. Kopiera innehållet i den nedladdade certifikatfilen i anteckningar och klistra sedan in den i textrutan **x509 Certifikat.** 

    d. I textrutan **SAML SSO URL** klistrar du in det värde för **Inloggnings-URL** som du har kopierat från Azure-portalen.
    
    e. Klistra in värdet **för URL-url för** **fjärrinloggning** som du har kopierat från Azure-portalen. 

    f. välj **Namn-ID** som **e-post (standard)**.

    g. Klicka på **Bekräfta**.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Workstars.

1. I Azure-portalen väljer du **Företagsprogram**, väljer **Alla program**och väljer sedan **Workstars**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Workstars**i programlistan .

    ![Länken Workstars i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-workstars-test-user"></a>Skapa Workstars testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Workstars. Arbeta med Supportteamet för [Workstars](https://support.workstars.com) för att lägga till användarna på Workstars-plattformen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Workstars på åtkomstpanelen ska du automatiskt loggas in på de Workstars som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

