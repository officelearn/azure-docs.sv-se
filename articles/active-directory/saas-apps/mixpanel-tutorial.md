---
title: 'Självstudiekurs: Azure Active Directory-integrering med Mixpanel | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mixpanel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a2df26ef-d441-44ac-a9f3-b37bf9709bcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/28/2019
ms.author: jeedes
ms.openlocfilehash: 58074d02dfc437a1804784e73fa4e65086b53b9e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160465"
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>Självstudiekurs: Azure Active Directory-integrering med Mixpanel

I den här självstudien får du lära dig hur du integrerar Mixpanel med Azure Active Directory (Azure AD).
Genom att integrera Mixpanel med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Mixpanel.
* Du kan aktivera dina användare så att de automatiskt loggas in på Mixpanel (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Mixpanel behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Mixpanel enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Mixpanel stöder **SP** initierade SSO

## <a name="adding-mixpanel-from-the-gallery"></a>Lägga till Mixpanel från galleriet

Om du vill konfigurera integreringen av Mixpanel i Azure AD måste du lägga till Mixpanel från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Mixpanel från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Mixpanel**i sökrutan , välj **Mixpanel** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![Mixpanel i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Mixpanel baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Mixpanel upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Mixpanel måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Mixpanel Single Sign-On](#configure-mixpanel-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Mixpanel-testanvändare](#create-mixpanel-test-user)** - om du vill ha en motsvarighet till Britta Simon i Mixpanel som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Mixpanel:

1. Välj Enkel inloggning på sidan Mixpanel-programintegrering **på Azure-portalen**. [Azure portal](https://portal.azure.com/) **Mixpanel**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Mixpanel Domän och webbadresser enkel inloggningsinformation](common/sp-signonurl.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://mixpanel.com/login/`

    > [!NOTE]
    > Registrera dig [https://mixpanel.com/register/](https://mixpanel.com/register/) för att ställa in dina inloggningsuppgifter och kontakta [Mixpanels supportteam](mailto:support@mixpanel.com) för att aktivera SSO-inställningar för din klient. Du kan också få ditt inloggnings-URL-värde om det behövs från ditt Supportteam för Mixpanel. 

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera mixpanel.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-mixpanel-single-sign-on"></a>Konfigurera enkel inloggning på Mixpanel

1. I ett annat webbläsarfönster loggar du in på Mixpanel-programmet som administratör.

2. Klicka på den lilla **kugghjulsikonen** längst ned på sidan i det vänstra hörnet. 
   
    ![Mixpanel Enkel inloggning](./media/mixpanel-tutorial/tutorial_mixpanel_06.png) 

3. Klicka på **fliken Access** och sedan på **Ändra inställningar**.
   
    ![Inställningar för Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_08.png) 

4. Klicka på **Välj fil** för att ladda upp det nedladdade certifikatet på dialogrutan **Ändra certifikat** och klicka sedan på **NÄSTA**.
   
    ![Inställningar för Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_09.png) 

5.  Klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen i textrutan **autentiserings-URL på dialogrutan Ändra autentiserings-URL** och klicka sedan på **NÄSTA**.
   
    ![Inställningar för Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_10.png) 

6. Klicka på **Klar**.

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

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Mixpanel.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Mixpanel**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Mixpanel**i programlistan .

    ![Länken Mixpanel i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-mixpanel-test-user"></a>Skapa Mixpanel-testanvändare

Syftet med detta avsnitt är att skapa en användare som heter Britta Simon i Mixpanel. 

1. Logga in på din Mixpanel-företagswebbplats som administratör.

2. Klicka på den lilla växelknappen längst ned på sidan för att öppna fönstret **Inställningar.**

3. Klicka på fliken **Team.**

4. Skriv Brittas e-postadress i Azure i textrutan **för gruppmedlem.**
   
    ![Inställningar för Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_11.png) 

5. Klicka på **Bjud in**. 

> [!Note]
> Användaren får ett e-postmeddelande för att ställa in profilen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Mixpanel på åtkomstpanelen ska du automatiskt loggas in på mixpanelen som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

