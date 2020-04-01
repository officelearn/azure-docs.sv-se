---
title: 'Självstudiekurs: Azure Active Directory-integrering med SkyDesk-e-post | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SkyDesk-e-post.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: faaa5dcc435452d6ed9e0f2c5b481df1e352dfd2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090431"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Självstudiekurs: Azure Active Directory-integrering med SkyDesk-e-post

I den här självstudien får du lära dig hur du integrerar SkyDesk-e-post med Azure Active Directory (Azure AD).
Genom att integrera SkyDesk-e-post med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SkyDesk-e-post.
* Du kan aktivera dina användare så att de automatiskt loggas in på SkyDesk-e-post (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med SkyDesk-e-post behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SkyDesk E-post enda inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SkyDesk Email stöder **SP** initierad SSO

## <a name="adding-skydesk-email-from-the-gallery"></a>Lägga till SkyDesk-e-post från galleriet

Om du vill konfigurera integreringen av SkyDesk-e-post i Azure AD måste du lägga till SkyDesk-e-post från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till SkyDesk-e-post från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **SkyDesk-e-post**i sökrutan och välj **SkyDesk-e-post** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

     ![SkyDesk E-post i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med SkyDesk-e-post baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SkyDesk-e-post upprättas.

Om du vill konfigurera och testa azure AD-enkel inloggning med SkyDesk-e-post måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SkyDesk Email Single Sign-On](#configure-skydesk-email-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SkyDesk E-testanvändare](#create-skydesk-email-test-user)** - om du vill ha en motsvarighet till Britta Simon i SkyDesk-e-post som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med SkyDesk-e-post:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **SkyDesk-e-postprogram.** **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![SkyDesk e-postdomän och webbadresser enkel inloggningsinformation](common/sp-signonurl.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [SkyDesk e-postklientsupport för](https://www.skydesk.jp/apps/support/) att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera SkyDesk-e-post.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-skydesk-email-single-sign-on"></a>Konfigurera SkyDesk-e-post enkel inloggning

1. I en annan webbläsare loggar du in på ditt SkyDesk-e-postkonto som administratör.

1. Klicka på **Inställningar**på menyn högst upp och välj **Org**.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. Klicka på **Domäner** från den vänstra panelen.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. Klicka på **Lägg till domän**.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Ange ditt domännamn och verifiera sedan domänen.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Klicka på **SAML-autentisering** från den vänstra panelen.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Gör följande på dialogsidan **för SAML-autentisering:**

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > Om du vill använda SAML-baserad autentisering bör du antingen ha **verifierad domän-** eller **portal-URL-konfiguration.** Du kan ange portal-URL:en med det unika namnet.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. Klistra in värdet **för inloggnings-URL**i textrutan **Inloggnings-URL** , som du har kopierat från Azure-portalen.

    b. Klistra in värdet för url för **utloggning**i textrutan **Logout** URL , som du har kopierat från Azure-portalen.

    c. **Ändra lösenords-URL** är valfritt så lämna det tomt.

    d. Klicka på **Hämta nyckel från fil** för att välja ditt hämtade certifikat från Azure-portalen och klicka sedan på **Öppna** för att ladda upp certifikatet.

    e. Som **Algoritm** väljer du **RSA**.

    f. Klicka på **Ok** om du vill spara ändringarna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till SkyDesk-e-post.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **SkyDesk-e-post**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **SkyDesk-e-post i**programlistan .

    ![Länken SkyDesk e-post i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-skydesk-email-test-user"></a>Skapa SkyDesk-e-posttestanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i SkyDesk Email.

Klicka på **Användaråtkomst** från den vänstra panelen i SkyDesk E-post och ange sedan ditt användarnamn.

![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Om du behöver skapa massanvändare måste du kontakta [SkyDesks supportteam för e-postklienten](https://www.skydesk.jp/apps/support/).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på skydesk-e-panelen på åtkomstpanelen ska du automatiskt loggas in i skydesk-e-postmeddelandet som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

