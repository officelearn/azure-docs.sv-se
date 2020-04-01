---
title: 'Självstudiekurs: Azure Active Directory-integrering med Procore SSO | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Procore SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca6863a6b02e867afd732ce1662136051b8afec8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093666"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Självstudiekurs: Azure Active Directory-integrering med Procore SSO

I den här självstudien får du lära dig hur du integrerar Procore SSO med Azure Active Directory (Azure AD).
Genom att integrera Procore SSO med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Procore SSO.
* Du kan aktivera dina användare automatiskt inloggad på Procore SSO (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Procore SSO behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Procore SSO enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Procore SSO stöder **IDP-initierad** SSO

## <a name="adding-procore-sso-from-the-gallery"></a>Lägga till Procore SSO från galleriet

Om du vill konfigurera integreringen av Procore SSO i Azure AD måste du lägga till Procore SSO från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Procore SSO från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Procore SSO**i sökrutan och välj **Procore SSO** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

    ![Procore SSO i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Procore SSO baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Procore SSO upprättas.

Om du vill konfigurera och testa azure AD-enkel inloggning med Procore SSO måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Procore SSO Single Sign-On](#configure-procore-sso-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Procore SSO-testanvändare](#create-procore-sso-test-user)** – om du vill ha en motsvarighet till Britta Simon i Procore SSO som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du enkel Azure AD-inloggning med Procore SSO:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Procore SSO-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![Procore SSO-domän och webbadresser enkel inloggningsinformation](common/preintegrated.png)

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Procore SSO** enligt dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-procore-sso-single-sign-on"></a>Konfigurera Procore SSO enkel inloggning

1. Om du vill konfigurera enkel inloggning på **Procore SSO-sidan** loggar du in på procore-företagets webbplats som administratör.

2. Från verktygslådan rullgardinsmenyn, klicka på **Admin** för att öppna SSO inställningar sidan.

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/procore_tool_admin.png)

3. Klistra in värdena i rutorna enligt beskrivningen nedan-

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. Klistra in värdet för **Azure AD-identifierare** som du har kopierat från Azure-portalen i textrutan URL för **enkel inloggning.**

    b. Klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen i **rutan SAML Sign On Target URL.**

    c. Öppna nu **XML-koden för federationsmetadata** som hämtats ovan från Azure-portalen och kopiera certifikatet i taggen **X509Certificate**. Klistra in det kopierade värdet i rutan **Certifikat för enkel inloggning x509.**

4. Klicka på **Spara ändringar**.

5. Efter dessa inställningar måste du skicka **domännamnet** (t.ex. **contoso.com)** genom vilket du loggar in på Procore till [Procore Support-teamet](https://support.procore.com/) och de aktiverar federerade SSO för den domänen.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Procore SSO.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Procore SSO**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Procore SSO**i programlistan .

    ![Länken Procore SSO i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-procore-sso-test-user"></a>Skapa Procore SSO-testanvändare

Följ stegen nedan för att skapa en Procore-testanvändare på Procore SSO-sidan.

1. Logga in på procore-företagets webbplats som administratör.    

2. Från verktygslådan rullgardinsmenyn, klicka på **Katalog** för att öppna företagets katalogsida.

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Klicka på **Lägg till ett personalternativ** för att öppna formuläret och ange utföra följande alternativ -

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/Procore_user_add.png)

    a. Skriv användarens förnamn som **Britta**i textrutan **Förnamn** .

    b. Skriv användarens efternamn som **Simon**i textrutan **Efternamn** .

    c. Skriv **Email Address** användarens e-postadress som BrittaSimon@contoso.com.

    d. Välj **behörighetsmall** som **Använd behörighetsmall senare**.

    e. Klicka på **Skapa**.

4. Kontrollera och uppdatera informationen för den nyligen tillagda kontakten.

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/Procore_user_check.png)

5. Klicka på **Spara och skicka inbjudan** (om en inbjudan via e-post krävs) eller **Spara** (Spara direkt) för att slutföra användarregistreringen.
    
    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Procore SSO-panelen på åtkomstpanelen ska du automatiskt loggas in på den Procore SSO som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

