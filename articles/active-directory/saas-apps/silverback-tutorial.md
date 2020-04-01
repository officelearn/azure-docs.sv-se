---
title: 'Självstudiekurs: Azure Active Directory-integrering med Silverback | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Silverback.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32cfc96f-2137-49ff-818b-67feadcd73b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 3c4eab02ed0c7c09fe9b5893bbaaf7cbe1c8028f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090915"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Självstudiekurs: Azure Active Directory-integrering med Silverback

I den här självstudien får du lära dig hur du integrerar Silverback med Azure Active Directory (Azure AD).
Genom att integrera Silverback med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Silverback.
* Du kan aktivera dina användare så att de automatiskt loggas in på Silverback (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Silverback behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Silverback enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Silverback stöder **SP** initierade SSO

## <a name="adding-silverback-from-the-gallery"></a>Lägga till Silverback från galleriet

Om du vill konfigurera integreringen av Silverback i Azure AD måste du lägga till Silverback från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Silverback från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Silverback**i sökrutan och välj **Silverback** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![Silverback i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Silverback baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Silverback upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Silverback måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Silverback Single Sign-On](#configure-silverback-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Silverback-testanvändare](#create-silverback-test-user)** – om du vill ha en motsvarighet till Britta Simon i Silverback som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Silverback:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Silverback-programintegration**. **Silverback**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Silverback-domän och webbadresser med enkel inloggning](common/sp-identifier-reply.png)

    a. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<YOURSILVERBACKURL>.com/ssp`

    b. Skriv en URL med följande mönster i rutan **Identifierare:**`<YOURSILVERBACKURL>.com`

    c. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta Supportteamet för [Silverback-klienten](mailto:helpdesk@matrix42.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-silverback-single-sign-on"></a>Konfigurera enkel inloggning för Silverback

1. I en annan webbläsare loggar du in på din Silverback-server som administratör.

2. Navigera till > **administratörsautentiseringsprovidern**. **Admin**

3. Gör följande på sidan Inställningar för **autentiseringsprovider:**

    ![Adminen](./media/silverback-tutorial/tutorial_silverback_admin.png)

    a.  Klicka på **Importera från URL**.

    b.  Klistra in den kopierade metadata-URL:en och klicka på **OK**.

    c.  Bekräfta med **OK** då värdena fylls i automatiskt.

    d.  Aktivera **Visa på inloggningssidan**.

    e.  Aktivera **dynamisk användarskapning** om du vill lägga till av Azure AD-behöriga användare automatiskt (valfritt).

    f.  Skapa en **rubrik** för knappen på självbetjäningsportalen.

    g.  Ladda upp en **ikon** genom att klicka på **Välj fil**.

    h.  Välj **bakgrundsfärg** för knappen.

    i.  Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Silverback.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Silverback**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Silverback**i programlistan .

    ![Silverback-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-silverback-test-user"></a>Skapa Silverback-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på Silverback måste de etableras i Silverback. I Silverback är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Din Silverback Server som administratör.

2. Navigera till **Användare** och **lägg till en ny enhetsanvändare**.

3. Gör följande på sidan **Grundläggande:**

    ![Användaren](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. I textrutan **Användarnamn** anger du namnet på användaren som **Britta**.

    b. I textrutan **Förnamn** anger du förnamnet på användaren som **Britta**.

    c. I textrutan **Efternamn** anger du efternamn på användaren som **Simon**.

    d. I textrutan **E-postadress** anger du e-postmeddelandet för användaren som **Brittasimon@contoso.com**.

    e. Ange ditt lösenord i textrutan **Lösenord.**

    f. Ange lösenordet och bekräfta igen i textrutan **Bekräfta lösenord.**

    g. Klicka på **Spara**.

> [!NOTE]
> Om du inte vill skapa varje användare manuellt Aktivera kryssrutan För **skapande av dynamisk användare** under **Admin** > **Authentication Provider**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Silverback på åtkomstpanelen ska du automatiskt loggas in på den Silverback som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

