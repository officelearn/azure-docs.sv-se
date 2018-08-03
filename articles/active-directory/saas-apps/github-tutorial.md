---
title: 'Självstudier: Azure Active Directory-integrering med GitHub | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 7f932346cdfd6363bd76f8167b8598d3a1199de3
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427796"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Självstudier: Azure Active Directory-integrering med GitHub

I den här självstudien får du lära dig hur du integrerar GitHub med Azure Active Directory (AD Azure).

Integrera GitHub med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till GitHub.
- Du kan aktivera användarna att automatiskt få loggat in på GitHub (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med GitHub, behöver du följande objekt:

- En Azure AD-prenumeration
- En GitHub enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägga till GitHub från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-github-from-the-gallery"></a>Lägga till GitHub från galleriet
För att konfigurera integrering av GitHub i Azure AD, som du behöver lägga till GitHub från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till GitHub från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **GitHub**väljer **GitHub** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![GitHub i resultatlistan](./media/github-tutorial/tutorial_github_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med GitHub baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i GitHub är en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i GitHub upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med GitHub, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare i GitHub](#create-a-github-test-user)**  – du har en motsvarighet för Britta Simon i GitHub som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt GitHub-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med GitHub:**

1. I Azure-portalen på den **GitHub** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/github-tutorial/tutorial_github_samlbase.png)

1. På den **GitHub domän och URL: er** avsnittet, utför följande steg:

    ![GitHub-domän och URL: er med enkel inloggning för information](./media/github-tutorial/tutorial_github_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://github.com/orgs/<entity-id>/sso`

    b. I den **identifierare (entitets-ID)** textrutan anger du ett URL med hjälp av följande mönster: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Observera att detta inte är det verkliga värdet. Du måste uppdatera dessa värden med faktiska inloggnings-URL och identifierare. Här föreslår vi att du för att använda det unika värdet av strängen i identifierare. Gå till GitHub-administratören att hämta dessa värden.

1. På den **användarattribut** väljer **användaridentifierare** som user.mail.

    ![Konfigurera enkel inloggning](./media/github-tutorial/tutorial_github_attribute_new01.png)

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/github-tutorial/tutorial_github_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/github-tutorial/tutorial_general_400.png)

1. På den **GitHub Configuration** klickar du på **konfigurera GitHub** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![GitHub-konfiguration](./media/github-tutorial/tutorial_github_configure.png) 

1. Logga in på webbplatsen GitHub-organisation som administratör i ett annat webbläsarfönster.

1. Gå till **inställningar** och klicka på **säkerhet**

    ![Inställningar](./media/github-tutorial/tutorial_github_config_github_03.png)

1. Kontrollera den **aktivera SAML-autentisering** rutan avslöja konfigurationsfält enkel inloggning. Använd sedan enkel inloggnings-URL-värdet för att uppdatera inloggnings-URL för enkel på Azure AD-konfiguration.

    ![Inställningar](./media/github-tutorial/tutorial_github_config_github_13.png)

1. Konfigurera följande fält:

    a. I den **inloggnings-URL** textrutan klistra in **SAML enkel inloggnings-URL för** värde som du har kopierat från Azure-portalen.

    b. I den **utfärdare** textrutan klistra in **SAML entitets-ID** värde som du har kopierat från Azure-portalen.

    c. Öppna det nedladdade certifikatet från Azure-portalen i anteckningar, klistra in innehållet i den **offentligt certifikat** textrutan.

    ![Inställningar](./media/github-tutorial/tutorial_github_config_github_051.png)

1. Klicka på **Test SAML-konfiguration** att bekräfta att inga verifieringsfel eller fel under enkel inloggning.

    ![Inställningar](./media/github-tutorial/tutorial_github_config_github_06.png)

1. Klicka på **Spara**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/github-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/github-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/github-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/github-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-github-test-user"></a>Skapa en testanvändare i GitHub

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i GitHub. GitHub stödjer automatisk användaretablering, vilket är som standard aktiverat. Du hittar mer information om [här](github-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du vill skapa användare manuellt kan du utföra följande steg:**

1. Logga in på webbplatsen GitHub företag som administratör.

1. Klicka på **personer**.

    ![Personer](./media/github-tutorial/tutorial_github_config_github_08.png "personer")

1. Klicka på **inbjudan medlem**.

    ![Bjuda in användare](./media/github-tutorial/tutorial_github_config_github_09.png "bjuda in användare")

1. På den **inbjudan medlem** dialogrutan utför följande steg:

    a. I den **e-post** textrutan skriver du e-postadressen för Britta Simon konto.

    ![Bjuda in människor](./media/github-tutorial/tutorial_github_config_github_10.png "bjuda in")

    b. Klicka på **skicka inbjudan**.

    ![Bjuda in människor](./media/github-tutorial/tutorial_github_config_github_11.png "bjuda in")

    > [!NOTE]
    > Azure Active Directory-kontoinnehavare kommer ett e-postmeddelande och följ en länk för att bekräfta sina konton innan den blir aktiv.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till GitHub.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon GitHub, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

1. I listan med program väljer **GitHub**.

    ![GitHub-länk i listan med program](./media/github-tutorial/tutorial_github_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på GitHub-panelen i åtkomstpanelen du bör få automatiskt loggat in på ditt GitHub-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/github-tutorial/tutorial_general_01.png
[2]: ./media/github-tutorial/tutorial_general_02.png
[3]: ./media/github-tutorial/tutorial_general_03.png
[4]: ./media/github-tutorial/tutorial_general_04.png

[100]: ./media/github-tutorial/tutorial_general_100.png

[200]: ./media/github-tutorial/tutorial_general_200.png
[201]: ./media/github-tutorial/tutorial_general_201.png
[202]: ./media/github-tutorial/tutorial_general_202.png
[203]: ./media/github-tutorial/tutorial_general_203.png

