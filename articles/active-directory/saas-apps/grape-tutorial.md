---
title: 'Självstudier: Azure Active Directory-integrering med gr Pe | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och tona Pe.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 073f8641-b64d-4754-b1a6-2b91c865b13d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02df0a5d13aeb90049383f61d743e8a11e93fc79
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188537"
---
# <a name="tutorial-azure-active-directory-integration-with-gra-pe"></a>Självstudier: Azure Active Directory-integrering med gr Pe

Lär dig hur du integrerar gr Pe med Azure Active Directory (AD Azure) i den här självstudien.

Integrera gr Pe med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till gr Pe.
- Du kan aktivera användarna att automatiskt få loggat in på gr-Pe (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med gr Pe, behöver du följande objekt:

- En Azure AD-prenumeration
- En Tona-Pe enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till gr Pe från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-gra-pe-from-the-gallery"></a>Att lägga till gr Pe från galleriet
För att konfigurera integrering av gr Pe i Azure AD, som du behöver lägga till gr Pe från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till gr Pe från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![image](./media/grape-tutorial/selectazuread.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![image](./media/grape-tutorial/a_select_app.png)
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![image](./media/grape-tutorial/a_new_app.png)

4. I sökrutan skriver **gr Pe**väljer **gr Pe** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![image](./media/grape-tutorial/tutorial_grape_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med gr-Pe baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i gr Pe är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i gr Pe upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med gr Pe, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare gr Pe](#create-a-gra-pe-test-user)**  – du har en motsvarighet för Britta Simon i l-Pe som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt gr Pe-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med gr Pe:**

1. I den [Azure-portalen](https://portal.azure.com/)på den **gr Pe** application integration markerar **enkel inloggning**.

    ![image](./media/grape-tutorial/b1_b2_select_sso.png)

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![image](./media/grape-tutorial/b1_b2_saml_sso.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![image](./media/grape-tutorial/b1-domains_and_urlsedit.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    I den **inloggnings-URL** anger en URL som:  `https://btm.tts.co.jp/portal/apl/SSOLogin.aspx`

    ![image](./media/grape-tutorial/tutorial_grape_url.png)

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Ladda ned** för att ladda ned **Certifikat (Base64)** och spara det på datorn.

    ![image](./media/grape-tutorial/tutorial_grape_certficate.png)

6. På den **konfigurera gr Pe** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggnings-URL

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

    ![image](./media/grape-tutorial/d1_samlsonfigure.png) 

7. Att konfigurera enkel inloggning på **gr Pe** sida, som du behöver skicka de hämtade **certifikat (Base64)** och kopierade **utloggnings-URL för inloggnings-URL, Azure AD-ID,** till [ Gr Pe supportteamet](https://www.toppantravel.com/inquiry/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![image](./media/grape-tutorial/d_users_and_groups.png)

2. Välj **Ny användare** överst på skärmen.

    ![image](./media/grape-tutorial/d_adduser.png)

3. Genomför följande steg i Användaregenskaper.

    ![image](./media/grape-tutorial/d_userproperties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.
 
### <a name="create-a-gra-pe-test-user"></a>Skapa en Tona-Pe testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i gr Pe. Arbeta med [gr Pe supportteamet](https://www.toppantravel.com/inquiry/) att lägga till användare i gr Pe-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till gr Pe.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![image](./media/grape-tutorial/d_all_applications.png)

2. I listan med program väljer **gr Pe**.

    ![image](./media/grape-tutorial/tutorial_grape_app.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![image](./media/grape-tutorial/d_leftpaneusers.png)

4. Välj den **Lägg till** knappen och välj **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![image](./media/grape-tutorial/d_assign_user.png)

4. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

5. I dialogrutan **Lägg till tilldelning** väljer du knappen **Tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen gr Pe i åtkomstpanelen du bör få automatiskt loggat in på ditt gr Pe-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


