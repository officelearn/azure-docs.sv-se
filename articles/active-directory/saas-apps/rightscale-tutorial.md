---
title: 'Självstudiekurs: Azure Active Directory-integrering med högerskala | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Rightscale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3a8d376d-95fb-4dd7-832a-4fdd4dd7c87c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 53799b62da043b7680f010e1eaaf0d9243f07dd5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093074"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Självstudiekurs: Azure Active Directory-integrering med högerskala

I den här självstudien får du lära dig hur du integrerar Rightscale med Azure Active Directory (Azure AD).
Genom att integrera rättskala med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Rightscale.
* Du kan aktivera dina användare så att de automatiskt loggas in på Rightscale (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Rightscale behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Rättskala enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Högerskala stöder **SP och IDP** initierad SSO

## <a name="adding-rightscale-from-the-gallery"></a>Lägga till högerskala från galleriet

Om du vill konfigurera integreringen av Rightscale i Azure AD måste du lägga till Högerskala från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Högerskala från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Högerskala**i sökrutan och välj **Högerskala** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![Högerskala i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Högerskala baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Rightscale upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Högerskala måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för högerskala](#configure-rightscale-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa rättskala testanvändare](#create-rightscale-test-user)** - att ha en motsvarighet till Britta Simon i högerskala som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Högerskala:

1. I [Azure-portalen](https://portal.azure.com/)väljer du **Enkel inloggning på**sidan Integrering av program för **högerskala** .

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![Enkel inloggningsinformation för domäner och webbadresser som har rättskala](common/preintegrated.png)

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Enkel inloggningsinformation för domäner och webbadresser som har rättskala](common/metadata-upload-additional-signon.png)

    I rutan **Inloggnings-URL** anger du följande URL: `https://login.rightscale.com/`

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera rättskala.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-rightscale-single-sign-on"></a>Konfigurera enkel inloggning för högerskala

1. Om du vill konfigurera SSO för ditt program måste du logga in på din RightScale-klientorganisation som administratör.

2. Klicka på fliken **Inställningar** högst upp och välj **Enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_001.png)

3. Klicka på den **nya** knappen om du vill lägga till **dina SAML-identitetsleverantörer**.

    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_002.png)

4. Mata in företagsnamnet i textrutan **med visningsnamn.**

    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_003.png)

5. Välj **Tillåt Högerskala initierad SSO med hjälp av en identifieringsledtråd** och mata in ditt **domännamn** i textrutan nedan.

    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_004.png)

6. Klistra in värdet **för inloggnings-URL** som du har kopierat från Azure-portalen till **SAML SSO-slutpunkten** i RightScale.

    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_006.png)

7. Klistra in värdet för **Azure AD-identifierare** som du har kopierat från Azure-portalen till **SAML EntityID** i RightScale.

    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_008.png)

8. Klicka på **knappen Webbläsare** om du vill ladda upp certifikatet som du hämtade från Azure-portalen.


    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_009.png)

9. Klicka på **Spara**.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Rightscale.

1. I Azure-portalen väljer du **Företagsprogram**, väljer **Alla program**och väljer sedan **Högerskala**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Högerskala**i programlistan .

    ![Länken Högerskala i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-rightscale-test-user"></a>Skapa testanvändare för rättskala

I det här avsnittet skapar du en användare som heter Britta Simon i Högerskala. Arbeta med [supportteamet](mailto:support@rightscale.com) för klient i rätt skala för att lägga till användarna i högerskalans plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Högerskala på åtkomstpanelen bör du automatiskt loggas in på den högerskala som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
