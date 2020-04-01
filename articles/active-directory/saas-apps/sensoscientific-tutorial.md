---
title: 'Självstudiekurs: Azure Active Directory-integrering med SensoScientific Wireless Temperature Monitoring System | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SensoScientific Wireless Temperature Monitoring System.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea2f5e33859852388357526052c39fa432471efb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091270"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Självstudiekurs: Azure Active Directory-integrering med SensoScientific Wireless Temperature Monitoring System

I den här självstudien får du lära dig hur du integrerar SensoScientific Wireless Temperature Monitoring System med Azure Active Directory (Azure AD).
Genom att integrera SensoScientific Wireless Temperature Monitoring System med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SensoScientific Wireless Temperature Monitoring System.
* Du kan aktivera dina användare automatiskt inloggad på SensoScientific Wireless Temperature Monitoring System (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med SensoScientific Wireless Temperature Monitoring System behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* SensoScientific Wireless Temperature Monitoring System enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SensoScientific Wireless Temperature Monitoring System stöder **IDP** initierad SSO

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Lägga till SensoScientific wireless temperature monitoring system från galleriet

Om du vill konfigurera integreringen av SensoScientific Wireless Temperature Monitoring System i Azure AD måste du lägga till SensoScientific Wireless Temperature Monitoring System från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till SensoScientific Wireless Temperature Monitoring System från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **SensoScientific Wireless Temperature Monitoring System**, välj **SensoScientific Wireless Temperature Monitoring System** från resultatpanelen och klicka sedan på **Lägg** till knappen för att lägga till programmet.

    ![SensoScientific trådlös temperatur övervakningssystem i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med SensoScientific Wireless Temperature Monitoring System baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SensoScientific Wireless Temperature Monitoring System upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med SensoScientific Wireless Temperature Monitoring System måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SensoScientific Wireless Temperature Monitoring System Single Sign-On](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SensoScientific Wireless Temperature Monitoring System testanvändare](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)** - att ha en motsvarighet till Britta Simon i SensoScientific wireless temperature monitoring system som är kopplat till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med SensoScientific Wireless Temperature Monitoring System:

1. I [Azure-portalen](https://portal.azure.com/)väljer du **Enkel inloggning**på sidan **AvsoScientific Wireless Temperature Monitoring System** application integration .

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![SensoScientific Wireless Temperature Monitoring System Domain och WEBBADRESSer enkel inloggningsinformation](common/preintegrated.png)

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina behov i avsnittet **Konfigurera SensoScientific Wireless Temperature Monitoring System.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>Konfigurera SensoScientific trådlös temperatur övervakningssystem enda sign-on

1. Logga in på ditt SensoScientific Wireless Temperature Monitoring System-program som administratör.

1. I navigeringsmenyn högst upp klickar du på **Konfiguration** och gå **till Konfigurera** under Enkel **inloggning** för att öppna inställningarna för enkel inloggning och utför följande steg:

    ![Konfigurera enkel inloggning](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. Välj **Utfärdarnamn** som Azure AD.

    b. I textrutan **Utfärdare URL** klistrar du in **Azure AD-identifieraren** som du har kopierat från Azure-portalen.

    c. Klistra in **inloggningsadressen** som du har kopierat från Azure-portalen i **textrutan För tjänsten Enkel inloggningstjänst.**

    d. I **textrutan URL för enkel utloggningstjänst** klistrar du in **url:en för utloggning** som du har kopierat från Azure-portalen.

    e. Bläddra bland certifikatet som du har hämtat från Azure Portal och ladda upp här.

    f. Klicka på **Spara**.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till SensoScientific Wireless Temperature Monitoring System.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **SensoScientific Wireless Temperature Monitoring System**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **SensoScientific Wireless Temperature Monitoring System**i programlistan .

    ![Länken SensoScientific Wireless Temperature Monitoring System i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Skapa testanvändare för test av SensoScientific Wireless Temperature Monitoring System

Om du vill att Azure AD-användare ska kunna logga in på SensoScientific Wireless Temperature Monitoring System måste de etableras i SensoScientific Wireless Temperature Monitoring System. Arbeta med [SensoScientific Wireless Temperature Monitoring System supportteam](https://www.sensoscientific.com/contact-us/) för att lägga till användare i SensoScientific Wireless Temperature Monitoring System plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SensoScientific Wireless Temperature Monitoring System i åtkomstpanelen bör du automatiskt loggas in på det sensoscientific trådlösa temperaturövervakningssystem som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

