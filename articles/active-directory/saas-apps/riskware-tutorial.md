---
title: 'Självstudiekurs: Azure Active Directory-integrering med Riskware | Microsoft-dokument'
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
ms.openlocfilehash: 6eaa1be81d3ac0733c0829bc45e1b62f8aae5755
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72027111"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Självstudiekurs: Azure Active Directory-integrering med Riskware

I den här självstudien får du lära dig hur du integrerar Riskware med Azure Active Directory (Azure AD).
Genom att integrera Riskware med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Riskware.
* Du kan aktivera dina användare så att de automatiskt loggas in på Riskware (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Riskware behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Riskware enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Riskware stöder **SP** initierade SSO

## <a name="adding-riskware-from-the-gallery"></a>Lägga till Riskware från galleriet

Om du vill konfigurera integreringen av Riskware i Azure AD måste du lägga till Riskware från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Riskware från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Riskware**i sökrutan och välj **Riskware** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

    ![Riskware i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Riskware baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Riskware upprättas.

Om du vill konfigurera och testa en enda Azure AD-inloggning med Riskware måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Riskware Single Sign-On](#configure-riskware-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Riskware-testanvändare](#create-riskware-test-user)** – om du vill ha en motsvarighet till Britta Simon i Riskware som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Riskware:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Riskware-programintegration**. **Riskware**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Riskware Domän och webbadresser enkel inloggningsinformation](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: 
    
    | Miljö| URL-mönster|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | Art| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | Demo| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. I textrutan **Identifierare (entitets-ID)** anger du URL:en: 
    
    | Miljö| URL-mönster|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | Art| `https://riskcloud.net/prod` |
    | Demo| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Riskware Client supportteam](mailto:support@pansoftware.com.au) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Riskware.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-riskware-single-sign-on"></a>Konfigurera engångs inloggning för Riskware

1. Logga in på riskware-företagets webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på **Underhåll** längst upp till höger för att öppna underhållssidan.

    ![Riskware-konfigurationer upprätthåller](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Klicka på **Autentisering**på underhållssidan.

    ![Riskware Konfiguration authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Gör följande på sidan **Autentiseringskonfiguration:**

    ![Riskware Konfiguration authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Välj **Typ** som **SAML** för autentisering.

    b. Skriv **koden** på samma sätt som AZURE_UAT i kodtextrutan.

    c. Skriv beskrivningen på så sätt som AZURE-konfiguration för SSO i textrutan **Beskrivning.**

    d. I textrutan **Enkel inloggningssida** klistrar du in värdet **för inloggnings-URL,** som du har kopierat från Azure-portalen.

    e. I textrutan **Logga ut sida** klistrar du in **url-värdet för utloggning,** som du har kopierat från Azure-portalen.

    f. I textrutan **Bokför formulärfält** skriver du fältnamnet som finns i Postsvar som innehåller SAML som SAMLResponse

    g. Skriv attribut i textrutan **XML Identity Tag Name,** som innehåller den unika identifieraren i SAML-svaret som NameID.

    h. Öppna den nedladdade **Metadata XML** från Azure-portalen i anteckningar, kopiera certifikatet från metadatafilen och klistra in det i **textrutan certifikat**

    i. I textrutan **Konsument-URL** klistrar du in värdet **för Svars-URL**, som du får från supportteamet.

    j. I textrutan **Utfärdare** klistrar du in värdet **för Identifierare**, som du får från supportteamet.

    > [!Note]
    > Kontakta [Riskware Client supportteam](mailto:support@pansoftware.com.au) för att få dessa värden

    k. Markera kryssrutan **Använd POST.**

    l. Markera kryssrutan **Använd SAML-begäran.**

    m. Klicka på **Spara**.

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

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Riskware.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Riskware**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Riskware**i programlistan .

    ![Länken Riskware i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-riskware-test-user"></a>Skapa riskware-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på Riskware måste de etableras i Riskware. I Riskware är etablering en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Riskware som säkerhetsadministratör.

1. Klicka på **Underhåll** längst upp till höger för att öppna underhållssidan. 

    ![Riskware-konfigurationen upprätthåller](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Klicka på **Personer**på underhållssidan .

    ![Personer med riskware-konfiguration](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Välj fliken **Information** och utför följande steg:

    ![Konfigurationsinformation för Riskware](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Välj **Persontyp** som medarbetare.

    b. I textrutan **Förnamn** anger du förnamnet på en användare som **Britta**.

    c. I textrutan **Efternamn** anger du efternamn på användaren som **Simon**.

1. Gör följande på fliken **Säkerhet:**

    ![Säkerhet för riskprogramkonfiguration](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. Under **Autentisering** väljer du **autentiseringsläget,** som du har konfigurerat som AZURE-konfiguration för SSO.

    b. Ange **Logon Details** e-postmeddelandet för **User ID** användaren som `brittasimon@contoso.com`.

    c. Ange lösenordet för användaren i textrutan **Lösenord.**

1. Gör följande på fliken **Organisation:**

    ![Riskware Konfiguration org](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Välj alternativet som **nivå1-organisation.**

    b. Skriv din plats i textrutan **Plats** under Avsnittet **Primär arbetsplats.**

    c. Under **Avsnittet Medarbetare** väljer du **Medarbetarstatus** som Ledig.

    d. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Riskware på åtkomstpanelen bör du automatiskt loggas in på riskmaterialet som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
