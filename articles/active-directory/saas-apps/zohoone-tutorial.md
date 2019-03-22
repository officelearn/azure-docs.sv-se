---
title: 'Självstudier: Azure Active Directory-integrering med en Zoho | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zoho en.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aadb3188969e099a709711ac18916b67c83b5da1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884446"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Självstudier: Azure Active Directory-integrering med en Zoho

I den här självstudien får du lära dig hur du integrerar en Zoho med Azure Active Directory (AD Azure).

Integrera en Zoho med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till en Zoho.
- Du kan aktivera användarna att automatiskt få loggat in på en Zoho (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med en Zoho, behöver du följande objekt:

- En Azure AD-prenumeration
- En Zoho en enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till en Zoho från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-zoho-one-from-the-gallery"></a>Att lägga till en Zoho från galleriet
Om du vill konfigurera integreringen av en Zoho i Azure AD, som du behöver lägga till en Zoho från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till en Zoho från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Zoho en**väljer **Zoho en** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![En Zoho i resultatlistan](./media/zohoone-tutorial/tutorial_zohoone_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Zoho en baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i en Zoho är till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i en Zoho upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med en Zoho, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en Zoho en testanvändare](#create-a-zoho-one-test-user)**  – du har en motsvarighet för Britta Simon i Zoho en som är kopplad till Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Zoho ett program.

**Om du vill konfigurera Azure AD enkel inloggning med en Zoho, utför du följande steg:**

1. I Azure-portalen på den **Zoho en** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/zohoone-tutorial/tutorial_zohoone_samlbase.png)

1. På den **Zoho en domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Zoho en domän och URL: er med enkel inloggning för information](./media/zohoone-tutorial/tutorial_zohoone_url.png)

    a. I den **identifierare (entitets-ID)** textrutan anger du ett URL: `one.zoho.com`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    c. Kontrollera **visa avancerade URL-inställningar**.

    d. I den **Vidarebefordransstatus** textrutan anger du ett URL:`https://one.zoho.com`

1. Om du vill konfigurera programmet i **SP**-initierat läge gör du följande:

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com`
     
    > [!NOTE] 
    > Föregående **svars-URL** och **inloggnings-URL** värdet är inte verkliga. Du ska uppdatera värdet med faktiska svars-URL och inloggnings-URL som beskrivs senare i självstudien. 

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/zohoone-tutorial/tutorial_zohoone_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/zohoone-tutorial/tutorial_general_400.png)
    
1. På den **Zoho en konfiguration** klickar du på **konfigurera Zoho en** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Zoho en konfiguration](./media/zohoone-tutorial/tutorial_zohoone_configure.png) 

1. I ett annat webbläsarfönster logga du in på webbplatsen Zoho ett företag som administratör.

1. På den **organisation** fliken, klickar du på **installationsprogrammet** under **SAML-autentisering**.

    ![En Zoho org](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

1. På sidan popup utför du följande steg:

    ![Zoho One sig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. I den **inloggning URL** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    b. I den **URL: en för utloggning** textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.

    c. Klicka på **Bläddra** att ladda upp den **certifikat (Base64)** som du har hämtat från Azure-portalen.

    d. Klicka på **Spara**.

1. När du har sparat inställningarna för SAML-autentisering, kopiera den **SAML-Identfier** värde och använda det här värdet i den **svars-URL** i Azure-portalen under **Zoho en domän och URL: er** avsnittet.

    ![Zoho One saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

1. Gå till den **domäner** fliken och klicka sedan på **Lägg till domän**.

    ![Zoho One domain](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

1. På den **Lägg till domän** utför följande steg:

    ![Zoho en Lägg till domän](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. I den **domännamn** textrutan Ange domän som **contoso.com**.

    b. Klicka på **Lägg till**.

    >[!Note]
    >När du lägger till den domän Följ [dessa](https://www.zoho.com/one/help/admin-guide/domain-verification.html) steg för att verifiera din domän. När domänen är verfified kan använda ditt domännamn i **inloggnings-URL** i **Zoho en domän och URL: er** avsnitt i Azure-portalen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/zohoone-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/zohoone-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/zohoone-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/zohoone-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-zoho-one-test-user"></a>Skapa en Zoho en testanvändare

Om du vill aktivera Azure AD-användare att logga in på en Zoho, måste de etableras i en Zoho. I en Zoho är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på Zoho en som en administratör.

1. På den **användare** fliken, klicka på **användaren logotyp**.

    ![Zoho One user](./media/zohoone-tutorial/tutorial_zohoone_users.png)

1. På den **Lägg till användare** utför följande steg:

    ![En Zoho lägga till användare](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. I **namn** text, ange namnet på användaren som **Britta simon**.
    
    b. I **e-postadress** text, ange den e-postadressen för användaren som **brittasimon\@contoso.com**.

    >[!Note]
    >Välj din verifierade domän i listan för domänen.

    c. Klicka på **Lägg till**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till en Zoho.

![Tilldela rollen][200] 

**Om du vill tilldela en Zoho Britta Simon, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Zoho en**.

    ![Zoho en länk i listan med program](./media/zohoone-tutorial/tutorial_zohoone_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på den Zoho en panelen i åtkomstpanelen du bör få automatiskt loggat in på ditt Zoho ett program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zohoone-tutorial/tutorial_general_01.png
[2]: ./media/zohoone-tutorial/tutorial_general_02.png
[3]: ./media/zohoone-tutorial/tutorial_general_03.png
[4]: ./media/zohoone-tutorial/tutorial_general_04.png

[100]: ./media/zohoone-tutorial/tutorial_general_100.png

[200]: ./media/zohoone-tutorial/tutorial_general_200.png
[201]: ./media/zohoone-tutorial/tutorial_general_201.png
[202]: ./media/zohoone-tutorial/tutorial_general_202.png
[203]: ./media/zohoone-tutorial/tutorial_general_203.png

