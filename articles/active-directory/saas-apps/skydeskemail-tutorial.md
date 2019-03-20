---
title: 'Självstudier: Azure Active Directory-integrering med SkyDesk e-post | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SkyDesk e-post.
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
ms.openlocfilehash: 76e19a5d83b1e59aedc0e6e4d6a1f03ae10039a9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58167005"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Självstudier: Azure Active Directory-integrering med SkyDesk e-post

I den här självstudien får du lära dig hur du integrerar SkyDesk e-post med Azure Active Directory (AD Azure).
Integrera SkyDesk e-post med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SkyDesk e-post.
* Du kan aktivera användarna att vara automatiskt inloggad till SkyDesk e-post (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SkyDesk e-postadress, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SkyDesk e-enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för e-SkyDesk **SP** -initierad SSO

## <a name="adding-skydesk-email-from-the-gallery"></a>Att lägga till SkyDesk e-post från galleriet

För att konfigurera integrering av SkyDesk e-post i Azure AD, som du behöver lägga till SkyDesk e-post från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SkyDesk e-post från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **SkyDesk e-** väljer **SkyDesk e-** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![SkyDesk e-post i listan med resultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SkyDesk e-post baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SkyDesk e-post ska upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med SkyDesk e-postadress, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för SkyDesk-e-](#configure-skydesk-email-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa e-SkyDesk testanvändare](#create-skydesk-email-test-user)**  – du har en motsvarighet för Britta Simon i SkyDesk e-post som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med SkyDesk e-postadress:

1. I den [Azure-portalen](https://portal.azure.com/)på den **SkyDesk e-** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![SkyDesk e-postdomäner och URL: er med enkel inloggning för information](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [SkyDesk e-postklient supportteamet](https://www.skydesk.jp/apps/support/) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera SkyDesk e-post** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-skydesk-email-single-sign-on"></a>Konfigurera e-SkyDesk enkel inloggning

1. I en annan webbläsare, inloggning till ditt SkyDesk e-postkonto som administratör.

1. Klicka på menyn längst upp **installationsprogrammet**, och välj **Org**.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. Klicka på **domäner** från den vänstra panelen.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. Klicka på **Lägg till domän**.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Ange ditt domännamn och verifiera domänen.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Klicka på **SAML-autentisering** från den vänstra panelen.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. På den **SAML-autentisering** dialogrutan utför följande steg:

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > Om du vill använda SAML-baserad autentisering, bör du antingen ha **verifierad domän** eller **portal URL** installationen. Du kan ställa in portalen URL: en med ett unikt namn.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. I den **inloggnings-URL** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    b. I den **utloggning** URL-textrutan klistra in värdet för **URL för utloggning**, som du har kopierat från Azure-portalen.

    c. **Ändra lösenord URL: en** är valfritt så lämna det tomt.

    d. Klicka på **hämta nyckeln från filen** välja nedladdade certifikatet från Azure-portalen och klicka sedan på **öppna** att ladda upp.

    e. Som **Algoritm** väljer du **RSA**.

    f. Klicka på **Ok** att spara ändringarna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till SkyDesk e-post.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **SkyDesk e-**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **SkyDesk e-**.

    ![Länken SkyDesk e-post i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-skydesk-email-test-user"></a>Skapa testanvändare SkyDesk e-post

I det här avsnittet skapar du en användare som kallas Britta Simon i SkyDesk e-post.

Klicka på **användaråtkomst** från vänstra panelen i SkyDesk e-post och ange sedan ditt användarnamn.

![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Om du vill skapa grupp användare kan du behöva kontakta den [SkyDesk e-postklient supportteamet](https://www.skydesk.jp/apps/support/).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen SkyDesk e-post i åtkomstpanelen, bör det vara loggas in automatiskt till SkyDesk e-postadressen som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

