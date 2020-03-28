---
title: 'Självstudiekurs: Azure Active Directory-integrering med Wizergos Produktivitetsprogram | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Wizergos Productivity Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: acc04396-13c5-4c24-ab9a-30fbc9234ebd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: b6491013cb35f2473eff6c2019fe2a80dd9e9b08
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086985"
---
# <a name="tutorial-azure-active-directory-integration-with-wizergos-productivity-software"></a>Självstudiekurs: Azure Active Directory-integrering med Wizergos Produktivitetsprogram

I den här självstudien får du lära dig hur du integrerar Wizergos Produktivitetsprogramvara med Azure Active Directory (Azure AD).
Genom att integrera Wizergos produktivitetsprogramvara med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Wizergos Produktivitetsprogramvara.
* Du kan aktivera dina användare automatiskt inloggad på Wizergos Productivity Software (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Wizergos Produktivitetsprogram behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Wizergos Productivity Software enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Wizergos Productivity Software stöder **IDP** initierad SSO

## <a name="adding-wizergos-productivity-software-from-the-gallery"></a>Lägga till Wizergos Produktivitetsprogram från galleriet

Om du vill konfigurera integreringen av Wizergos Produktivitetsprogramvara i Azure AD måste du lägga till Wizergos Produktivitetsprogramvara från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Wizergos Produktivitetsprogram från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Wizergos Productivity Software**, väljer **Wizergos Productivity Software** från resultatpanelen och klickar sedan på Lägg **till** knappen för att lägga till programmet.

     ![Wizergos Produktivitet Software i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Wizergos Productivity Software baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Wizergos Produktivitetsprogram upprättas.

Om du vill konfigurera och testa azure AD-enkel inloggning med Wizergos Productivity Software måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Wizergos Productivity Software Single Sign-On](#configure-wizergos-productivity-software-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Wizergos Productivity Software testanvändare](#create-wizergos-productivity-software-test-user)** - att ha en motsvarighet till Britta Simon i Wizergos Produktivitetsprogramvara som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du azure AD-enkel inloggning med Wizergos Productivity Software:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Wizergos Productivity** **Software-programintegration**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Wizergos Produktivitet Software Domain och webbadresser enkel inloggningsinformation](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du en URL: `https://www.wizergos.net`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Wizergos-produktivitetsprogram.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-wizergos-productivity-software-single-sign-on"></a>Konfigurera Wizergos ProduktivitetSprogram enkel inloggning

1. I ett annat webbläsarfönster loggar du in på wizergos-klienten för produktivitetsprogram som administratör.

2. Välj **Admin**på hamburgermenyn .

    ![Konfigurera enkel inloggning på appsidan](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)

3. På admin-sidan på menyn till vänster väljer du **AUTENTISERING** och klickar på **Azure AD**.

    ![Konfigurera enkel inloggning på appsidan](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)

4. Utför följande steg i avsnittet **AUTENTISERING.**

    ![Konfigurera enkel inloggning på appsidan](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)
    
    a. Klicka på **KNAPPEN LADDA** UPP om du vill ladda upp det hämtade certifikatet från Azure AD.
    
    b. I textrutan **Utfärdare-URL** klistrar du in **azure AD-identifierare** som du har kopierat från Azure-portalen.
    
    c. I textrutan **För enkel inloggnings-URL** klistrar du in värdet **för inloggnings-URL** som du har kopierat från Azure-portalen.
    
    d. I textrutan FÖR **enkel utloggning av WEBBADRESS** klistrar du in **url-värdet för utloggning** som du har kopierat från Azure-portalen.
    
    e. Klicka på knappen **Spara**.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Wizergos Produktivitetsprogramvara.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Wizergos Produktivitetsprogram**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Wizergos Produktivitetsprogram**i programlistan .

    ![Wizergos produktivitetsprogram länk i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-wizergos-productivity-software-test-user"></a>Skapa Wizergos Productivity Software testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Wizergos Productivity Software. Arbeta med [Wizergos Supportteam för produktivitetsprogram](mailTo:support@wizergos.com) för att lägga till användarna i Wizergos produktivitetsprogrammjukvaraplattform.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Wizergos Productivity Software på åtkomstpanelen bör du automatiskt loggas in på Wizergos Produktivitetsprogramvara som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

