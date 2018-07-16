---
title: 'Självstudier: Azure Active Directory-integrering med kunskaper Manager | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och färdigheter Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: dab8debd-3b7b-4656-9bf0-1963ad8fce05
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jeedes
ms.openlocfilehash: 06bd3670c576a0515956147996d7500c75cf8708
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050688"
---
# <a name="tutorial-azure-active-directory-integration-with-skills-manager"></a>Självstudier: Azure Active Directory-integrering med kunskaper Manager

Lär dig hur du integrerar kunskaper Manager med Azure Active Directory (AD Azure) i den här självstudien.

Integrera kunskaper Manager med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till kunskaper Manager.
- Du kan aktivera användarna att automatiskt få loggat in på färdigheter Manager (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med kunskaper Manager behöver du följande objekt:

- En Azure AD-prenumeration
- En kunskaper Manager enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till kunskap Manager från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-skills-manager-from-the-gallery"></a>Att lägga till kunskap Manager från galleriet
Om du vill konfigurera integreringen av kunskaper Manager till Azure AD, som du behöver lägga till kunskap från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till kunskap Manager från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **kunskaper Manager**väljer **kunskaper Manager** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Kunskaper Manager i resultatlistan](./media/skillsmanager-tutorial/tutorial_skillsmanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med kunskaper Manager baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i kunskaper Manager är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i kunskaper Manager upprättas.

I kunskaper Manager, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med kunskaper Manager, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare kunskaper Manager](#create-a-skills-manager-test-user)**  – du har en motsvarighet för Britta Simon i kunskaper Manager som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt kunskaper Manager-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med kunskaper Manager:**

1. I Azure-portalen på den **kunskaper Manager** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/skillsmanager-tutorial/tutorial_skillsmanager_samlbase.png)

3. På den **kunskaper Manager-domän och URL: er** avsnittet, utför följande steg:

    ![Kunskaper Manager-domän och URL: er med enkel inloggning för information](./media/skillsmanager-tutorial/tutorial_skillsmanager_url.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://subdomain.skills-manager.com/kennametal`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://subdomain.skills-manager.com/public/SamlLogin2.aspx`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare och svars-URL. Kontakta [kunskaper Manager supportteamet](https://www.ibm.com/support/uk/?lnk=msu_uk) att hämta dessa värden.
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/skillsmanager-tutorial/tutorial_skillsmanager_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/skillsmanager-tutorial/tutorial_general_400.png)

6. På den **kunskaper Manager Configuration** klickar du på **konfigurera kunskaper Manager** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfiguration av kunskaper Manager](./media/skillsmanager-tutorial/tutorial_skillsmanager_configure.png) 

7. Att konfigurera enkel inloggning på **kunskaper Manager** sida, som du behöver skicka de hämtade **Certificate(Base64)**, **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** till [kunskaper Manager supportteamet](https://www.ibm.com/support/uk/?lnk=msu_uk). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/skillsmanager-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/skillsmanager-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/skillsmanager-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/skillsmanager-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-a-skills-manager-test-user"></a>Skapa en testanvändare kunskaper Manager

I det här avsnittet skapar du en användare som kallas Britta Simon i kunskaper Manager. Arbeta med [kunskaper Manager supportteamet](https://www.ibm.com/support/uk/?lnk=msu_uk) att lägga till användare i kunskaper Manager-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till kunskaper Manager.

![Tilldela rollen][200] 

**Om du vill tilldela kunskaper Manager Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **kunskaper Manager**.

    ![Länken kunskaper Manager i listan med program](./media/skillsmanager-tutorial/tutorial_skillsmanager_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen kunskaper Manager i åtkomstpanelen du bör få automatiskt loggat in på dina kunskaper Manager-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skillsmanager-tutorial/tutorial_general_01.png
[2]: ./media/skillsmanager-tutorial/tutorial_general_02.png
[3]: ./media/skillsmanager-tutorial/tutorial_general_03.png
[4]: ./media/skillsmanager-tutorial/tutorial_general_04.png

[100]: ./media/skillsmanager-tutorial/tutorial_general_100.png

[200]: ./media/skillsmanager-tutorial/tutorial_general_200.png
[201]: ./media/skillsmanager-tutorial/tutorial_general_201.png
[202]: ./media/skillsmanager-tutorial/tutorial_general_202.png
[203]: ./media/skillsmanager-tutorial/tutorial_general_203.png

