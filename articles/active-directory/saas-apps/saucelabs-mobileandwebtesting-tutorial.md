---
title: 'Självstudiekurs: Azure Active Directory-integrering med Sauce Labs – Mobil- och webbtestning | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Sauce Labs - Mobile och Web Testing.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 8933cb90672e49305cd0fb7dc5e4f8f04f94093e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091561"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Självstudiekurs: Azure Active Directory-integrering med Sauce Labs – Mobil- och webbtestning

I den här självstudien får du lära dig hur du integrerar Sauce Labs - Mobile och Web Testing med Azure Active Directory (Azure AD).
Integrera Sauce Labs - Mobil- och webbtestning med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Sauce Labs - Mobile och Web Testing.
* Du kan aktivera dina användare automatiskt inloggade på Sauce Labs - Mobile and Web Testing (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Sauce Labs – Mobil- och webbtestning behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Sauce Labs - Mobil- och webbtestning enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Sauce Labs - Mobil- och webbtestning stöder **IDP-initierad** SSO
* Sauce Labs - Mobil- och webbtestning stöder **just in time-användares** etablering

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Lägga Sauce Labs - Mobil och webbtestning från galleriet

Om du vill konfigurera integreringen av Sauce Labs – Mobile och Web Testing i Azure AD måste du lägga till Sauce Labs – Mobile och Web Testing från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Sauce Labs – Mobile and Web Testing från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Sauce Labs - Mobile and Web Testing**, väljer Sauce **Labs - Mobile och Web Testing** från resultatpanelen och klickar sedan på **Lägg** till knappen för att lägga till programmet.

    ![Sauce Labs - Mobil- och webbtestning i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med Sauce Labs - Mobile och Web Testing baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Sauce Labs - Mobile och Web Testing upprättas.

Om du vill konfigurera och testa en enda Azure AD-inloggning med Sauce Labs – Mobil- och webbtestning måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Sauce Labs - Mobile and Web Testing Single Sign-On](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Sauce Labs - Mobile and Web Testing testanvändare](#create-sauce-labs---mobile-and-web-testing-test-user)** - att ha en motsvarighet till Britta Simon i Sauce Labs - Mobil- och webbtestning som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enda Azure AD-inloggning med Sauce Labs – Mobile och Web Testing:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Sauce Labs - Mobile och Web** **Testing-programintegration**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![Sauce Labs - Mobil- och webbtestdomän och webbadresser med enkel inloggning](common/preintegrated.png)

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera såslabbar - mobil- och webbtestning.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>Konfigurera Sauce Labs - Mobil- och webbtestning enkel inloggning

1. I ett annat webbläsarfönster loggar du in på webbplatsen Sauce Labs – Mobile and Web Testing som administratör.

2. Klicka på **ikonen Användare** och välj fliken **Teamhantering.**

    ![Konfigurera enkel inloggning](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. Ange ditt **domännamn** i textrutan.

    ![Konfigurera enkel inloggning](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. Klicka på **Fliken Konfigurera.**

    ![Konfigurera enkel inloggning](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. I avsnittet **Konfigurera enkel inloggning** utför du följande steg.

    ![Konfigurera enkel inloggning](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Klicka på **Bläddra** och ladda upp den nedladdade metadatafilen från Azure AD.

    b. Markera kryssrutan **TILLÅT ETABLERING I TID.**

    c. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du `brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Sauce Labs - Mobile och Web Testing.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Sauce Labs - Mobile och Web Testing**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Sauce Labs - Mobile och Web Testing**.

    ![Såslabbet - mobil- och webbtestningslänken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Skapa Sås Labs - Mobil- och webbtestningstestanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Sauce Labs - Mobile och Web Testing. Sauce Labs - Mobile och Web Testing stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Sauce Labs - Mobile och Web Testing skapas en ny efter autentisering.

> [!Note]
> Om du behöver skapa en användare manuellt kontaktar du [supportteamet för Sauce Labs - Mobile och Web Testing](mailto:support@saucelabs.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Sauce Labs - Mobile och Web Testing i åtkomstpanelen bör du automatiskt loggas in på de såslaboratorjor - Mobil- och webbtester som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

