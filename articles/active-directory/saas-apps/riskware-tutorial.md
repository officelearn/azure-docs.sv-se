---
title: 'Självstudier: Azure Active Directory integration med riskmaterial | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och risk.
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
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027111"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Självstudier: Azure Active Directory integration med risk

I den här självstudien får du lära dig att integrera riskmaterial med Azure Active Directory (Azure AD).
Integrering av riskmaterial med Azure AD ger följande fördelar:

* Du kan styra i Azure AD som har åtkomst till riskmaterial.
* Du kan göra det möjligt för användarna att automatiskt vara inloggade på riskmaterial (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med risk behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Aktive rad prenumeration med enkel inloggnings risk

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Riskfylld stöder **SP** -INITIERAd SSO

## <a name="adding-riskware-from-the-gallery"></a>Lägga till riskmaterial från galleriet

Du måste lägga till riskmaterial från galleriet till din lista över hanterade SaaS-appar för att kunna konfigurera integrering av riskmaterial i Azure AD.

**Utför följande steg för att lägga till riskmaterial från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **riskmaterial**, väljer **riskmaterial** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![Riskmaterial i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med risk baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i risk upprättas.

Om du vill konfigurera och testa enkel inloggning med risk för Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning med risk för enkel inloggning](#configure-riskware-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa risk test användare](#create-riskware-test-user)** – för att få en motsvarighet till Britta Simon i riskmaterial som är länkat till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med risk för Azure AD:

1. På sidan [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på sidan **riskfylld** program integrering.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för riskfyllda domäner och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL enligt följande mönster:
    
    | Miljö| URL-mönster|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | ORDER| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. I text rutan **identifierare (enhets-ID)** anger du URL: en:
    
    | Miljö| URL-mönster|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | ORDER| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Hämta värdet genom att kontakta [riskfylld support team](mailto:support@pansoftware.com.au) . Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

6. På sidan **Konfigurera risker** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-riskware-single-sign-on"></a>Konfigurera enkel inloggning för riskbaserade användare

1. Logga in på din riskfyllda företags webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på **Underhåll** längst upp till höger för att öppna underhålls sidan.

    ![Konfigurationer av riskabelt underhåll](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. På sidan underhåll klickar du på **autentisering**.

    ![Authen för konfiguration av riskmaterial](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Utför följande steg på sidan **konfiguration av autentisering** :

    ![Authenconfig för konfiguration av riskmaterial](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Välj **typ** som **SAML** för autentisering.

    b. I text rutan **kod** skriver du koden som AZURE_UAT.

    c. I text rutan **Beskrivning** skriver du din beskrivning som Azure-konfiguration för SSO.

    d. I text rutan för **enkel inloggnings sida** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    e. I text rutan **Logga ut sida** klistrar du in URL-värdet för **utloggning** som du har kopierat från Azure Portal.

    f. I text rutan **post-formulär fält** skriver du det fält namn som finns i post-svar som innehåller SAML som SAMLResponse

    g. I text rutan **namn på XML-identitetsprovider** skriver du Attribute, som innehåller den unika identifieraren i SAML-svaret som NameID.

    h. Öppna den hämtade **metadata-XML-** filen från Azure Portal i anteckningar, kopiera certifikatet från metadatafilen och klistra in det i **certifikat** text rutan

    i. I text rutan **konsument-URL** klistrar du in värdet för **svars-URL**, som du får från support teamet.

    j. I text rutan **utfärdare** klistrar du in värdet för **identifierare**, som du får från support teamet.

    > [!Note]
    > Ge [support teamet](mailto:support@pansoftware.com.au) för kontakt risk för att hämta dessa värden

    k. Markera kryss rutan **Använd post** .

    l. Markera kryss rutan **Använd SAML-begäran** .

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
  
    b. I fältet **användar namn** anger du `brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till riskmaterial.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **riskfyllda**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **risk**.

    ![Länken riskmaterial i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-riskware-test-user"></a>Skapa risk test användare

Om du vill att Azure AD-användare ska kunna logga in på riskmaterial måste de tillhandahållas av risk. I riskmaterial är etableringen en manuell uppgift.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på risk som en säkerhets administratör.

1. Klicka på **Underhåll** längst upp till höger för att öppna underhålls sidan. 

    ![Konfiguration av riskabelt risk underhåll](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. På sidan underhåll klickar du på **personer**.

    ![Personer med riskfylld konfiguration](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Välj fliken **information** och utför följande steg:

    ![Konfigurations information för riskmaterial](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Välj **person typ** som anställd.

    b. I text rutan **förnamn** anger du det första namnet på användaren som **Britta**.

    c. I **text rutan efter namn anger** du det senaste namnet på användaren som **Simon**.

1. Utför följande steg på fliken **säkerhet** :

    ![Säkerhet för riskabel konfiguration](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. Under avsnittet **autentisering** väljer du **autentiseringsläge** , som du har konfigurerat som Azure-konfiguration för SSO.

    b. Under avsnittet **information om inloggning** i text rutan **användar-ID** anger du e-postmeddelandet som `brittasimon@contoso.com`.

    c. Ange användarens lösen ord i text rutan **lösen ord** .

1. Utför följande steg på fliken **organisation** :

    ![Organisation för farlighets konfiguration](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Välj alternativet som **level1** organisation.

    b. Under **personens primära arbets plats** avsnitt skriver du din plats i text rutan **plats** .

    c. Under **personal** -avsnittet väljer du **personal status** som vardaglig.

    d. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen riskmaterial på åtkomst panelen, bör du loggas in automatiskt på den risk som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
