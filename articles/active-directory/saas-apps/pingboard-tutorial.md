---
title: 'Självstudier: Azure Active Directory-integrering med Pingboard | Microsoft Docs'
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
ms.openlocfilehash: 45b542cb644666a6bf1df3dc76cbf54693330810
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438911"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Självstudier: Azure Active Directory-integrering med Pingboard

I den här självstudien får du lära dig hur du integrerar Pingboard med Azure Active Directory (AD Azure).
Integrera Pingboard med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Pingboard.
* Du kan aktivera användarna att vara automatiskt inloggad till Pingboard (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Pingboard, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Pingboard enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Pingboard **SP** och **IDP** -initierad SSO

* Har stöd för Pingboard [automatisk användaretablering](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial) 

## <a name="adding-pingboard-from-the-gallery"></a>Att lägga till Pingboard från galleriet

För att konfigurera integrering av Pingboard i Azure AD, som du behöver lägga till Pingboard från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Pingboard från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Pingboard**väljer **Pingboard** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Pingboard i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Pingboard baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Pingboard upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Pingboard, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Pingboard Single Sign-On](#configure-pingboard-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Pingboard](#create-pingboard-test-user)**  – du har en motsvarighet för Britta Simon i Pingboard som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Pingboard:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Pingboard** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Pingboard domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du in en URL: `http://app.pingboard.com/sp`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<entity-id>.pingboard.com/auth/saml/consume`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Pingboard domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska svars-URL:en och inloggnings-URL:en. Kontakta [Pingboard klienten supportteamet](https://support.pingboard.com/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. På den **konfigurera Pingboard** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-pingboard-single-sign-on"></a>Konfigurera Pingboard Single Sign-On

1. För att konfigurera enkel inloggning på Pingboard sida, öppna ett nytt webbläsarfönster och logga in på ditt Pingboard-konto. Du måste vara administratör för Pingboard att konfigurera enkel inloggning.

2. På den översta menyn, Välj **appar > integreringar**

    ![Konfigurera enkel inloggning](./media/pingboard-tutorial/Pingboard_integration.png)

3. På den **integreringar** sidan, hitta den **”Azure Active Directory”** panelen och klicka på den.

    ![Pingboard Single-Sign-On-integrering](./media/pingboard-tutorial/Pingboard_aad.png)

4. I modal som följer Klicka **”konfigurera”**

    ![Pingboard configuration knappen](./media/pingboard-tutorial/Pingboard_configure.png)

5. På sidan Observera att ”Azure SSO-integrering är aktiverat”. Öppna den hämta Metadata XML-filen i anteckningar och klistra in innehållet i **IDP Metadata**.

    ![Skärm för konfiguration av Pingboard SSO](./media/pingboard-tutorial/Pingboard_sso_configure.png)

6. Filen har verifierats och om allt är korrekt, enkel inloggning kommer nu att aktiveras.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Pingboard.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Pingboard**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Pingboard**.

    ![Länken Pingboard i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-pingboard-test-user"></a>Skapa Pingboard testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Pingboard. Pingboard stöder automatisk användaretablering, vilket är som standard aktiverat. Du hittar mer information [här](pingboard-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på webbplatsen Pingboard företag som administratör.

2. Klicka på **”Lägg till medarbetare”** knappen **Directory** sidan.

    ![Lägga till medarbetare](./media/pingboard-tutorial/create_testuser_add.png)

3. På den **”Lägg till medarbetare”** dialogrutan utför följande steg:

    ![Bjuda in](./media/pingboard-tutorial/create_testuser_name.png)

    a. I den **fullständigt namn** textrutan typ det fullständiga namnet för användaren som **Britta Simon**.

    b. I den **e-post** textrutan typ e-postadressen för användaren som **brittasimon@contoso.com**.

    c. I den **befattning** textrutan skriver Britta Simon befattning.

    d. I den **plats** listrutan Välj platsen för Britta Simon.

    e. Klicka på **Lägg till**.

4. En bekräftelseskärm visas för att bekräfta att lägga till användaren.

    ![Bekräfta](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Pingboard i åtkomstpanelen, bör det vara loggas in automatiskt till Pingboard som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurera användarförsörjning](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial)
