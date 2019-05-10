---
title: 'Självstudier: Azure Active Directory-integrering med SurveyMonkey Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SurveyMonkey Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a2d9b57-27b5-4d9f-b52c-09b692872cf7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f354d0413d023e7af6d1719d86e0fbff59cf2b2
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441378"
---
# <a name="tutorial-azure-active-directory-integration-with-surveymonkey-enterprise"></a>Självstudier: Azure Active Directory-integrering med SurveyMonkey Enterprise

Lär dig hur du integrerar SurveyMonkey Enterprise med Azure Active Directory (AD Azure) i den här självstudien.
Integrera SurveyMonkey Enterprise med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SurveyMonkey Enterprise.
* Du kan aktivera användarna att vara automatiskt inloggad till SurveyMonkey Enterprise (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med SurveyMonkey Enterprise, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* SurveyMonkey Enterprise enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för SurveyMonkey Enterprise **SP och IDP** -initierad SSO

## <a name="adding-surveymonkey-enterprise-from-the-gallery"></a>Att lägga till SurveyMonkey Enterprise från galleriet

Om du vill konfigurera integreringen av SurveyMonkey Enterprise till Azure AD, som du behöver lägga till SurveyMonkey Enterprise från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SurveyMonkey Enterprise från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Lägg till det nya programmet genom att klicka på den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **SurveyMonkey Enterprise**väljer **SurveyMonkey Enterprise** resultatet panelen och klicka sedan på den **Lägg till** för att lägga till programmet.

    ![SurveyMonkey Enterprise i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SurveyMonkey Enterprise baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SurveyMonkey Enterprise upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med SurveyMonkey Enterprise, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för SurveyMonkey-Enterprise](#configure-surveymonkey-enterprise-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa SurveyMonkey Enterprise testanvändare](#create-surveymonkey-enterprise-test-user)**  – du har en motsvarighet för Britta Simon i SurveyMonkey-företag som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med SurveyMonkey Enterprise:

1. I den [Azure-portalen](https://portal.azure.com/)på den **SurveyMonkey Enterprise** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen **Redigeringa** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På den **SAML grundkonfiguration** avsnittet programmet är förkonfigurerad och nödvändiga URL: er är redan ifyllda på förhand med Azure. Användaren behöver för att spara konfigurationen genom att klicka på den **spara** knappen.

    ![SurveyMonkey Enterprise domän och URL: er med enkel inloggning för information](common/preintegrated.png)

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![SurveyMonkey Enterprise domän och URL: er med enkel inloggning för information](common/both-signonurl.png)

    I rutan **Inloggnings-URL** anger du en URL: ` https://www.surveymonkey.com/user/sign-in/sso/?ut_source=megamenu`

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

7. På den **konfigurera SurveyMonkey Enterprise** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-surveymonkey-enterprise-single-sign-on"></a>Konfigurera SurveyMonkey Enterprise enkel inloggning

Att konfigurera enkel inloggning på **SurveyMonkey Enterprise** sida, som du behöver skicka de hämtade **XML-Metadata för Federation** och lämpliga kopierade URL: er från Azure portal för att [SurveyMonkey Enterprise support-teamet](mailto:support@selerix.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SurveyMonkey Enterprise.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**, och välj sedan **SurveyMonkey Enterprise**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **SurveyMonkey Enterprise**.

    ![Länken SurveyMonkey Enterprise i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan och klicka sedan på den **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-surveymonkey-enterprise-test-user"></a>Skapa SurveyMonkey Enterprise testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i SurveyMonkey-företag. Arbeta med [SurveyMonkey Enterprise support-teamet](https://help.surveymonkey.com/) att lägga till användare i SurveyMonkey Enterprise-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen SurveyMonkey Enterprise i åtkomstpanelen, bör det vara loggas in automatiskt SurveyMonkey Enterprise som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
