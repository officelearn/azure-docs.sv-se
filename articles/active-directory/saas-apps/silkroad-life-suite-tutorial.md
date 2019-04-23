---
title: 'Självstudier: Azure Active Directory-integrering med SilkRoad liv Suite | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SilkRoad liv Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: bcad6232de7fa257b58fe6d84f2c2ff794b64589
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005425"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Självstudier: Azure Active Directory-integrering med SilkRoad liv Suite

I den här självstudien får du lära dig hur du integrerar SilkRoad liv Suite med Azure Active Directory (AD Azure).
Integrera SilkRoad liv Suite med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SilkRoad liv Suite.
* Du kan aktivera användarna att vara automatiskt inloggad till SilkRoad liv Suite (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med SilkRoad liv Suite, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* SilkRoad liv Suite enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för SilkRoad liv Suite **SP** -initierad SSO

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Att lägga till SilkRoad liv Suite från galleriet

Om du vill konfigurera integreringen av SilkRoad liv Suite till Azure AD, som du behöver lägga till SilkRoad liv Suite från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SilkRoad liv Suite från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **SilkRoad liv Suite**väljer **SilkRoad liv Suite** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![SilkRoad liv Suite i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SilkRoad liv Suite baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i SilkRoad liv Suite upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med SilkRoad liv Suite, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SilkRoad liv Suite enkel inloggning](#configure-silkroad-life-suite-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SilkRoad liv Suite testanvändare](#create-silkroad-life-suite-test-user)**  – du har en motsvarighet för Britta Simon i SilkRoad liv Suite som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med SilkRoad liv Suite:

1. I den [Azure-portalen](https://portal.azure.com/)på den **SilkRoad liv Suite** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    > [!NOTE]
    > Du får den **tjänstleverantör metadatafil** beskrivs senare i den här självstudien.

    a. Klicka på **Ladda upp metadatafil**.

    ![image](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![image](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp den **identifierare** och **svars-URL** värden får automatiskt ifylld i avsnittet grundläggande SAML-konfiguration:

    ![image](common/sp-identifier-reply.png)

    > [!Note]
    > Om värdena **Identifierare** och **Svars-URL** inte fylls i automatiskt fyller du i värdena manuellt enligt dina krav.

    d. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<subdomain>.silkroad-eng.com/Authentication/`

5. På den **SAML grundkonfiguration** om du inte har **tjänstleverantör metadatafil**, utför följande steg:

    ![SilkRoad liv Suite domän och URL: er med enkel inloggning för information](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. I rutan **Identifierare** skriver du en URL med följande mönster:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP`|
    | `https://<subdomain>.silkroad.com/Authentication/SP`|

    c. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/`|
    | `https://<subdomain>.silkroad.com/Authentication/`|

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [SilkRoad liv Suite klienten supportteamet](https://www.silkroad.com/locations/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. På den **konfigurera SilkRoad liv Suite** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-silkroad-life-suite-single-sign-on"></a>Konfigurera enkel inloggning för SilkRoad liv-Suite

1. Logga in på webbplatsen SilkRoad företag som administratör.

    > [!NOTE]
    > För att få åtkomst till programmet SilkRoad liv Suite autentisering för att konfigurera federation med Microsoft Azure AD kan du kontakta SilkRoad Support eller genom ditt ombud för SilkRoad tjänster.

1. Gå till **tjänstleverantör**, och klicka sedan på **Federation information**.

    ![Azure AD enkel inloggning](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Klicka på **hämta Federationsmetadata**, och spara sedan metadatafilen på datorn. Använda ned Federationsmetadata som en **tjänstleverantör metadatafil** i den **grundkonfiguration SAML** avsnitt i Azure-portalen.

    ![Azure AD enkel inloggning](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. I din **SilkRoad** programmet, klickar du på **autentisering källor**.

    ![Azure AD enkel inloggning](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Klicka på **lägga till autentiseringskälla**.

    ![Azure AD enkel inloggning](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. I den **lägga till autentiseringskälla** avsnittet, utför följande steg:

    ![Azure AD enkel inloggning](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. Under **alternativ 2 - metadatafil**, klickar du på **Bläddra** att ladda upp den hämtade metadatafilen från Azure-portalen.
  
    b. Klicka på **skapa identitetsprovider som använder fildata**.

1. I den **autentisering källor** klickar du på **redigera**.

    ![Azure AD enkel inloggning](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. På den **redigera autentiseringskälla** dialogrutan utför följande steg:

    ![Azure AD enkel inloggning](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. Som **aktiverad**väljer **Ja**.

    b. I den **EntityId** textrutan klistra in värdet för **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    c. I den **IdP beskrivning** textrutan anger du en beskrivning för din konfiguration (till exempel: *Azure AD SSO*).

    d. I den **metadatafil** textrutan ladda upp den **metadata** fil som du har hämtat från Azure-portalen.
  
    e. I den **IdP namn** textrutan anger du ett namn som är specifik för din konfiguration (till exempel: *Azure SP*).
  
    f. I den **Utloggning** textrutan klistra in värdet för **URL för utloggning** som du har kopierat från Azure-portalen.

    g. I den **inloggnings-tjänstens URL** textrutan klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.

    h. Klicka på **Spara**.

1. Inaktivera alla andra källor för autentisering.

    ![Azure AD enkel inloggning](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till SilkRoad liv Suite.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **SilkRoad liv Suite**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **SilkRoad liv Suite**.

    ![Länken SilkRoad liv Suite i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-silkroad-life-suite-test-user"></a>Skapa SilkRoad liv Suite testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i SilkRoad liv Suite. Arbeta med [SilkRoad liv Suite klienten supportteamet](https://www.silkroad.com/locations/) att lägga till användare i SilkRoad liv Suite-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen SilkRoad liv Suite i åtkomstpanelen, bör det vara loggas in automatiskt till SilkRoad liv Suite som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
