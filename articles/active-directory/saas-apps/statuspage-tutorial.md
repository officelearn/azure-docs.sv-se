---
title: 'Självstudiekurs: Azure Active Directory-integrering med StatusPage | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och StatusPage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: d947f610e6a753ce2ed349917640b07a55bbb735
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67089876"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Självstudiekurs: Azure Active Directory-integrering med StatusPage

I den här självstudien får du lära dig hur du integrerar StatusPage med Azure Active Directory (Azure AD).
Genom att integrera StatusPage med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till StatusPage.
* Du kan aktivera användarna automatiskt inloggade på StatusPage (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med StatusPage behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* StatusPage enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* StatusPage stöder **IDP-initierad** SSO

## <a name="adding-statuspage-from-the-gallery"></a>Lägga till StatusPage från galleriet

Om du vill konfigurera integreringen av StatusPage i Azure AD måste du lägga till StatusPage från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till StatusPage från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **StatusPage**i sökrutan och välj **StatusPage** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

    ![StatusSida i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med StatusPage baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i StatusPage upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med StatusPage måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Enstaka inloggning för StatusPage](#configure-statuspage-single-sign-on)** – om du vill konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Testanvändare](#create-statuspage-test-user)** för StatusPage – om du vill ha en motsvarighet till Britta Simon i StatusPage som är länkad till Azure AD-representationen för användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med StatusPage:

1. Välj Enkel inloggning på sidan StatusPage-programintegrering i **StatusPage** [Azure-portalen](https://portal.azure.com/). **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![StatusPage-domän och webbadresser med enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/`|
    | `https://<subdomain>.statuspage.io/`|

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume`|
    | `https://<subdomain>.statuspage.io/sso/saml/consume`|

    > [!NOTE]
    > Kontakta Supportteamet för [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)StatusPage för att begära metadata som krävs för att konfigurera enkel inloggning. 
    >
    > a. Från metadata kopierar du utfärdarvärdet och klistrar sedan in det i textrutan **Identifierare.**
    >
    > b. Från metadata kopierar du svars-URL:en och klistrar sedan in den i textrutan **Svara på URL.**

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera StatusPage.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-statuspage-single-sign-on"></a>Konfigurera enstaka inloggning för StatusPage

1. Logga in på webbplatsen StatusPage som administratör i ett annat webbläsarfönster.

1. Klicka på Hantera **konto**i huvudverktygsfältet .

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klicka på fliken **Enkel inloggning.**

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Gör följande på sidan SSO-installationsprogrammet:

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. Klistra in värdet **för inloggnings-URL**i textrutan **SSO-mål-URL** som du har kopierat från Azure-portalen.

    b. Öppna det nedladdade certifikatet i Anteckningar, kopiera innehållet och klistra sedan in det i **textrutan Certifikat.**

    c. Klicka på **SPARA KONFIGURATION**.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till StatusPage.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **StatusPage**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **StatusPage**i programlistan .

    ![Länken StatusPage i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-statuspage-test-user"></a>Skapa Testanvändare för StatusPage

Syftet med det här avsnittet är att skapa en användare som heter Britta Simon i StatusPage.

StatusPage stöder just-in-time-etablering. Du har redan aktiverat den i [Konfigurera Azure AD Single Sign-On](#configure-azure-ad-single-sign-on).

**Så här skapar du en användare som heter Britta Simon i StatusPage:**

1. Logga in på webbplatsen StatusPage som administratör.

1. Klicka på **Hantera konto**högst upp.

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klicka på fliken **Gruppmedlemmar.**
  
    ![Skapa en testanvändare för Azure AD](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Klicka på **LÄGG TILL GRUPPMEDLEM**.
  
    ![Skapa en testanvändare för Azure AD](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Skriv **e-postadress,** **förnamn**och **efternamn för** en giltig användare som du vill etablera i relaterade textrutor. 

    ![Skapa en testanvändare för Azure AD](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Som **roll**väljer du **Klientadministratör**.

1. Klicka på **SKAPA KONTO**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen StatusPage på Åtkomstpanelen bör du automatiskt loggas in på den StatusSida som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
