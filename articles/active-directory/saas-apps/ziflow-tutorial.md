---
title: 'Självstudier: Azure Active Directory-integrering med Ziflow | Microsoft Docs'
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
ms.openlocfilehash: 02c09744130bec55eed4181b0d4ba958aec59e69
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920552"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Självstudier: Azure Active Directory-integrering med Ziflow

I den här självstudien får du lära dig hur du integrerar Ziflow med Azure Active Directory (AD Azure).
Integrera Ziflow med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Ziflow.
* Du kan aktivera användarna att vara automatiskt inloggad till Ziflow (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Ziflow, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Ziflow enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Ziflow **SP** -initierad SSO

## <a name="adding-ziflow-from-the-gallery"></a>Att lägga till Ziflow från galleriet

För att konfigurera integrering av Ziflow i Azure AD, som du behöver lägga till Ziflow från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Ziflow från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Ziflow**väljer **Ziflow** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Ziflow i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Ziflow baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Ziflow upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Ziflow, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Ziflow Single Sign-On](#configure-ziflow-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Ziflow](#create-ziflow-test-user)**  – du har en motsvarighet för Britta Simon i Ziflow som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Ziflow:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Ziflow** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Ziflow domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Föregående värden är inte verkliga. Du uppdaterar det unika ID-värdet i identifierare och URL: en inloggning med faktiskt värde, som beskrivs senare i självstudien.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera Ziflow** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-ziflow-single-sign-on"></a>Konfigurera Ziflow Single Sign-On

1. I ett annat webbläsarfönster, loggar du in Ziflow som en administratör.

2. Klicka på Avatar i övre högra hörnet och klicka sedan på **Hantera konto**.

    ![Hantera Ziflow konfiguration](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. Längst upp till vänster klickar du på **enkel inloggning**.

    ![Ziflow Configuration inloggning](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. På sidan **Enkel inloggning** utför du följande steg:

    ![Ziflow Configuration enskild](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Välj **typ** som **SAML2.0**.

    b. I den **logga i URL: en** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    c. Ladda upp det Base64-kodade certifikatet som du har hämtat från Azure-portalen till den **X509 signeringscertifikat**.

    d. I den **Utloggning** textrutan klistra in värdet för **URL för utloggning**, som du har kopierat från Azure-portalen.

    e. Från den **konfigurationsinställningar för leverantören identifierare** avsnittet, kopiera det markerade unika ID-värdet och lägger till dem med identifierare och logga in på URL: en i den **SAML grundkonfiguration** på Azure portalen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Ziflow.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Ziflow**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Ziflow**.

    ![Länken Ziflow i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-ziflow-test-user"></a>Skapa Ziflow testanvändare

Om du vill aktivera Azure AD-användare att logga in på Ziflow, måste de etableras i Ziflow. I Ziflow är etablering en manuell aktivitet.

Gör följande för att etablera ett användarkonto:

1. Logga in på Ziflow som en administratör.

2. Gå till **personer** längst upp.

    ![Ziflow Configuration personer](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Klicka på **Lägg till** och klicka sedan på **Lägg till användare**.

    ![Ziflow konfiguration att lägga till användaren](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. På den **lägga till en användare** popup-fönstret utför följande steg:

    ![Ziflow konfiguration att lägga till användaren](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. I **e-post** text, ange den e-postadressen för användaren som brittasimon@contoso.com.

    b. I **Förnamn** text, ange först namnet på användaren som Britta.

    c. I **efternamn** text anger efternamn för användaren som Simon.

    d. Välj din Ziflow roll.

    e. Klicka på **Lägg till 1 användare**.

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Ziflow i åtkomstpanelen, bör det vara loggas in automatiskt till Ziflow som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

