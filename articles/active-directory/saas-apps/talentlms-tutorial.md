---
title: 'Självstudiekurs: Azure Active Directory-integrering med TalentLMS | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 970ab9de270b1227884a13ac578d4c439043b20c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233368"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Självstudiekurs: Azure Active Directory-integrering med TalentLMS

I den här självstudien får du lära dig hur du integrerar TalentLMS med Azure Active Directory (Azure AD).
Genom att integrera TalentLMS med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till TalentLMS.
* Du kan aktivera dina användare automatiskt inloggade på TalentLMS (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med TalentLMS behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* TalentLMS enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* TalentLMS stöder **SP** initierade SSO

## <a name="adding-talentlms-from-the-gallery"></a>Lägga till TalentLMS från galleriet

Om du vill konfigurera integreringen av TalentLMS i Azure AD måste du lägga till TalentLMS från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till TalentLMS från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **TalentLMS**i sökrutan och välj **TalentLMS** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

    ![TalentLMS i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med TalentLMS baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i TalentLMS upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med TalentLMS måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera TalentLMS Single Sign-On](#configure-talentlms-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa TalentLMS-testanvändare](#create-talentlms-test-user)** – om du vill ha en motsvarighet till Britta Simon i TalentLMS som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med TalentLMS:

1. Välj Enkel inloggning på sidan **TalentLMS-programintegration** på [Azure-portalen](https://portal.azure.com/). **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![TalentLMS-domän och webbadresser med enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenant-name>.TalentLMSapp.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [TalentLMS Client supportteam](https://www.talentlms.com/contact) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. Kopiera **Tumavtryck** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera TalentLMS.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-talentlms-single-sign-on"></a>Konfigurera TalentLMS enkel inloggning

1. Logga in på TalentLMS-företagets webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på fliken **Användare** i avsnittet **Konto & Inställningar.**

    ![Inställningar för konto &](./media/talentlms-tutorial/IC777296.png "Inställningar för konto &")

1. Klicka på **Enkel inloggning (SSO)**,

1. Gör följande i avsnittet Enkel inloggning:

    ![Enkel inloggning](./media/talentlms-tutorial/IC777297.png "för Aha!")

    a. Välj **SAML 2.0**i listan **SSO-integreringstyp** .

    b. Klistra in värdet för **Azure AD-identifierare**i textrutan **Identitetsprovider (IDP)** som du har kopierat från Azure-portalen.

    c. Klistra in **tumavtrycksvärdet** från Azure-portalen i textrutan **för fingeravtryck för certifikat.**

    d.  Klistra in värdet för **inloggnings-URL**i textrutan **För fjärrloggning** , som du har kopierat från Azure-portalen.

    e. Klistra in värdet för url för **utloggning**i textrutan **För fjärrutloggning** , som du har kopierat från Azure-portalen.

    f. Fyll i följande:

    * Skriv i textrutan **TargetedID**`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * Skriv textrutan Förnamn i textrutan **Förnamn**`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * Skriv textrutan Efternamn i textrutan **Efternamn**`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * Skriv textrutan **e-post** i textrutan`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till TalentLMS.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **TalentLMS**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **TalentLMS**i programlistan .

    ![Länken TalentLMS i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-talentlms-test-user"></a>Skapa TalentLMS-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på TalentLMS måste de etableras i TalentLMS. När det gäller TalentLMS är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **TalentLMS-klient.**

1. Klicka på **Användare**och sedan på **Lägg till användare**.

1. Gör följande på dialogrutan **Lägg till användare:**

    ![Lägg till användare](./media/talentlms-tutorial/IC777299.png "Lägg till användare")  

    a. I textrutan **Förnamn** anger du förnamnet på en användare som **Britta**.

    b. I textrutan **Efternamn** skriver du efternamnet på användaren: **Simon**.
 
    c. I textrutan **E-postadress** skriver du e-postadressen för användaren: `brittasimon\@contoso.com`.

    d. Klicka på **Lägg till användare**.

> [!NOTE]
> Du kan använda andra TalentLMS-verktyg för att skapa konton eller API:er som tillhandahålls av TalentLMS för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen TalentLMS på åtkomstpanelen ska du automatiskt loggas in på talentlms som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

