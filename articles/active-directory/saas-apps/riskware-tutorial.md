---
title: 'Självstudier: Azure Active Directory-integrering med Riskware | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Riskware.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 08db463c92e4ee34819f446c3827ed8dfddc02d7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685310"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Självstudier: Azure Active Directory-integrering med Riskware

I den här självstudien får du lära dig hur du integrerar Riskware med Azure Active Directory (AD Azure).
Integrera Riskware med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Riskware.
* Du kan aktivera användarna att vara automatiskt inloggad till Riskware (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Riskware, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Riskware enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Riskware **SP** -initierad SSO

## <a name="adding-riskware-from-the-gallery"></a>Att lägga till Riskware från galleriet

För att konfigurera integrering av Riskware i Azure AD, som du behöver lägga till Riskware från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Riskware från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Riskware**väljer **Riskware** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Riskware i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Riskware baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Riskware upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Riskware, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Riskware Single Sign-On](#configure-riskware-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Riskware](#create-riskware-test-user)**  – du har en motsvarighet för Britta Simon i Riskware som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Riskware:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Riskware** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Riskware domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL enligt följande mönster:
    
    | Miljö| URL-mönster|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. I den **identifierare (entitets-ID)** text skriver du URL: en:
    
    | Miljö| URL-mönster|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Riskware klienten supportteamet](mailto:support@pansoftware.com.au) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **konfigurera Riskware** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-riskware-single-sign-on"></a>Konfigurera Riskware Single Sign-On

1. I ett annat webbläsarfönster, loggar du in din Riskware företagets webbplats som administratör.

1. Klicka på upp till höger, **Underhåll** att öppna underhållssidan.

    ![Riskware konfigurationer Underhåll](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Underhåll på sidan klickar du på **autentisering**.

    ![Riskware Configuration authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. I **Autentiseringskonfiguration** utför följande steg:

    ![Riskware Configuration authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Välj **typ** som **SAML** för autentisering.

    b. I den **kod** textrutan skriver in din kod som AZURE_UAT.

    c. I den **beskrivning** textrutan skriver du en beskrivning som AZURE-konfiguration för enkel inloggning.

    d. I **enkel inloggning på sidan** textrutan klistra in den **inloggnings-URL** värde, som du har kopierat från Azure-portalen.

    e. I **logga ut sidan** textrutan klistra in den **URL för utloggning** värde, som du har kopierat från Azure-portalen.

    f. I den **Post formulärfält** textrutan skriver fältnamnet finns i inlägget svar som innehåller SAML som SAMLResponse

    g. I den **XML-taggnamnet för Identity** textrutan type-attributet som innehåller den unika identifieraren i SAML-svar som NameID.

    h. Öppna den hämtade **Xml-Metadata för** kopiera certifikatet från metadatafilen från Azure-portalen i anteckningar och klistra in den i den **certifikat** textrutan

    i. I **konsument URL** textrutan klistra in värdet för **svars-URL**, som du får från supporten.

    j. I **utfärdare** textrutan klistra in värdet för **identifierare**, som du får från supporten.

    > [!Note]
    > Kontakta [Riskware klienten supportteamet](mailto:support@pansoftware.com.au) att hämta dessa värden

    k. Välj **användning efter** kryssrutan.

    l. Välj **Använd SAML-begäran** kryssrutan.

    m. Klicka på **Spara**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Riskware.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Riskware**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Riskware**.

    ![Länken Riskware i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-riskware-test-user"></a>Skapa Riskware testanvändare

Om du vill aktivera Azure AD-användare att logga in på Riskware, måste de etableras i Riskware. I Riskware är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på Riskware som en administratör.

1. Klicka på upp till höger, **Underhåll** att öppna underhållssidan. 

    ![Riskware bibehåller](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Underhåll på sidan klickar du på **personer**.

    ![Riskware Configuration personer](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Välj **information** fliken och utför följande steg:

    ![Riskware konfigurationsinformation](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Välj **typ** som anställd.

    b. I **Förnamn** textrutan Ange först namnet på användaren som **Britta**.

    c. I **efternamn** textrutan Ange efternamn för användaren som **Simon**.

1. På den **Security** fliken, utför följande steg:

    ![Riskware Configuration säkerhet](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. Under **autentisering** väljer den **autentisering** läge, som du har konfigurera som AZURE-konfiguration för enkel inloggning.

    b. Under **inloggningsuppgifter** avsnittet i den **användar-ID** textrutan Ange e-postadress för användaren som `brittasimon@contoso.com`.

    c. I den **lösenord** textrutan anger du lösenordet för användaren.

1. På den **organisation** fliken, utför följande steg:

    ![Riskware Configuration org](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Välj alternativet som **Level1** organisation.

    b. Under **persons primära arbetsplats** avsnittet i den **plats** textrutan skriver din plats.

    c. Under **medarbetare** väljer **anställningsstatus** som Vardaglig.

    d. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Riskware i åtkomstpanelen, bör det vara loggas in automatiskt till Riskware som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
