---
title: 'Självstudiekurs: Azure Active Directory-integrering med FirmPlay – Medarbetarpåverkan för rekrytering | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FirmPlay - Employee Advocacy för rekrytering.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 2bdc3a13582f079cc9325ef1c6949c3ae10138eb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102527"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Självstudiekurs: Azure Active Directory-integrering med FirmPlay - Medarbetares opinionsbildning för rekrytering

I den här självstudien får du lära dig hur du integrerar FirmPlay – Employee Advocacy för rekrytering med Azure Active Directory (Azure AD).
Genom att integrera FirmPlay – Medarbetares opinionsbildning för rekrytering med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till FirmPlay - Employee Advocacy för rekrytering.
* Du kan aktivera dina användare automatiskt inloggade på FirmPlay - Employee Advocacy for Recruiting (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med FirmPlay – Employee Advocacy for Recruiting behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* FirmPlay - Medarbetares opinionsbildning för att rekrytera enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* FirmPlay - Employee Advocacy för rekrytering stöder **SP** initierade SSO

## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Lägga FirmPlay - Anställd Advocacy för rekrytering från galleriet

Om du vill konfigurera integreringen av FirmPlay – Employee Advocacy for Recruiting into Azure AD måste du lägga till FirmPlay - Employee Advocacy for Recruiting from the gallery till din lista över hanterade SaaS-appar.

**Så här lägger du till FirmPlay - Employee Advocacy for Recruiting från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **FirmPlay - Employee Advocacy for Recruiting**, select **FirmPlay - Employee Advocacy for Recruiting** from result panel och klickar sedan på **Lägg** till knappen för att lägga till programmet.

     ![FirmPlay - Medarbetares opinionsbildning för rekrytering i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med FirmPlay - Employee Advocacy for Recruiting baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i FirmPlay - Employee Advocacy for Recruiting upprättas.

Om du vill konfigurera och testa en enda Azure AD-inloggning med FirmPlay – Employee Advocacy for Recruiting måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera FirmPlay - Medarbetares opinionsbildning för att rekrytera enkel inloggning](#configure-firmplay---employee-advocacy-for-recruiting-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa FirmPlay - Medarbetares opinionsbildning för att rekrytera testanvändare](#create-firmplay---employee-advocacy-for-recruiting-test-user)** - om du vill ha en motsvarighet till Britta Simon i FirmPlay - Employee Advocacy for Recruiting som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du enkel Azure AD-inloggning med FirmPlay – Employee Advocacy for Recruiting:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **FirmPlay - Employee Advocacy för rekrytering av** **programintegrering**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![FirmPlay - Medarbetare Advocacy för rekrytering domän och webbadresser enda inloggningsinformation](common/sp-signonurl.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<your-subdomain>.firmplay.com/`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [FirmPlay - Employee Advocacy för att rekrytera kundsupportteam för](mailto:engineering@firmplay.com) att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera FirmPlay - Medarbetares påverkanssättning för rekrytering.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-firmplay---employee-advocacy-for-recruiting-single-sign-on"></a>Konfigurera FirmPlay - Medarbetares opinionsbildning för att rekrytera enkel inloggning

Om du vill konfigurera enkel inloggning på **FirmPlay - Employee Advocacy för rekryteringssidan** måste du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade url:er från Azure-portalen till [FirmPlay - Employee Advocacy för rekryteringssupportteam](mailto:engineering@firmplay.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till FirmPlay - Employee Advocacy för rekrytering.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **FirmPlay - Employee Advocacy för rekrytering**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **FirmPlay - Employee Advocacy för rekrytering**.

    ![FirmPlay - Medarbetare Advocacy för rekrytering länk i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-firmplay---employee-advocacy-for-recruiting-test-user"></a>Skapa FirmPlay - Medarbetares opinionsbildning för att rekrytera testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i FirmPlay - Employee Advocacy for Recruiting. Arbeta med [FirmPlay - Employee Advocacy för att rekrytera supportteam för](mailto:engineering@firmplay.com) att lägga till användarna i FirmPlay - Employee Advocacy for Recruiting-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen FirmPlay - Medarbetare för rekrytering i åtkomstpanelen ska du automatiskt loggas in på firmplay - medarbetares opinionsbildning för rekrytering som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

