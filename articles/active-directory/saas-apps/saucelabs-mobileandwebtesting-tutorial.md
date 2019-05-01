---
title: 'Självstudier: Azure Active Directory-integrering med sås Labs - Mobile- och Web testning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och sås Labs - Mobile- och Web testning.
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
ms.openlocfilehash: 41b35324ccca8cf40edbc53ed25a2d8615a9294e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64729342"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Självstudier: Azure Active Directory-integrering med sås Labs - Mobile- och webb-testning

I den här självstudien får du lära dig hur du integrerar sås Labs - Mobile- och Web testning med Azure Active Directory (AD Azure).
Integrera sås Labs - Mobile- och Web testning med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till sås Labs - Mobile- och Web testning.
* Du kan aktivera användarna att vara automatiskt inloggad till sås labb - Mobile- och Web testning (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med sås Labs - Mobile- och webb-testning, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Sås Labs - Mobile och Web testa enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Sås Labs - Mobile- och har stöd för Web testning **IDP** -initierad SSO
* Sås Labs - Mobile- och har stöd för Web testning **Just In Time** etableringen av användare

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Att lägga till sås Labs - Mobile- och Web testning från galleriet

Om du vill konfigurera integreringen av sås Labs - Mobile- och Web testning i Azure AD, som du behöver lägga till sås Labs - Mobile- och Web testning från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till sås Labs - Mobile- och Web testning från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **sås Labs - Mobile- och Web testning**väljer **sås Labs - Mobile- och Web testning** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Sås Labs - Mobile- och Web testning i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med sås Labs, Mobile och Web testning baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning till arbete, en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i sås Labs - måste Mobile- och Web testning upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med sås Labs - Mobile- och webb-testning, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera sås Labs - Mobile- och Web testa enkel inloggning](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa sås Labs - Mobile- och Web testning testanvändare](#create-sauce-labs---mobile-and-web-testing-test-user)**  – du har en motsvarighet för Britta Simon i sås Labs - Mobile- och Web testning som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Om du vill konfigurera Azure utför AD enkel inloggning med sås Labs - Mobile- och webb-testning, du följande steg:

1. I den [Azure-portalen](https://portal.azure.com/)på den **sås Labs - Mobile- och Web testning** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![Sås Labs - Mobile och Web testning domän och URL: er enkel inloggning för information](common/preintegrated.png)

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **konfigurera sås labb - Mobile- och Web testning** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>Konfigurera sås Labs - Mobile och webb testa enkel inloggning

1. I ett annat webbläsarfönster, loggar du in dina sås labb - Mobilappar och webbprogram testa företagets plats som administratör.

2. Klicka på den **Användarikon** och välj **Team Management** fliken.

    ![Konfigurera enkel inloggning](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. Ange din **domännamn** i textrutan.

    ![Konfigurera enkel inloggning](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. Klicka på **konfigurera** fliken.

    ![Konfigurera enkel inloggning](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. I den **Konfigurera enkel inloggning** avsnittet, utför följande steg.

    ![Konfigurera enkel inloggning](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Klicka på **Bläddra** ladda upp den hämtade metadata från Azure AD.

    b. Välj den **TILLÅT etablering av just** kryssrutan.

    c. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till sås Labs - Mobile- och Web testning.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **sås Labs - Mobile- och Web testning**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **sås Labs - Mobile- och Web testning**.

    ![Sås Labs - Mobile- och Web testning länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Skapa sås Labs - Mobile- och Web testning testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i sås Labs - Mobile- och Web testning. Sås Labs - Mobile och Web testning stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i sås Labs - Mobile- och webb-testning, skapas en ny efter autentisering.

> [!Note]
> Om du vill skapa en användare manuellt kan du kontakta [sås Labs - Mobile- och Web testning supportteam](mailto:support@saucelabs.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på sås Labs - Mobile- och Web testning panelen i åtkomstpanelen, bör det vara loggas in automatiskt till sås labb - Mobile- och Web testning som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

