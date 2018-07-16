---
title: 'Självstudier: Azure Active Directory-integration med kunskaper Base | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och färdigheter Base.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 237d90c4-8243-4f80-a305-b5ad9204159e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: jeedes
ms.openlocfilehash: 84aac0017496c50f0006fd6e184537e4c14f10c7
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059447"
---
# <a name="tutorial-azure-active-directory-integration-with-skills-base"></a>Självstudier: Azure Active Directory-integration med kunskaper Base

Lär dig hur du integrerar grundläggande kunskaper med Azure Active Directory (AD Azure) i den här självstudien.

Integrera grundläggande kunskaper med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till grundläggande kunskaper.
- Du kan aktivera användarna att automatiskt få loggat in på färdigheter Base (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med grundläggande kunskaper, behöver du följande objekt:

- En Azure AD-prenumeration
- En grundläggande kunskaper enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till kunskap Base från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-skills-base-from-the-gallery"></a>Att lägga till kunskap Base från galleriet
För att konfigurera integrering av grundläggande kunskaper i Azure AD, som du behöver lägga till kunskap Base från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till kunskap Base från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **kunskaper Base**väljer **kunskaper Base** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Grundläggande kunskaper i resultatlistan](./media/skillsbase-tutorial/tutorial_skillsbase_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med kunskaper Base baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i kunskaper Base är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i kunskaper Base upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med grundläggande kunskaper, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare kunskaper Base](#create-a-skills-base-test-user)**  – du har en motsvarighet för Britta Simon i kunskaper Base som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program med grundläggande kunskaper.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med grundläggande kunskaper:**

1. I Azure-portalen på den **kunskaper Base** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/skillsbase-tutorial/tutorial_skillsbase_samlbase.png)

3. På den **kunskaper Base domän och URL: er** avsnittet, utför följande steg:

    ![Kunskaper Base domän och URL: er med enkel inloggning för information](./media/skillsbase-tutorial/tutorial_skillsbase_url.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://app.skills-base.com/o/<customer-unique-key>`

    > [!NOTE] 
    > Inloggnings-URL-värdet är inte verkliga. Uppdatera värdet med faktiska inloggnings-URL: en. Kontakta [kunskaper Base klienten supportteamet](mailto:support@skills-base.com) att hämta värdet.

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/skillsbase-tutorial/tutorial_skillsbase_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/skillsbase-tutorial/tutorial_general_400.png)

6. I ett annat webbläsarfönster, logga in på färdigheter Base som en administratör.

7. Från vänster sida av menyn under **ADMIN** klickar du på **autentisering**.

    ![Administratören](./media/skillsbase-tutorial/tutorial_skillsbase_auth.png)

8. På den **autentisering** väljer du enkel inloggning som **SAML 2**.

    ![Enda](./media/skillsbase-tutorial/tutorial_skillsbase_single.png)

9. På den **autentisering** utför följande steg:

    ![Enda](./media/skillsbase-tutorial/tutorial_skillsbase_save.png)

    a. Klicka på **uppdatering IdP metadata** bredvid knappen **Status** alternativet och klistra in innehållet i XML-Metadata som du hämtade från Azure-portalen i angivna textrutan.

    > [!Note]
    > Du kan också bekräfta idp metadata via den **Metadata verifieraren** verktyget som markerade i skärmbilden ovan.

    b. Klicka på **Spara**.
    

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/skillsbase-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/skillsbase-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/skillsbase-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/skillsbase-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-skills-base-test-user"></a>Skapa en grundläggande kunskaper testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i kunskaper Base. Kunskaper Base stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt grundläggande kunskaper om det inte finns ännu.

>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [kunskaper Base klienten supportteamet](mailto:support@skills-base.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till grundläggande kunskaper.

![Tilldela rollen][200] 

**Om du vill tilldela kunskaper Base Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **kunskaper Base**.

    ![Länken grundläggande kunskaper i listan med program](./media/skillsbase-tutorial/tutorial_skillsbase_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen grundläggande kunskaper i åtkomstpanelen du bör få automatiskt loggat in på dina kunskaper Base-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skillsbase-tutorial/tutorial_general_01.png
[2]: ./media/skillsbase-tutorial/tutorial_general_02.png
[3]: ./media/skillsbase-tutorial/tutorial_general_03.png
[4]: ./media/skillsbase-tutorial/tutorial_general_04.png

[100]: ./media/skillsbase-tutorial/tutorial_general_100.png

[200]: ./media/skillsbase-tutorial/tutorial_general_200.png
[201]: ./media/skillsbase-tutorial/tutorial_general_201.png
[202]: ./media/skillsbase-tutorial/tutorial_general_202.png
[203]: ./media/skillsbase-tutorial/tutorial_general_203.png

