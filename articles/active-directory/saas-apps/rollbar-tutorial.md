---
title: 'Självstudiekurs: Azure Active Directory-integrering med Rollbar | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Rollbar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/15/2019
ms.author: jeedes
ms.openlocfilehash: d76f4e9d61d8fd210fe9332084f9f44d19e54eed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092685"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Självstudiekurs: Azure Active Directory-integrering med Rollbar

I den här självstudien får du lära dig hur du integrerar Rollbar med Azure Active Directory (Azure AD).
Genom att integrera Rollbar med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Rollbar.
* Du kan aktivera dina användare så att de automatiskt loggas in på Rollbar (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Rollbar behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Rollbar enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Rollbar stöder **SP och IDP** initierad SSO

## <a name="adding-rollbar-from-the-gallery"></a>Lägga till rollbar från galleriet

Om du vill konfigurera integreringen av Rollbar i Azure AD måste du lägga till Rollbar från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Rollbar från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Rollbar**i sökrutan och välj **Rollbar** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![Rollbar i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Rollbar baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Rollbar upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Rollbar måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning i rollbar](#configure-rollbar-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Rollbar-testanvändare](#create-rollbar-test-user)** - om du vill ha en motsvarighet till Britta Simon i Rollbar som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Rollbar:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Rollbar-programintegration**. **Rollbar**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![Rollbar Domän och webbadresser enkel inloggningsinformation](common/idp-intiated.png)

    a. Skriv URL:en i textrutan **Identifierare:**`https://saml.rollbar.com`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://rollbar.com/<accountname>/saml/sso/azure/`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Rollbar Domän och webbadresser enkel inloggningsinformation](common/metadata-upload-additional-signon.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://rollbar.com/<accountname>/saml/login/azure/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Kontakta [Rollbar Client support team](mailto:support@rollbar.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera rollbar.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-rollbar-single-sign-on"></a>Konfigurera enkel inloggning i rollbar

1. Logga in på Rollbar-företagets webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på **profilinställningarna** i det högra övre hörnet och klicka sedan på **Kontonamnsinställningar**.

    ![Konfiguration](./media/rollbar-tutorial/general.png)

1. Klicka på **Identitetsprovider** under SÄKERHET.

    ![Konfiguration](./media/rollbar-tutorial/configure1.png)

1. Gör följande i avsnittet **SAML-identitetsprovider:**

    ![Konfiguration](./media/rollbar-tutorial/configure2.png)

    a. Välj **AZURE** i rullgardinsmenyn **SAML-identitetsprovider.**

    b. Öppna metadatafilen i anteckningar, kopiera innehållet i den till Urklipp och klistra sedan in den i **SMS:en.**

    c. Klicka på **Spara**.

1. När du har klickat på spara-knappen blir skärmen så här:

    ![Konfiguration](./media/rollbar-tutorial/configure3.png)

    > [!NOTE]
    > För att slutföra följande steg måste du först lägga till dig själv som användare i Rollbar-appen i Azure.
    >

    a. Om du vill att alla användare ska autentisera via Azure klickar du **på logga in via din identitetsleverantör** för att autentisera via Azure igen.  

    b.  När du har återsänts till skärmen väljer du kryssrutan **Kräv inloggning via SAML Identity Provider.**

    b. Klicka på **Spara**.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Rollbar.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Rollbar**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Rollbar**i programlistan .

    ![Länken Rollbar i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-rollbar-test-user"></a>Skapa rollbartestanvändare

Om du vill att Azure AD-användare ska kunna logga in på Rollbar måste de etableras i Rollbar. När det gäller Rollbar är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på rollbarföretagets webbplats som administratör.

1. Klicka på **profilinställningarna** i det högra övre hörnet och klicka sedan på **Kontonamnsinställningar**.

    ![Användare](./media/rollbar-tutorial/general.png)

1. Klicka på **Användare**.

    ![Lägga till medarbetare](./media/rollbar-tutorial/user1.png)

1. Klicka på **Bjud in gruppmedlemmar**.

    ![Bjud in personer](./media/rollbar-tutorial/user2.png)

1. I textrutan anger du namnet på användaren som **brittasimon\@contoso.com** och klickar på **Lägg till/bjud in**.

    ![Bjud in personer](./media/rollbar-tutorial/user3.png)

1. Användaren får en inbjudan och efter att ha accepterat den skapas de i systemet.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på rollbarpanelen på åtkomstpanelen ska du automatiskt loggas in i den rollbar som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

