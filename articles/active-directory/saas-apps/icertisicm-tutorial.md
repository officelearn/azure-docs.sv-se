---
title: 'Självstudier: Azure Active Directory-integrering med Icertis plattform för hantering av kontrakt | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Icertis plattform för hantering av kontrakt.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6627e6dd-f559-4cd4-a509-f6d9a4961b49
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 15fac61856115ef5c4746c96b36e383b9a5b7b30
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708811"
---
# <a name="tutorial-azure-active-directory-integration-with-icertis-contract-management-platform"></a>Självstudier: Azure Active Directory-integrering med Icertis plattform för hantering av kontrakt

I den här självstudien får du lära dig hur du integrerar Icertis plattform för hantering av kontrakt med Azure Active Directory (AD Azure).
Integrera Icertis plattform för hantering av kontrakt med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Icertis plattform för hantering av kontrakt.
* Du kan aktivera användarna att vara automatiskt inloggad till Icertis kontrakt Hanteringsplattform (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Icertis plattform för hantering av kontrakt, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Icertis plattform för hantering av kontrakt enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Icertis plattform för hantering av kontrakt stöder **SP** -initierad SSO

## <a name="adding-icertis-contract-management-platform-from-the-gallery"></a>Att lägga till Icertis plattform för hantering av kontrakt från galleriet

För att konfigurera integrering av Icertis plattform för hantering av kontrakt i Azure AD, som du behöver lägga till Icertis plattform för hantering av kontrakt från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Icertis plattform för hantering av kontrakt från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Icertis plattform för hantering av kontrakt**väljer **Icertis plattform för hantering av kontrakt** resultatet panelen klickar **Lägg till** för att lägga till den programmet.

     ![Icertis kontrakt Hanteringsplattform i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Icertis kontrakt Hanteringsplattform baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Icertis plattform för hantering av kontrakt upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Icertis plattform för hantering av kontrakt, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Icertis kontrakt Management plattform för enkel inloggning](#configure-icertis-contract-management-platform-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Icertis plattform för hantering av kontrakt testanvändare](#create-icertis-contract-management-platform-test-user)**  – du har en motsvarighet för Britta Simon i Icertis kontrakt-plattform som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Om du vill konfigurera Azure AD enkel inloggning med Icertis plattform för hantering av kontrakt, utför du följande steg:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Icertis plattform för hantering av kontrakt** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Icertis kontrakt Hanteringsdomän plattform och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<company name>.icertis.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<company name>.icertis.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [Icertis kontrakt plattform Hanteringsklient supportteamet](https://www.icertis.com/company/contact/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **konfigurera Icertis plattform för hantering av kontrakt** avsnittet, kopiera den lämpliga URL: er enligt dina behov. För **inloggnings-URL**, använder du värdet med följande mönster: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ är klient-id för Azure AD-prenumeration.

    ![Kopiera konfigurations-URL:er](media/icertisicm-tutorial/configurls.png)

    a. Azure AD-identifierare

    b. Utloggnings-URL

### <a name="configure-icertis-contract-management-platform-single-sign-on"></a>Konfigurera Icertis kontrakt Management plattform enkel inloggning

Att konfigurera enkel inloggning på **Icertis plattform för hantering av kontrakt** sida, som du behöver skicka de hämtade **XML-Metadata för Federation** och lämpliga kopieras URL: er från Azure portal för att [ Icertis plattform för hantering av kontrakt supportteamet](https://www.icertis.com/company/contact/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Icertis plattform för hantering av kontrakt.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Icertis plattform för hantering av kontrakt**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Icertis plattform för hantering av kontrakt**.

    ![Länken Icertis plattform för hantering av kontrakt i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-icertis-contract-management-platform-test-user"></a>Skapa Icertis plattform för hantering av kontrakt testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Icertis plattform för hantering av kontrakt. Arbeta med [Icertis plattform för hantering av kontrakt supportteamet](https://www.icertis.com/company/contact/) att lägga till användare i Icertis plattform för hantering av kontrakt-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Icertis plattform för hantering av kontrakt på åtkomstpanelen, bör det vara loggas in automatiskt till Icertis kontrakt Hanteringsplattform som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)