---
title: 'Självstudiekurs: Azure Active Directory-integrering med Pingboard | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Pingboard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 341d8dd712b858572ec5df76b176258ca87c8857
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094432"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Självstudiekurs: Azure Active Directory-integrering med Pingboard

I den här självstudien får du lära dig hur du integrerar Pingboard med Azure Active Directory (Azure AD).
Genom att integrera Pingboard med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Pingboard.
* Du kan aktivera dina användare så att de automatiskt loggas in på Pingboard (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Pingboard behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Pingboard enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Pingboard stöder **SP** och **IDP** initierad SSO

* Pingboard stöder [automatisk användaretablering](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial) 

## <a name="adding-pingboard-from-the-gallery"></a>Lägga till Pingboard från galleriet

Om du vill konfigurera integreringen av Pingboard i Azure AD måste du lägga till Pingboard från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Pingboard från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Pingboard**i sökrutan och välj **Pingboard** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![Pingboard i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Pingboard baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Pingboard upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Pingboard måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Pingboard Single Sign-On](#configure-pingboard-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Pingboard-testanvändare](#create-pingboard-test-user)** – om du vill ha en motsvarighet till Britta Simon i Pingboard som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Pingboard:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Pingboard-programintegration**. **Pingboard**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![Pingboarddomän och webbadresser med enkel inloggning](common/idp-intiated.png)

    a. Skriv en URL i textrutan **Identifierare:**`http://app.pingboard.com/sp`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<entity-id>.pingboard.com/auth/saml/consume`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Pingboarddomän och webbadresser med enkel inloggning](common/metadata-upload-additional-signon.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska svars-URL:en och inloggnings-URL:en. Kontakta [Pingboard Client supportteam](https://support.pingboard.com/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Pingboard.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-pingboard-single-sign-on"></a>Konfigurera pingboard enkel inloggning

1. Om du vill konfigurera SSO på Pingboard-sidan öppnar du ett nytt webbläsarfönster och loggar in på pingboardkontot. Du måste vara pingboardadministratör för att kunna konfigurera enkel inloggning.

2. På den övre menyn väljer du **Appar >-integreringar**

    ![Konfigurera enkel inloggning](./media/pingboard-tutorial/Pingboard_integration.png)

3. Leta reda på panelen **"Azure Active Directory"** på sidan **Integrationer** och klicka på den.

    ![Pingboard Single Sign-On Integration](./media/pingboard-tutorial/Pingboard_aad.png)

4. I modal som följer klickar du på **"Konfigurera"**

    ![Knappen Konfiguration av Pingboard](./media/pingboard-tutorial/Pingboard_configure.png)

5. På följande sida märker du att "Azure SSO Integration is enabled". Öppna den nedladdade METADATA XML-filen i ett anteckningsblock och klistra in innehållet i **IDP-metadata**.

    ![Konfigurationsskärmen för Pingboard SSO](./media/pingboard-tutorial/Pingboard_sso_configure.png)

6. Filen valideras, och om allt är korrekt aktiveras nu enkel inloggning.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Pingboard.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Pingboard**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Pingboard i**programlistan .

    ![Länken Pingboard i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-pingboard-test-user"></a>Skapa pingboardtestanvändare

Syftet med detta avsnitt är att skapa en användare som heter Britta Simon i Pingboard. Pingboard stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](pingboard-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på pingboardföretagets webbplats som administratör.

2. Klicka på **knappen Lägg till medarbetare** på **katalogsidan.**

    ![Lägga till medarbetare](./media/pingboard-tutorial/create_testuser_add.png)

3. Gör följande på dialogsidan **"Lägg till medarbetare":**

    ![Bjud in personer](./media/pingboard-tutorial/create_testuser_name.png)

    a. Skriv det fullständiga namnet på användaren som **Britta Simon**i textrutan **Fullständigt namn** .

    b. Skriv **Email** e-postadressen till användaren som **brittasimon@contoso.com**.

    c. Skriv befattningstiteln Britta Simon i textrutan **Jobbrubrik.**

    d. Välj platsen för Britta Simon i listrutan **Plats.**

    e. Klicka på **Lägg till**.

4. En bekräftelse skärm kommer upp för att bekräfta tillägg av användaren.

    ![Bekräfta](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på pingboardpanelen på åtkomstpanelen ska du automatiskt loggas in på pingboarden som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurera etablering av användare](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial)
