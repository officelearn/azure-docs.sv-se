---
title: 'Självstudiekurs: Azure Active Directory-integrering med Yodeck | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Yodeck.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b2c8dccb-eeb0-4f4d-a24d-8320631ce819
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: af0748f36e8d2299921f987c517f7a4923bd0d55
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086482"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Självstudiekurs: Azure Active Directory-integrering med Yodeck

I den här självstudien får du lära dig hur du integrerar Yodeck med Azure Active Directory (Azure AD).
Genom att integrera Yodeck med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Yodeck.
* Du kan aktivera dina användare så att de automatiskt loggas in på Yodeck (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Yodeck behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Yodeck enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Yodeck stöder **SP** och **IDP** initierade SSO

## <a name="adding-yodeck-from-the-gallery"></a>Lägga till Yodeck från galleriet

Om du vill konfigurera integreringen av Yodeck i Azure AD måste du lägga till Yodeck från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Yodeck från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Yodeck**i sökrutan och välj **Yodeck** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![Yodeck i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Yodeck baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Yodeck upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Yodeck måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Yodeck Single Sign-On](#configure-yodeck-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Yodeck-testanvändare](#create-yodeck-test-user)** – om du vill ha en motsvarighet till Britta Simon i Yodeck som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Yodeck:

1. Välj [Azure portal](https://portal.azure.com/)Enkel inloggning på sidan Yodeck-programintegration på sidan **För yodeck-program.** **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande steg:

    ![Yodeck-domän och webbadresser med enkel inloggning](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du en URL: `https://app.yodeck.com/api/v1/account/metadata/`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![image](common/both-preintegrated-signon.png)

    Skriv en URL i textrutan **Sign-on-URL:**`https://app.yodeck.com/login`

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-yodeck-single-sign-on"></a>Konfigurera Yodeck Enkel inloggning

1. Logga in på din Yodeck-företagswebbplats som administratör i ett annat webbläsarfönster.

2. Klicka på Alternativet **Användarinställningar** längst upp till höger på sidan och välj **Kontoinställningar**.

    ![Yodeck-konfiguration](./media/yodeck-tutorial/configure1.png)

3. Välj **SAML** och utför följande steg:

    ![Yodeck-konfiguration](./media/yodeck-tutorial/configure2.png)

    a. Välj **Importera från URL**.

    b. I **URL-textrutan** klistrar du in **url-värdet för App Federation Metadata,** som du har kopierat från Azure-portalen och klickar på **Importera**.
    
    c. När du har importerat Url för metadata för **appfederation**fylls de återstående fälten i automatiskt.

    d. Klicka på **Spara**.

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

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Yodeck.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Yodeck**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Yodeck**i programlistan .

    ![Länken Yodeck i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-yodeck-test-user"></a>Skapa Yodeck-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på Yodeck måste de etableras i Yodeck. När det gäller Yodeck är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din Yodeck-företagswebbplats som administratör.

2. Klicka på Alternativet **Användarinställningar** längst upp till höger på sidan och välj **Användare**.

    ![Lägga till medarbetare](./media/yodeck-tutorial/user1.png)

3. Klicka på **+Användare** för att öppna fliken **Användarinformation.**

    ![Lägga till medarbetare](./media/yodeck-tutorial/user2.png)

4. I dialogrutan **Användarinformation** utför du följande steg:

    ![Lägga till medarbetare](./media/yodeck-tutorial/user3.png)

    a. Skriv förnamnet för användaren som **Britta**i textrutan **Förnamn** .

    b. I textrutan **Efternamn** skriver du efternamnet: **Simon**.

    c. I textrutan **E-post** skriver du e-postadressen för användaren: brittasimon@contoso.com.

    d. Välj lämpligt **kontobehörigheter** enligt organisationens krav.
    
    e. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Yodeck på åtkomstpanelen ska du automatiskt loggas in på den Yodeck som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

