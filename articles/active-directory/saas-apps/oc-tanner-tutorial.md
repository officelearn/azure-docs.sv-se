---
title: 'Självstudier: Azure Active Directory-integrering med O.C. Turesson - AppreciateHub | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och O.C. Tanner - AppreciateHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: debda9784470f6d32894a7f9ddf55fc848ff6d10
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58850920"
---
# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>Självstudier: Azure Active Directory-integrering med O.C. Tanner - AppreciateHub

I den här självstudien får du lära dig hur du integrerar O.C. Turesson - AppreciateHub med Azure Active Directory (AD Azure).
Integrera O.C. Turesson - AppreciateHub med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till O.C. Tanner - AppreciateHub.
* Du kan aktivera användarna att vara automatiskt inloggad till O.C. Turesson - AppreciateHub (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Konfigurera Azure AD-integrering med O.C. Turesson - AppreciateHub, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* O.C. Turesson - AppreciateHub enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* O.C. Har stöd för Turesson - AppreciateHub **IDP** -initierad SSO

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Att lägga till O.C. Turesson - AppreciateHub från galleriet

Konfigurera integreringen av O.C. Turesson - AppreciateHub till Azure AD, som du behöver lägga till O.C. Turesson - AppreciateHub från galleriet i din lista över hanterade SaaS-appar.

**Att lägga till O.C. Turesson - AppreciateHub från galleriet, utför följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **O.C. Turesson - AppreciateHub**väljer **O.C. Turesson - AppreciateHub** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![O.C. Turesson - AppreciateHub i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med O.C. Turesson - AppreciateHub baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning att fungera, en länk förhållandet mellan en Azure AD-användare och relaterade användaren i O.C. Turesson - AppreciateHub måste upprättas.

Konfigurera och testa Azure AD enkel inloggning med O.C. Turesson - AppreciateHub, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera O.C. Turesson - AppreciateHub Single Sign-On](#configure-oc-tanner---appreciatehub-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa O.C. Turesson - AppreciateHub testanvändare](#create-oc-tanner---appreciatehub-test-user)**  – du har en motsvarighet för Britta Simon i O.C. Turesson - AppreciateHub som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera Azure AD enkel inloggning med O.C. Turesson - AppreciateHub, utför följande steg:

1. I den [Azure-portalen](https://portal.azure.com/)på den **O.C. Turesson - AppreciateHub** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    >[!NOTE]
    >Du kan ladda ned den **tjänstleverantör metadatafil** från [här](https://fed.appreciatehub.com/fed/sp/metadata)

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har överförts den **identifierare** och **svars-URL** värden får automatiskt ifylld i avsnittet grundläggande SAML-konfiguration.

     ![O.C. Turesson - AppreciateHub domän och URL: er enkel inloggning för information](common/idp-intiated.png)

    > [!Note]
    > Om den **identifierare** och **svars-URL** värden inte få automatisk polulated, och Fyll i värden manuellt enligt dina behov. Kontakta [O.C. Turesson - AppreciateHub klienten supportteamet](mailto:sso@octanner.com) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **konfigurera O.C. Turesson - AppreciateHub** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-oc-tanner---appreciatehub-single-sign-on"></a>Konfigurera O.C. Turesson - AppreciateHub Single Sign-On

Att konfigurera enkel inloggning på **O.C. Turesson - AppreciateHub** sida, som du behöver skicka de hämtade **XML-Metadata för Federation** och lämpliga kopierade URL: er från Azure portal för att [O.C. Turesson - AppreciateHub supportteamet](mailto:sso@octanner.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fältet Typ av användare som BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till O.C. Tanner - AppreciateHub.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **O.C. Turesson - AppreciateHub**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **O.C. Turesson - AppreciateHub**.

    ![O.C. Turesson - AppreciateHub länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-oc-tanner---appreciatehub-test-user"></a>Skapa O.C. Tanner - AppreciateHub test user

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i O.C. Tanner - AppreciateHub.

**Skapa en användare som kallas Britta Simon i O.C. Turesson - AppreciateHub, utför följande steg:**

Be din [O.C. Turesson - AppreciateHub supportteamet](mailto:sso@octanner.com) att skapa en användare som har du samma värde som användarnamn för Britta Simon i Azure AD som nameID-attribut.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på O.C. Turesson - AppreciateHub panel i åtkomstpanelen, det bör vara loggas in automatiskt till O.C. Turesson - AppreciateHub som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
