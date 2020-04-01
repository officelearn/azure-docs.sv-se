---
title: 'Självstudiekurs: Azure Active Directory-integrering med Marketo | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Marketo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 0488fd1e9bc10d61d6660745acfc8c39becf3a89
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159469"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Självstudiekurs: Azure Active Directory-integrering med Marketo

I den här självstudien får du lära dig hur du integrerar Marketo med Azure Active Directory (Azure AD).
Genom att integrera Marketo med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Marketo.
* Du kan aktivera dina användare automatiskt inloggade på Marketo (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Marketo behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Marketo enda sign-on aktiverat abonnemang

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Marketo stöder **IDP** initierad SSO

## <a name="adding-marketo-from-the-gallery"></a>Lägga till Marketo från galleriet

Om du vill konfigurera integreringen av Marketo i Azure AD måste du lägga till Marketo från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Marketo från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Marketo**i sökrutan och välj **Marketo** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![Marketo i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Marketo baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Marketo upprättas.

Om du vill konfigurera och testa azure AD enkel inloggning med Marketo måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Marketo Single Sign-On](#configure-marketo-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Marketo-testanvändare](#create-marketo-test-user)** – om du vill ha en motsvarighet till Britta Simon i Marketo som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du enkel Azure AD-inloggning med Marketo:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Marketo-programintegration**. **Marketo**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Marketo Domän och webbadresser enkel inloggningsinformation](common/idp-intiated.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://saml.marketo.com/sp`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Marketo Client support team](https://investors.marketo.com/contactus.cfm) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Marketo.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-marketo-single-sign-on"></a>Konfigurera Marketo Single Sign-On

1. Logga in på Marketo med hjälp av administratörsautentiseringsuppgifter och utföra följande åtgärder för att få Munchkin-ID för din ansökan:
   
    a. Logga in på Marketo-appen med administratörsautentiseringsuppgifter.
   
    b. Klicka på knappen **Admin** i det övre navigeringsfönstret.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigera till integrationsmenyn och klicka på **Munchkin-länken**.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Kopiera Munchkin-ID:t som visas på skärmen och fyll i svars-URL:en i konfigurationsguiden för Azure AD.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Så här konfigurerar du SSO i programmet:
   
    a. Logga in på Marketo-appen med administratörsautentiseringsuppgifter.
   
    b. Klicka på knappen **Admin** i det övre navigeringsfönstret.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigera till integrationsmenyn och klicka **på Enkel inloggning**.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Om du vill aktivera SAML-inställningarna klickar du på **Knappen Redigera.**
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Aktiverad** Enkel inloggningsinställningar.
   
    f. Klistra in **Azure AD-identifieraren**i textrutan **Utfärdare-ID.**
   
    g. Ange **Entity ID** URL:en som `http://saml.marketo.com/sp`.
   
    h. Välj elementet Användar-ID som **namnidentifierare**.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Om ditt användaridentifierare inte är UPN-värde ändrar du värdet på fliken Attribut.
   
    i. Ladda upp certifikatet som du har hämtat från Azure AD-konfigurationsguiden. **Spara** inställningarna.
   
    j. Redigera inställningarna för omdirigeringssidor.
   
    k. Klistra in **inloggningsadressen** i textrutan **Inloggningsadress.**
   
    l. Klistra in **url:en för utloggning** i textrutan **Utloggningsadress.**
   
    m. I **fel-URL:en**kopierar du **url:en för Marketo-instansen** och klickar på **Knappen Spara** för att spara inställningar.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Så här aktiverar du SSO för användare:
   
    a. Logga in på Marketo-appen med administratörsautentiseringsuppgifter.
   
    b. Klicka på knappen **Admin** i det övre navigeringsfönstret.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigera till **säkerhetsmenyn** och klicka på **Inloggningsinställningar**.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Markera alternativet **Kräv SSO** och **Spara** inställningarna.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_14.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** skriver **du\@brittasimon yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Marketo.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Marketo**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Marketo**i listan över applikationer .

    ![Marketo-länken i listan Över ansökningar](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-marketo-test-user"></a>Skapa Marketo-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Marketo. följ dessa steg för att skapa en användare i Marketo-plattformen.

1. Logga in på Marketo-appen med administratörsautentiseringsuppgifter.

2. Klicka på knappen **Admin** i det övre navigeringsfönstret.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Navigera till **säkerhetsmenyn** och klicka på **Användare & roller**
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Klicka på länken **Bjud in ny användare** på fliken Användare
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. I guiden Bjud in ny användare fyller du i följande information
   
    a. Ange **användarens e-postadress** i textrutan
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Ange **förnamn** i textrutan
   
    c. Ange **efternamnet** i textrutan
   
    d. Klicka på **Nästa**

6. Markera **userRoles** på fliken **Behörigheter** och klicka på **Nästa**
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Klicka på knappen **Skicka** för att skicka användarbjudan
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Användaren får e-postmeddelandet och måste klicka på länken och ändra lösenordet för att aktivera kontot. 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på marketo-panelen på åtkomstpanelen ska du automatiskt loggas in på den Marketo som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

