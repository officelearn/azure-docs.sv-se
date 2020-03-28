---
title: 'Självstudiekurs: Azure Active Directory-integrering med TeamSeer | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TeamSeer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 3e505e4823db8c9f42bbff216f30326081110dcf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088967"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Självstudiekurs: Azure Active Directory-integrering med TeamSeer

I den här självstudien får du lära dig hur du integrerar TeamSeer med Azure Active Directory (Azure AD).
Genom att integrera TeamSeer med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till TeamSeer.
* Du kan aktivera dina användare automatiskt inloggade på TeamSeer (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med TeamSeer behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* TeamSeer enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* TeamSeer stöder **SP** initierade SSO

## <a name="adding-teamseer-from-the-gallery"></a>Lägga till TeamSeer från galleriet

Om du vill konfigurera integreringen av TeamSeer i Azure AD måste du lägga till TeamSeer från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till TeamSeer från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **TeamSeer**i sökrutan och välj **TeamSeer** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

     ![TeamSeer i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med TeamSeer baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i TeamSeer upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med TeamSeer måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera TeamSeer Single Sign-On](#configure-teamseer-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa TeamSeer-testanvändare](#create-teamseer-test-user)** – om du vill ha en motsvarighet till Britta Simon i TeamSeer som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med TeamSeer:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **TeamSeer-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![TeamSeer-domän och webbadresser med enkel inloggning](common/sp-signonurl.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://www.teamseer.com/<companyid>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [TeamSeer Client supportteam](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera TeamSeer.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-teamseer-single-sign-on"></a>Konfigurera TeamSeer enkel inloggning

1. Logga in på teamSeer-företagets webbplats som administratör i ett annat webbläsarfönster.

1. Gå till **HR Admin**.

    ![HR-administratör](./media/teamseer-tutorial/ic789634.png "HR-administratör")

1. Klicka på **Installationsprogrammet**.

    ![Installation](./media/teamseer-tutorial/ic789635.png "Installation")

1. Klicka på **Konfigurera INFORMATION om SAML-provider**.

    ![SAML-inställningar](./media/teamseer-tutorial/ic789636.png "SAML-inställningar")

1. Gör följande i avsnittet SAML-providerinformation:

    ![SAML-inställningar](./media/teamseer-tutorial/ic789637.png "SAML-inställningar")

    a. I **URL-textrutan** klistrar du in värdet **för inloggnings-URL,** som du har kopierat från Azure-portalen.

    b. Öppna ditt bas-64-kodade certifikat i anteckningar, kopiera innehållet i det till Urklipp och klistra sedan in det i textrutan för offentligt certifikat för **IdP.**

1. Så här slutför du KONFIGURATIONEN AV SAML-providern:

    ![SAML-inställningar](./media/teamseer-tutorial/ic789638.png "SAML-inställningar")

    a. Skriv testanvändarens e-postadress i **test-e-postadressen.**
  
    b. Skriv tjänstens utställar-URL i textrutan **Utfärdare.**
  
    c. Klicka på **Spara**.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till TeamSeer.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **TeamSeer**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **TeamSeer**i programlistan .

    ![Länken TeamSeer i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-teamseer-test-user"></a>Skapa TeamSeer-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på TeamSeer måste de etableras i ShiftPlanning. När det gäller TeamSeer är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på **teamseers** företagswebbplats som administratör.

1. Gå till **HR-administratörsanvändare \> ** och klicka sedan på **Kör guiden Ny användare**.

    ![HR-administratör](./media/teamseer-tutorial/ic789640.png "HR-administratör")

1. Gör följande i avsnittet **Användarinformation:**

    ![Användarinformation](./media/teamseer-tutorial/ic789641.png "Användarinformation")

    a. Skriv **förnamn**, **efternamn**, **användarnamn (e-postadress)** för ett giltigt Azure AD-konto som du vill etablera i relaterade textrutor.
  
    b. Klicka på **Nästa**.

1. Följ anvisningarna på skärmen för att lägga till en ny användare och klicka på **Slutför**.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för att skapa andra TeamSeer-användarkonton som tillhandahålls av TeamSeer för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på teamhanteraren på åtkomstpanelen bör du automatiskt loggas in på teamhanteraren som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
