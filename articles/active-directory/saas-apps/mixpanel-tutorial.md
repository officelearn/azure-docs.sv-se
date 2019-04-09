---
title: 'Självstudier: Azure Active Directory-integrering med Mixpanel | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mixpanel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a2df26ef-d441-44ac-a9f3-b37bf9709bcb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/28/2019
ms.author: jeedes
ms.openlocfilehash: 7fa4c78269464b1a38507acd6512e5f44ed94945
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59267675"
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>Självstudier: Azure Active Directory-integrering med Mixpanel

I den här självstudien får du lära dig hur du integrerar Mixpanel med Azure Active Directory (AD Azure).
Integrera Mixpanel med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Mixpanel.
* Du kan aktivera användarna att vara automatiskt inloggad till Mixpanel (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Mixpanel, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Mixpanel enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Mixpanel **SP** -initierad SSO

## <a name="adding-mixpanel-from-the-gallery"></a>Att lägga till Mixpanel från galleriet

För att konfigurera integrering av Mixpanel i Azure AD, som du behöver lägga till Mixpanel från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Mixpanel från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Mixpanel**väljer **Mixpanel** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Mixpanel i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Mixpanel baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Mixpanel upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Mixpanel, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Mixpanel Single Sign-On](#configure-mixpanel-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare i Mixpanel](#create-mixpanel-test-user)**  – du har en motsvarighet för Britta Simon i Mixpanel som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Mixpanel:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Mixpanel** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Mixpanel domän och URL: er med enkel inloggning för information](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://mixpanel.com/login/`

    > [!NOTE]
    > Registrera dig på [ https://mixpanel.com/register/ ](https://mixpanel.com/register/) att ställa in dina inloggningsuppgifter och kontakta den [Mixpanel supportteamet](mailto:support@mixpanel.com) att aktivera SSO-inställningar för din klient. Du kan också få logga på URL-värdet om det behövs från din Mixpanel supportteam. 

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera Mixpanel** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-mixpanel-single-sign-on"></a>Konfigurera Mixpanel Single Sign-On

1. I ett annat webbläsarfönster inloggning till Mixpanel-programmet som en administratör.

2. Längst ned på sidan, klicka på den lilla **kugghjulsikonen** i det vänstra hörnet. 
   
    ![Mixpanel Single Sign-On](./media/mixpanel-tutorial/tutorial_mixpanel_06.png) 

3. Klicka på den **programåtkomst** fliken och klicka sedan på **ändra inställningarna för**.
   
    ![Mixpanel inställningar](./media/mixpanel-tutorial/tutorial_mixpanel_08.png) 

4. På den **ändra certifikatet** dialogrutan sidan, klickar du på **Välj fil** ladda upp nedladdade certifikatet och klicka sedan på **nästa**.
   
    ![Mixpanel inställningar](./media/mixpanel-tutorial/tutorial_mixpanel_09.png) 

5.  I URL-textrutan för autentisering på den **ändra autentiserings-URL** dialogrutan klistrar du in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen och klicka sedan på **nästa**.
   
    ![Mixpanel inställningar](./media/mixpanel-tutorial/tutorial_mixpanel_10.png) 

6. Klicka på **Klar**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Mixpanel.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Mixpanel**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Mixpanel**.

    ![Länken Mixpanel i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-mixpanel-test-user"></a>Skapa Mixpanel testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Mixpanel. 

1. Logga in på webbplatsen Mixpanel företag som administratör.

2. Längst ned på sidan klickar du på knappen lite gear i det vänstra hörnet för att öppna den **inställningar** fönster.

3. Klicka på den **Team** fliken.

4. I den **teammedlem** textrutan skriver Brittas e-postadress i Azure.
   
    ![Mixpanel inställningar](./media/mixpanel-tutorial/tutorial_mixpanel_11.png) 

5. Klicka på **Bjud in**. 

> [!Note]
> Användaren får ett e-postmeddelande för att konfigurera profilen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Mixpanel i åtkomstpanelen, bör det vara loggas in automatiskt till Mixpanel som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

