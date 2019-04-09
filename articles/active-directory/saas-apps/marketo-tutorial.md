---
title: 'Självstudier: Azure Active Directory-integrering med Marketo | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Marketo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 09f452a0971e2a0e74e51edd2db44eecda39c204
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265771"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Självstudier: Azure Active Directory-integrering med Marketo

I den här självstudien får du lära dig hur du integrerar Marketo med Azure Active Directory (AD Azure).
Integrera Marketo med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Marketo.
* Du kan aktivera användarna att vara automatiskt inloggad till Marketo (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Marketo, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Marketo enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Marketo **IDP** -initierad SSO

## <a name="adding-marketo-from-the-gallery"></a>Att lägga till Marketo från galleriet

För att konfigurera integrering av Marketo i Azure AD, som du behöver lägga till Marketo från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Marketo från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Marketo**väljer **Marketo** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Marketo i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Marketo baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Marketo upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Marketo, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Marketo Single Sign-On](#configure-marketo-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Marketo testanvändare](#create-marketo-test-user)**  – du har en motsvarighet för Britta Simon i Marketo som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Marketo:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Marketo** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Marketo-domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://saml.marketo.com/sp`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Marketo-klienten supportteamet](http://investors.marketo.com/contactus.cfm) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera Marketo** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-marketo-single-sign-on"></a>Konfigurera Marketo Single Sign-On

1. Logga in till Marketo med administratörsautentiseringsuppgifter för att få Munchkin ID: T för ditt program, och utföra följande åtgärder:
   
    a. Logga in på Marketo-app med hjälp av autentiseringsuppgifter som administratör.
   
    b. Klicka på den **Admin** knappen i det övre navigeringsfönstret.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Gå till menyn integrering och klicka på den **Munchkin länk**.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Kopiera Munchkin-Id som visas på skärmen och slutför svars-URL i guiden för konfiguration av Azure AD.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Så här konfigurerar du SSO i programmet i stegen nedan:
   
    a. Logga in på Marketo-app med hjälp av autentiseringsuppgifter som administratör.
   
    b. Klicka på den **Admin** knappen i det övre navigeringsfönstret.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Gå till menyn integrering och klicka på **Single Sign On**.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Om du vill aktivera SAML-inställningar klickar du på **redigera** knappen.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Aktiverad** inställningar för enkel inloggning.
   
    f. Klistra in den **Azure AD-identifierare**i den **utfärdar-ID** textrutan.
   
    g. I den **entitets-ID** textrutan anger du Webbadressen som `http://saml.marketo.com/sp`.
   
    h. Välj den plats som ID **namnidentifierare elementet**.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Om ditt användar-ID inte är UPN-värdet och sedan ändra värdet i fliken attribut.
   
    i. Ladda upp certifikatet som du har laddat ned från Azure AD-konfigurationsguiden. **Spara** inställningarna.
   
    j. Redigera inställningar för omdirigera felsidor.
   
    k. Klistra in den **inloggnings-URL** i den **inloggnings-URL** textrutan.
   
    l. Klistra in den **URL för utloggning** i den **URL för utloggning** textrutan.
   
    m. I den **fel URL**, kopiera din **Marketo instans-URL** och klicka på **spara** för att spara inställningarna.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Om du vill aktivera enkel inloggning för användare att utföra följande åtgärder:
   
    a. Logga in på Marketo-app med hjälp av autentiseringsuppgifter som administratör.
   
    b. Klicka på den **Admin** knappen i det övre navigeringsfönstret.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigera till den **Security** menyn och klickar på **inloggningsinställningar**.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Kontrollera den **kräver SSO** alternativet och **spara** inställningarna.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_14.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Marketo.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Marketo**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Marketo**.

    ![Marketo-länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-marketo-test-user"></a>Skapa Marketo testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Marketo. Följ dessa steg om du vill skapa en användare i Marketo-plattformen.

1. Logga in på Marketo-app med hjälp av autentiseringsuppgifter som administratör.

2. Klicka på den **Admin** knappen i det övre navigeringsfönstret.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Navigera till den **Security** menyn och klickar på **användare och roller**
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Klicka på den **Bjud in ny användare** länken på fliken användare
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. I guiden bjuda in nya användare fyller du följande information
   
    a. Ange användaren **e-post** adressen i textrutan
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Ange den **Förnamn** i textrutan
   
    c. Ange den **efternamn** i textrutan
   
    d. Klicka på **Nästa**

6. I den **behörigheter** fliken den **userRoles** och klicka på **nästa**
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Klicka på den **skicka** knappen för att skicka användarinbjudan
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Användaren tar emot e-postmeddelandet och klicka på länken och ändra lösenordet för att aktivera kontot. 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Marketo i åtkomstpanelen, bör det vara loggas in automatiskt till Marketo som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

