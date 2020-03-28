---
title: 'Självstudiekurs: Azure Active Directory-integrering med Ziflow | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: d9745bdb1cb6de86a96946564865958433d49732
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086200"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Självstudiekurs: Azure Active Directory-integrering med Ziflow

I den här självstudien får du lära dig hur du integrerar Ziflow med Azure Active Directory (Azure AD).
Genom att integrera Ziflow med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Ziflow.
* Du kan aktivera dina användare så att de automatiskt loggas in på Ziflow (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Ziflow behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Ziflow enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Ziflow stödjer **SP** initierade SSO

## <a name="adding-ziflow-from-the-gallery"></a>Lägga ziflow från galleriet

Om du vill konfigurera integreringen av Ziflow i Azure AD måste du lägga till Ziflow från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Ziflow från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Ziflow**i sökrutan och välj **Ziflow** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![Ziflow i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Ziflow baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Ziflow upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Ziflow måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Ziflow Single Sign-On](#configure-ziflow-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Ziflow-testanvändare](#create-ziflow-test-user)** - om du vill ha en motsvarighet till Britta Simon i Ziflow som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Ziflow:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Ziflow-programintegration**. **Ziflow**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Ziflow Domän och webbadresser enkel inloggningsinformation](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Föregående värden är inte verkliga. Du uppdaterar det unika ID-värdet i identifieraren och signerar url:en med verkligt värde, vilket förklaras senare i självstudien.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Ziflow.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-ziflow-single-sign-on"></a>Konfigurera En inloggning i Ziflow

1. I ett annat webbläsarfönster loggar du in på Ziflow som säkerhetsadministratör.

2. Klicka på Avatar i det övre högra hörnet och klicka sedan på **Hantera konto**.

    ![Hantering av Ziflow-konfiguration](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. Klicka på Enkel **inloggning**längst upp till vänster .

    ![Konfigurationstecken för Ziflow](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. På sidan **Enkel inloggning** utför du följande steg:

    ![Ziflow Konfiguration Single](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Välj **Typ** som **SAML2.0**.

    b. Klistra in värdet för **inloggnings-URL i** textrutan Logga in, som du har kopierat från Azure-portalen. **Login URL**

    c. Ladda upp det kodade bas64-certifikatet som du har hämtat från Azure-portalen till **X509-signeringscertifikatet**.

    d. Klistra in värdet för url för **utloggning**i textrutan **Logga** ut , som du har kopierat från Azure-portalen.

    e. Från **avsnittet Konfigurationsinställningar för identifierare providern kopierar** du det markerade unika ID-värdet och lägger till det med identifieraren och inloggnings-URL:en i **den grundläggande SAML-konfigurationen** på Azure-portalen.

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

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Ziflow.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Ziflow**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Ziflow**i programlistan .

    ![Ziflow-länken i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-ziflow-test-user"></a>Skapa Ziflow-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på Ziflow måste de etableras i Ziflow. I Ziflow är etablering en manuell aktivitet.

Gör följande för att etablera ett användarkonto:

1. Logga in på Ziflow som säkerhetsadministratör.

2. Navigera till **Personer** högst upp.

    ![Ziflow Konfiguration människor](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Klicka på **Lägg till** och sedan på Lägg **till användare**.

    ![Ziflow-konfiguration lägger till användare](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. Gör följande på **popup-fönstret Lägg till en användare:**

    ![Ziflow-konfiguration lägger till användare](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. I textrutan **E-post** anger du användarens e-postadress som brittasimon@contoso.com.

    b. I textrutan **Förnamn** anger du förnamnet på användare som Britta.

    c. I textrutan **Efternamn** anger du efternamnet för användaren som Simon.

    d. Välj din Ziflow-roll.

    e. Klicka på **Lägg till 1 användare**.

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Ziflow-panelen i åtkomstpanelen ska du automatiskt loggas in på den Ziflow som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

