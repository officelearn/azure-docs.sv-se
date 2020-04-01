---
title: 'Självstudiekurs: Azure Active Directory-integrering med SilkRoad Life Suite | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SilkRoad Life Suite.
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
ms.openlocfilehash: 63165da69815c77afb8692e1e68c1710beb8df8c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090826"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Självstudiekurs: Azure Active Directory-integrering med SilkRoad Life Suite

I den här självstudien får du lära dig hur du integrerar SilkRoad Life Suite med Azure Active Directory (Azure AD).
Genom att integrera SilkRoad Life Suite med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har tillgång till SilkRoad Life Suite.
* Du kan aktivera dina användare så att de automatiskt loggas in på SilkRoad Life Suite (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med SilkRoad Life Suite behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* SilkRoad Life Suite enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SilkRoad Life Suite stöder **SP** initierade SSO

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Lägga SilkRoad Life Suite från galleriet

Om du vill konfigurera integreringen av SilkRoad Life Suite i Azure AD måste du lägga till SilkRoad Life Suite från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till SilkRoad Life Suite från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **SilkRoad Life Suite**, väljer **SilkRoad Life Suite** från resultatpanelen och klickar sedan på **Lägg** till knappen för att lägga till programmet.

    ![SilkRoad Life Suite i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med SilkRoad Life Suite baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning till jobbet måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SilkRoad Life Suite upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med SilkRoad Life Suite måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SilkRoad Life Suite Enkel inloggning](#configure-silkroad-life-suite-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SilkRoad Life Suite-testanvändare](#create-silkroad-life-suite-test-user)** – om du vill ha en motsvarighet till Britta Simon i SilkRoad Life Suite som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med SilkRoad Life Suite:

1. Välj Enkel inloggning på sidan SilkRoad Life Suite-programintegrering **på** [Sidan](https://portal.azure.com/) **För Programmet SilkRoad Life Suite** .

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    > [!NOTE]
    > Du kommer att få **Service Provider metadatafilen** förklaras senare i den här självstudien.

    a. Klicka på **Ladda upp metadatafil**.

    ![image](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![image](common/browse-upload-metadata.png)

    c. När metadatafilen har överförts fylls **URL-värdena identifierare** och **svar** i i fyllas i automatiskt i avsnittet Grundläggande SAML-konfiguration:

    ![image](common/sp-identifier-reply.png)

    > [!Note]
    > Om värdena **Identifierare** och **Svars-URL** inte fylls i automatiskt fyller du i värdena manuellt enligt dina krav.

    d. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<subdomain>.silkroad-eng.com/Authentication/`

5. Om du inte har **metadatafilen för Service Provider**i avsnittet Grundläggande **SAML-konfiguration** gör du följande:

    ![SilkRoad Life Suite Domän och webbadresser enkel inloggningsinformation](common/sp-identifier-reply.png)

    a. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<subdomain>.silkroad-eng.com/Authentication/`

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
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [SilkRoad Life Suite Client supportteam](https://www.silkroad.com/locations/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera SilkRoad Life Suite.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-silkroad-life-suite-single-sign-on"></a>Konfigurera SilkRoad Life Suite Enkel inloggning

1. Logga in på din SilkRoad-företagswebbplats som administratör.

    > [!NOTE]
    > Om du vill få åtkomst till SilkRoad Life Suite Authentication-programmet för att konfigurera federation med Microsoft Azure AD kontaktar du SilkRoad Support eller din SilkRoad Services-representant.

1. Gå till **Tjänsteleverantör**och klicka sedan på **Federationsinformation**.

    ![Enkel azure AD-inloggning](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Klicka på **Hämta federationsmetadata**och spara sedan metadatafilen på datorn. Använd hämtade federationsmetadata som en **tjänstprovidermetadatafil** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    ![Enkel azure AD-inloggning](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. Klicka på **Autentiseringskällor**i programmet **SilkRoad** .

    ![Enkel azure AD-inloggning](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Klicka på **Lägg till autentiseringskälla**.

    ![Enkel azure AD-inloggning](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. Gör följande i avsnittet **Lägg till autentiseringskälla:**

    ![Enkel azure AD-inloggning](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. Under **Alternativ 2 - Metadatafil**klickar du på **Bläddra** för att ladda upp den nedladdade metadatafilen från Azure-portalen.
  
    b. Klicka på **Skapa identitetsprovider med hjälp av Fildata**.

1. Klicka på **Redigera**i avsnittet **Autentiseringskällor** .

    ![Enkel azure AD-inloggning](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. Gör följande i dialogrutan **Redigera autentiseringskälla:**

    ![Enkel azure AD-inloggning](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. Som **aktiverat**väljer du **Ja**.

    b. I textrutan **EntityId** klistrar du in värdet för **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    c. Skriv en beskrivning för konfigurationen i textrutan **IdP-beskrivning** (till exempel *Azure AD SSO*).

    d. Ladda upp **metadatafilen** som du har hämtat från Azure-portalen i textrutan **Metadatafil.**
  
    e. Skriv ett namn som är specifikt för din konfiguration i textrutan för **IdP-namn** (till exempel *Azure SP*).
  
    f. Klistra in värdet för **url-URL för utloggning** som du har kopierat från Azure-portalen i **textrutan Logout Service** URL.

    g. Klistra in värdet för **inloggningsadressen** som du har kopierat från Azure-portalen i textrutan **för tjänstens webbloggning.**

    h. Klicka på **Spara**.

1. Inaktivera alla andra autentiseringskällor.

    ![Enkel azure AD-inloggning](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till SilkRoad Life Suite.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **SilkRoad Life Suite**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **SilkRoad Life Suite**i programlistan .

    ![Länken SilkRoad Life Suite i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-silkroad-life-suite-test-user"></a>Skapa SilkRoad Life Suite-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i SilkRoad Life Suite. Arbeta med [SilkRoad Life Suite Client supportteam](https://www.silkroad.com/locations/) för att lägga till användarna i SilkRoad Life Suite-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SilkRoad Life Suite i åtkomstpanelen ska du automatiskt loggas in på Den SilkRoad Life Suite som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
