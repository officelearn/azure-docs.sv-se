---
title: 'Självstudiekurs: Azure Active Directory-integrering med SmartRecruiters | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SmartRecruiters.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e96aeecd-e113-454e-89c3-58c9f44cfd4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: f20a8a63160a498b9789f77eededad1fd6a02906
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090110"
---
# <a name="tutorial-azure-active-directory-integration-with-smartrecruiters"></a>Självstudiekurs: Azure Active Directory-integrering med SmartRecruiters

I den här självstudien får du lära dig hur du integrerar SmartRecruiters med Azure Active Directory (Azure AD).
Genom att integrera SmartRecruiters med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SmartRecruiters.
* Du kan aktivera dina användare så att de automatiskt loggas in på SmartRecruiters (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med SmartRecruiters behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SmartRecruiters enda inloggningsaktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SmartRecruiters stöder **SP och IDP** initierade SSO

## <a name="adding-smartrecruiters-from-the-gallery"></a>Lägga till SmartRecruiters från galleriet

Om du vill konfigurera integreringen av SmartRecruiters i Azure AD måste du lägga till SmartRecruiters från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till SmartRecruiters från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **SmartRecruiters**i sökrutan och välj **SmartRecruiters** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

     ![SmartRecruiters i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du en enda Azure AD-inloggning med SmartRecruiters baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SmartRecruiters upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med SmartRecruiters måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SmartRecruiters Single Sign-On](#configure-smartrecruiters-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SmartRecruiters-testanvändare](#create-smartrecruiters-test-user)** – om du vill ha en motsvarighet till Britta Simon i SmartRecruiters som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med SmartRecruiters:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **SmartRecruiters-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![SmartRecruiters Domän och WEBBADRESSER enkel inloggningsinformation](common/idp-intiated.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://www.smartrecruiters.com/web-sso/saml/<companyname>`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://www.smartrecruiters.com/web-sso/saml/<companyname>/callback`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![SmartRecruiters Domän och WEBBADRESSER enkel inloggningsinformation](common/metadata-upload-additional-signon.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://www.smartrecruiters.com/web-sso/saml/<companyname>/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [SmartRecruiters Client support team](https://www.smartrecruiters.com/about-us/contact-us/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera SmartRecruiters.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-smartrecruiters-single-sign-on"></a>Konfigurera SmartRecruiters Enkel inloggning

1. I ett annat webbläsarfönster loggar du in på smartrecruiters-företagets webbplats som administratör.

1. Gå till **Inställningar / Admin**.

    ![SmartRecruiters-konfiguration](./media/smartrecruiters-tutorial/configure.png)

1. Klicka på **Webb-SSO**i avsnittet **Konfiguration** .

    ![SmartRecruiters-konfiguration](./media/smartrecruiters-tutorial/configure1.png)

1. Växla **Aktivera webb-SSO**.

    ![SmartRecruiters-konfiguration](./media/smartrecruiters-tutorial/configure2.png)

1. I Konfiguration av **identitetsprovider**utför du följande steg:

    ![SmartRecruiters-konfiguration](./media/smartrecruiters-tutorial/configure4.png)

    a. Klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen i **URL-textrutan** för identitetsprovider.

    b. Öppna **certifikat(Base64)** som du har hämtat från Azure-portalen i Anteckningar, kopiera innehållet i den och klistra in i **textrutan för identitetsprovidercertifikat.**

1. Klicka på **Spara webb-SSO-konfiguration**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till SmartRecruiters.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **SmartRecruiters**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **SmartRecruiters**i programlistan .

    ![Länken SmartRecruiters i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-smartrecruiters-test-user"></a>Skapa SmartRecruiters-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i SmartRecruiters. Arbeta med [SmartRecruiters supportteam](https://www.smartrecruiters.com/about-us/contact-us/) för att lägga till användarna i SmartRecruiters-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SmartRecruiters på åtkomstpanelen bör du automatiskt loggas in på de SmartRecruiters som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

