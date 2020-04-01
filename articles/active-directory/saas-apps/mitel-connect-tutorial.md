---
title: 'Självstudiekurs: Azure Active Directory-integrering med Mitel Connect | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mitel Connect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 30a4dcbd15d8a47d99b3a61879a7e61938d3b133
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160533"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Självstudiekurs: Azure Active Directory-integrering med Mitel MiCloud Connect

I den här självstudien får du lära dig hur du integrerar Mitel MiCloud Connect med Azure Active Directory (Azure AD). Genom att integrera MiCloud Connect med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till MiCloud Connect-appar med hjälp av sina företagsautentiseringsuppgifter.
* Du kan aktivera användare på ditt konto så att de automatiskt loggas in på MiCloud Connect (Enkel inloggning) med sina Azure AD-konton.


Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med MiCloud Connect behöver du följande objekt:

* En Azure AD-prenumeration

  Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Ett Mitel MiCloud Connect-konto

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD single sign-on (SSO).

* Mitel Connect stöder **SP** initierade SSO

## <a name="adding-mitel-connect-from-the-gallery"></a>Lägga till Mitel Connect från galleriet

Om du vill konfigurera integreringen av Mitel Connect i Azure AD måste du lägga till Mitel Connect från galleriet i listan över hanterade SaaS-appar i Azure-portalen.

**Så här lägger du till Mitel Connect från galleriet:**

1. Klicka på **Azure Active Directory** på den vänstra navigeringspanelen på **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Klicka på **Företagsprogram** och sedan på **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Klicka på **Nytt program**.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Mitel Connect** i sökfältet, klicka på **Mitel Connect** från resultatpanelen och klicka sedan på Lägg **till**.

     ![Mitel Connect i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med MiCloud Connect baserat på en testanvändare som heter **Britta Simon**. För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i MiCloud Connect upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med MiCloud Connect måste du utföra följande steg:

1. **[Konfigurera MiCloud Connect för SSO med Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)** – så att användarna kan använda den här funktionen och konfigurera SSO-inställningarna på programsidan.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
4. **[Skapa en Mitel MiCloud Connect-testanvändare](#create-a-mitel-micloud-connect-test-user)** – om du vill ha en motsvarighet till Britta Simon på ditt MiCloud Connect-konto som är länkad till Azure AD-representationen av användaren.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Konfigurera MiCloud Connect för SSO med Azure AD

I det här avsnittet aktiverar du azure AD enkel inloggning för MiCloud Connect i Azure-portalen och konfigurerar ditt MiCloud Connect-konto så att SSO kan använda Azure AD.

För att konfigurera MiCloud Connect med SSO för Azure AD är det enklast att öppna Azure-portalen och Mitel-kontoportalen sida vid sida. Du måste kopiera en del information från Azure-portalen till Mitel-kontoportalen och några från Mitel-kontoportalen till Azure-portalen.


1. Så här öppnar du konfigurationssidan i [Azure-portalen:](https://portal.azure.com/)

    a. Klicka på Enkel inloggning på sidan Integrering av **Mitel** **Connect-program**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

    b. Klicka på **SAML**i dialogrutan **Välj en enskild inloggningsmetod** .

    ![Välja läge för enkel inloggning](common/select-saml-option.png)
    
    Den SAML-baserade inloggningssidan visas.

2. Så här öppnar du konfigurationsdialogrutan i Mitel-kontoportalen:

    a. Klicka på **Tilläggsfunktioner**på **menyn Telefonsystem** .

    b. Till höger om **Enkel inloggning**klickar du på **Aktivera** eller **Inställningar**.
    
    Dialogrutan Anslut inställningar för enkel inloggning visas.
    
3. Markera kryssrutan **Aktivera enkel inloggning.**
    ![bild](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. Klicka på ikonen **Redigera** i avsnittet **Grundläggande SAML-konfiguration i Azure-portalen.**
    ![bild](common/edit-urls.png)

    Dialogrutan Grundläggande SAML-konfiguration visas.

5.  Kopiera URL:enen från fältet **Mitel Identifier (Entity ID)** i Mitel-kontoportalen och klistra in den i fältet **Identifierare (entitets-ID)** i Azure-portalen.

6. Kopiera URL:en från **url:en för svar (Kontroll konsumenttjänsts URL)** i Mitel-kontoportalen och klistra in den i **url-adressen för svar (Assertion Consumer Service)** i Azure-portalen.  
   ![bild](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. Skriv en av följande webbadresser i textrutan Logga in på **WEBBADRESS:**

    * **https://portal.shoretelsky.com**- att använda Mitel Account portalen som din standard Mitel ansökan
    * **https://teamwork.shoretel.com**- att använda Teamwork som standard Mitel ansökan

    **Standardprogrammet**Mitel är det program som används när en användare klickar på panelen Mitel Connect på åtkomstpanelen. Detta är också det program som används när du gör en testkonfiguration från Azure AD.

8. Klicka på **Spara** i dialogrutan **Grundläggande SAML-konfiguration** i Azure-portalen.

9. I avsnittet **SAML-signeringscertifikat** på den **SAML-baserade inloggningssidan** i Azure-portalen klickar du på **Hämta** **bredvid Certifikat (Base64)** för att hämta **signeringscertifikatet** och spara det på datorn.
    ![bild](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Öppna signeringscertifikatfilen i en textredigerare, kopiera alla data i filen och klistra sedan in data i fältet **Signeringscertifikat** i Mitel-kontoportalen. 
    ![bild](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. Gör följande i avsnittet **Setup Mitel Connect** på den **SAML-baserade inloggningssidan i Azure-portalen:**

    a. Kopiera webbadressen från fältet **Inloggnings-URL** och klistra in den i fältet **Inloggnings-URL i** Mitel-kontoportalen.

    b. Kopiera URL:en från fältet **Azure AD-identifierare** och klistra in den i fältet **Entitets-ID** i Mitel-kontoportalen.
    ![bild](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Klicka på **Spara** i dialogrutan Anslut inställningar **för enkel inloggning** i Mitel-kontoportalen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet ska du skapa en testanvändare med namnet Britta Simon i Azure-portalen.

1. Klicka på **Azure Active Directory**i Azure Active Directory i Azure-portalen, klicka på **Användare**och klicka sedan på **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Klicka på **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Gör följande i dialogrutan Användaregenskaper:

    ![Dialogrutan Användare](common/user-properties.png)

    a. Skriv **BrittaSimon**i fältet **Namn** .
  
    b. Skriv **User name** brittasimon@\<ditt företag i fältet Användarnamn\>. \<förlängning\>.  
Till exempel BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned värdet som visas i rutan **Lösenord.**

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Mitel Connect.

1. Klicka på Enterprise **Applications**i Azure-portalen och klicka sedan på **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Klicka på **Mitel Connect**i programlistan .

    ![Länken Mitel Connect i programlistan](common/all-applications.png)

3. Klicka på Användare och **grupper**på menyn till vänster .

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på **Lägg till användare**och sedan på Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan **Användare** och klickar sedan på **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på **Välj** längst ned på skärmen.

7. Klicka på **Tilldela**i dialogrutan **Lägg till tilldelning** .

### <a name="create-a-mitel-micloud-connect-test-user"></a>Skapa en Mitel MiCloud Connect-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon på ditt MiCloud Connect-konto. Användare måste skapas och aktiveras innan du använder enkel inloggning.

Mer information om hur du lägger till användare i Mitel-kontoportalen finns i artikeln [Lägga till en användare](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) i Mitel Knowledge Base.

Skapa en användare på ditt MiCloud Connect-konto med följande information:

  * **Namn:** Britta Simon

* **E-postadress för företag:**`brittasimon@<yourcompanydomain>.<extension>`   
(Exempel: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))

* **Användarnamn:**`brittasimon@<yourcompanydomain>.<extension>`  
(Exempel: [brittasimon@contoso.com](mailto:brittasimon@contoso.com); användarens användarnamn är vanligtvis detsamma som användarens e-postadress för företag)

**OBS:** Användarens MiCloud Connect-användarnamn måste vara identiskt med användarens e-postadress i Azure.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska du testa din Azure AD-konfiguration med enkel inloggning med hjälp av åtkomstpanelen.

När du klickar på panelen Mitel Connect på åtkomstpanelen bör du automatiskt omdirigeras för att logga in på det MiCloud Connect-program som du konfigurerade som standard i fältet **Logga in på URL.** Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
