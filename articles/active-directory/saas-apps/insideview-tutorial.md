---
title: 'Självstudier: Azure Active Directory-integrering med InsideView | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: 91e1780462021430913a1354b4add7c5f047ec15
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263187"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Självstudier: Azure Active Directory-integrering med InsideView

I den här självstudien får du lära dig hur du integrerar InsideView med Azure Active Directory (AD Azure).
Integrera InsideView med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till InsideView.
* Du kan aktivera användarna att vara automatiskt inloggad till InsideView (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med InsideView, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* InsideView enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för InsideView **IDP** -initierad SSO

## <a name="adding-insideview-from-the-gallery"></a>Att lägga till InsideView från galleriet

För att konfigurera integrering av InsideView i Azure AD, som du behöver lägga till InsideView från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till InsideView från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **InsideView**väljer **InsideView** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![InsideView i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med InsideView baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i InsideView upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med InsideView, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera InsideView Single Sign-On](#configure-insideview-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare InsideView](#create-insideview-test-user)**  – du har en motsvarighet för Britta Simon i InsideView som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med InsideView:

1. I den [Azure-portalen](https://portal.azure.com/)på den **InsideView** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![InsideView domän och URL: er med enkel inloggning för information](common/idp-reply.png)

    I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [InsideView klienten supportteamet](mailto:support@insideview.com) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. På den **konfigurera InsideView** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-insideview-single-sign-on"></a>Konfigurera InsideView Single Sign-On

1. I ett annat webbläsarfönster, loggar du in din InsideView företagets webbplats som administratör.

1. I verktygsfältet högst upp, klickar du på **Admin**, **SingleSignOn inställningar**, och klicka sedan på **lägga till SAML**.
   
   ![SAML enkel inloggning inställningar](./media/insideview-tutorial/ic794135.png "SAML enkel inloggning inställningar")

1. I den **lägga till en ny SAML** avsnittet, utför följande steg:

    ![Lägg till en ny SAML](./media/insideview-tutorial/ic794136.png "lägga till en ny SAML")

    a. I den **STS Name** textrutan anger du ett namn för din konfiguration.

    b. I **SamlP/WS-Fed oombedda EndPoint** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    c. Öppna Base64-kodade certifikatet, som du har hämtat från Azure-portalen, kopiera innehållet i den till Urklipp och klistra in den till den **STS Certificate** textrutan.

    d. I den **Crm användarmappning för Id** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. I den **Crm e-mappning** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. I den **Crm förnamn mappning** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    g. I den **Crm lastName mappning** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.  

    h. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till InsideView.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **InsideView**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **InsideView**.

    ![Länken InsideView i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-insideview-test-user"></a>Skapa InsideView testanvändare

Om du vill aktivera Azure AD-användare att logga in på InsideView, måste de vara etablerade i att InsideView. När det gäller InsideView är etablering en manuell aktivitet.

För att få användare eller kontakter som skapas i InsideView kan kontakta [InsideView supportteamet](mailto:support@insideview.com).

> [!NOTE]
> Du kan använda alla andra InsideView användare konto verktyg för att skapa eller API: er som tillhandahålls av InsideView att etablera användarkonton i Azure AD.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen InsideView i åtkomstpanelen, bör det vara loggas in automatiskt till InsideView som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
