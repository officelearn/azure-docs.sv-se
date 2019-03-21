---
title: 'Självstudier: Azure Active Directory-integrering med nämligen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och nämligen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9541d5c4-4c82-4b5b-b01a-6a3f75a2b7a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: 6e3b66f4cb93f0917a3c29b0f337e29dd6c94448
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57844825"
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Självstudier: Azure Active Directory-integrering med nämligen

Lär dig hur du integrerar nämligen med Azure Active Directory (AD Azure) i den här självstudien.
Integrera nämligen med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till nämligen.
* Du kan aktivera att användarna ska automatiskt inloggad till nämligen (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med handlar bl.a, om du behöver följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Nämligen enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för nämligen **SP** -initierad SSO

## <a name="adding-namely-from-the-gallery"></a>Att lägga till nämligen från galleriet

För att konfigurera integrering av nämligen i Azure AD, som du behöver lägga till nämligen från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till nämligen från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **nämligen**väljer **nämligen** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Nämligen i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med nämligen baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i nämligen ska upprättas.

Om du vill konfigurera och testa Azure AD måste enkel inloggning med handlar bl.a, du du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera nämligen enkel inloggning](#configure-namely-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa nämligen testanvändare](#create-namely-test-user)**  – du har en motsvarighet för Britta Simon i nämligen som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med nämligen:

1. I den [Azure-portalen](https://portal.azure.com/)på den **nämligen** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Nämligen domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.namely.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [nämligen klienten supportteamet](https://www.namely.com/contact/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera nämligen** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-namely-single-sign-on"></a>Konfigurera nämligen enkel inloggning

1. I ett nytt webbläsarfönster loggar du in på ditt nämligen företagets plats som administratör.

2. I verktygsfältet högst upp, klickar du på **företagets**.
   
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_06.png) 

3. Klicka på fliken **Settings** (Inställningar).
   
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_07.png) 

4. Klicka på **SAML**.
   
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_08.png) 

5. På den **SAML-inställningar** utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_09.png)
 
    a. Klicka på **Aktivera SAML**. 

    b. I den **SSO-url för identitetsprovider** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.
    
    c. Öppna din nedladdade certifikatet i anteckningar, kopiera innehållet och klistra in den i den **providern identitetscertifikat** textrutan.
     
    d. Klicka på **Spara**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till nämligen.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **nämligen**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **nämligen**.

    ![Den nämligen länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-namely-test-user"></a>Skapa nämligen testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon nämligen.

**Utför följande steg för att skapa en användare som kallas Britta Simon nämligen:**

1. Inloggning till din nämligen företagets plats som administratör.

2. I verktygsfältet högst upp, klickar du på **personer**.
   
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_10.png) 

3. Klicka på den **Directory** fliken.
   
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_11.png) 

4. Klicka på **Lägg till ny Person**.

    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_12.png)

5. På den **Lägg till ny Person** dialogrutan utför följande steg:

    a. I den **Förnamn** textrutan typ **Britta**.

    b. I den **efternamn** textrutan typ **Simon**.

    c. I den **e-post** textrutan skriver den **e-postadress** av BrittaSimon.

    d. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på den panelen nämligen i åtkomstpanelen, du bör vara loggas in automatiskt det nämligen för vilket du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

