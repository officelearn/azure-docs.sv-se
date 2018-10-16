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
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeedes
ms.openlocfilehash: dbd4634c575fd4f1886d3e7714ef9ddabbde0f8a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341165"
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
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-github-from-the-gallery"></a>Lägga till GitHub från galleriet
För att konfigurera integrering av GitHub i Azure AD, som du behöver lägga till GitHub från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till GitHub från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![image](./media/github-tutorial/selectazuread.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![image](./media/github-tutorial/a_select_app.png)
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![image](./media/github-tutorial/a_new_app.png)

4. I sökrutan skriver **GitHub**väljer **GitHub** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![image](./media/github-tutorial/tutorial_github_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med GitHub baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i GitHub är en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i GitHub upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med GitHub, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare i GitHub](#create-a-github-test-user)**  – du har en motsvarighet för Britta Simon i GitHub som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt GitHub-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med GitHub:**

1. I den [Azure-portalen](https://portal.azure.com/)på den **GitHub** application integration markerar **enkel inloggning**.

    ![image](./media/github-tutorial/b1_b2_select_sso.png)

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![image](./media/github-tutorial/b1_b2_saml_sso.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![image](./media/github-tutorial/b1-domains_and_urlsedit.png)

4. På den **SAML grundkonfiguration** avsnittet, utför följande steg:

    ![image](./media/github-tutorial/tutorial_github_url.png) 

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://github.com/orgs/<entity-id>/sso`

    b. I den **identifierare (entitets-ID)** textrutan anger du ett URL med hjälp av följande mönster: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Observera att detta inte är det verkliga värdet. Du måste uppdatera dessa värden med faktiska inloggnings-URL och identifierare. Här föreslår vi att du för att använda det unika värdet av strängen i identifierare. Gå till GitHub-administratören att hämta dessa värden.

5. GitHub-program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut** avsnitt på sidan för integrering av programmet. Klicka på **redigera** knappen för att öppna **användarattribut** dialogrutan.

    ![image](./media/github-tutorial/i3-attribute.png)

6. I den **användaranspråk** avsnittet på den **användarattribut** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    a. Klicka på **redigera** knappen för att öppna den **hantera användaranspråk** dialogrutan.

    ![image](./media/github-tutorial/i2-attribute.png)

    ![image](./media/github-tutorial/i4-attribute.png)

    b. Från den **källattribut** väljer attributvärdet.

    c. Klicka på **Spara**.
 
7. I den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned den **certifikat (Base64)** och spara den på din dator.

    ![image](./media/github-tutorial/tutorial_github_certficate.png)

8. På den **konfigurera GitHub** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggningswebbadress

    b. Azure AD-ID

    c. Utloggnings-URL

    ![image](./media/github-tutorial/d1_samlsonfigure.png) 

9. Logga in på webbplatsen GitHub-organisation som administratör i ett annat webbläsarfönster.

10. Gå till **inställningar** och klicka på **säkerhet**

    ![Inställningar](./media/github-tutorial/tutorial_github_config_github_03.png)

11. Kontrollera den **aktivera SAML-autentisering** rutan avslöja konfigurationsfält enkel inloggning. Använd sedan enkel inloggnings-URL-värdet för att uppdatera inloggnings-URL för enkel på Azure AD-konfiguration.

    ![Inställningar](./media/github-tutorial/tutorial_github_config_github_13.png)

12. Konfigurera följande fält:

    ![Inställningar](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. I den **inloggnings-URL** textrutan klistra in **inloggnings-URL** värde som du har kopierat från Azure-portalen.

    b. I den **utfärdare** textrutan klistra in **Azure AD-identifierare** värde som du har kopierat från Azure-portalen.

    c. Öppna det nedladdade certifikatet från Azure-portalen i anteckningar, klistra in innehållet i den **offentligt certifikat** textrutan.

    d. Klicka på **redigera** ikon för att redigera den **signatur metoden** och **sammanfattad metoden** från **RSA-SHA1** och **SHA1**till **RSA-SHA256** och **SHA256** enligt nedan.

    ![image](./media/github-tutorial/tutorial_github_sha.png) 
    
13. Klicka på **Test SAML-konfiguration** att bekräfta att inga verifieringsfel eller fel under enkel inloggning.

    ![Inställningar](./media/github-tutorial/tutorial_github_config_github_06.png)

14. Klicka på **Spara**

> [!NOTE]
> Enkel inloggning i GitHub autentiserar till en viss organisation i GitHub och ersätter inte autentisering av själva GitHub. Därför om användarens GitHub.com session har upphört att gälla, kan du bli ombedd att autentisera med GitHub-ID/lösenord under processen för enkel inloggning.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![image](./media/github-tutorial/d_users_and_groups.png)

2. Välj **ny användare** överst på skärmen.

    ![image](./media/github-tutorial/d_adduser.png)

3. Utför följande steg i egenskaperna för användaren.

    ![image](./media/github-tutorial/d_userproperties.png)

    a. I den **namn** ange **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.
 
### <a name="create-a-github-test-user"></a>Skapa en testanvändare i GitHub

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i GitHub. GitHub stödjer automatisk användaretablering, vilket är som standard aktiverat. Du hittar mer information om [här](github-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du vill skapa användare manuellt kan du utföra följande steg:**

1. Logga in på webbplatsen GitHub företag som administratör.

2. Klicka på **personer**.

    ![Personer](./media/github-tutorial/tutorial_github_config_github_08.png "personer")

3. Klicka på **inbjudan medlem**.

    ![Bjuda in användare](./media/github-tutorial/tutorial_github_config_github_09.png "bjuda in användare")

4. På den **inbjudan medlem** dialogrutan utför följande steg:

    a. I den **e-post** textrutan skriver du e-postadressen för Britta Simon konto.

    ![Bjuda in människor](./media/github-tutorial/tutorial_github_config_github_10.png "bjuda in")

    b. Klicka på **skicka inbjudan**.

    ![Bjuda in människor](./media/github-tutorial/tutorial_github_config_github_11.png "bjuda in")

    > [!NOTE]
    > Azure Active Directory-kontoinnehavare kommer ett e-postmeddelande och följ en länk för att bekräfta sina konton innan den blir aktiv.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till GitHub.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![image](./media/github-tutorial/d_all_applications.png)

2. I listan med program väljer **GitHub**.

    ![image](./media/github-tutorial/tutorial_github_app.png)

3. I menyn till vänster väljer **användare och grupper**.

    ![image](./media/github-tutorial/d_leftpaneusers.png)

4. Välj den **Lägg till** knappen och välj **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![image](./media/github-tutorial/d_assign_user.png)

4. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

5. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på GitHub-panelen i åtkomstpanelen du bör få automatiskt loggat in på ditt GitHub-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


