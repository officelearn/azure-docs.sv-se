---
title: 'Självstudier: Azure Active Directory-integration med Apptio | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Apptio.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b23eba91-7698-47e7-ae75-0ceafd739965
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/6/2017
ms.author: jeedes
ms.openlocfilehash: ad5c8a61a83211147f5e4929a4f4f6fab738ba32
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39043762"
---
# <a name="tutorial-azure-active-directory-integration-with-apptio"></a>Självstudier: Azure Active Directory-integration med Apptio

I den här självstudien får du lära dig hur du integrerar Apptio med Azure Active Directory (AD Azure).

Integrera Apptio med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Apptio.
- Du kan aktivera användarna att automatiskt få loggat in på Apptio (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Apptio, behöver du följande objekt:

- En Azure AD-prenumeration
- En Apptio enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Apptio från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-apptio-from-the-gallery"></a>Att lägga till Apptio från galleriet
För att konfigurera integrering av Apptio i Azure AD, som du behöver lägga till Apptio från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Apptio från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Apptio**väljer **Apptio** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Apptio i resultatlistan](./media/apptio-tutorial/tutorial_apptio_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Apptio baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Apptio är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Apptio upprättas.

I Apptio, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Apptio, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Apptio](#create-an-apptio-test-user)**  – du har en motsvarighet för Britta Simon i Apptio som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Apptio program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Apptio:**

1. I Azure-portalen på den **Apptio** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/apptio-tutorial/tutorial_apptio_samlbase.png)

3. På den **Apptio domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i IDP-initierad läge:

    ![Apptio domän och URL: er med enkel inloggning för information](./media/apptio-tutorial/tutorial_apptio_url.png)

    I den **identifierare** textrutan angett värde: `urn:federation:apptio`

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/apptio-tutorial/tutorial_apptio_certificate.png) 

5. Apptio programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. Tänk på att **användaridentifierare** mappas med **user.mail**. Det finns andra tre attribut - **fullname, e-post och rollen** som du behöver konfigurera. Följande skärmbild visar ett exempel för detta.

    ![Konfigurera enkel inloggning](./media/apptio-tutorial/tutorial_apptio_attributes.png)     
    
    > [!NOTE]
    > Klicka på [här](../../role-based-access-control/role-assignments-portal.md) veta hur du konfigurerar **rollen** i Azure AD
    
6. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden och utför följande steg:

    | Attributnamn | Attributvärde |
    | -------------- | -------------------- |    
    | FullName       | User.DisplayName |
    | e-post           | User.Mail |
    | roll           | User.assignedrole |
    
    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/apptio-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/apptio-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **OK**.

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/apptio-tutorial/tutorial_general_400.png)
    
9. Att konfigurera enkel inloggning på **Apptio** sida, som du behöver skicka de hämtade **XML-Metadata för** till [Apptio supportteamet](https://www.apptio.com/about/contact). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/apptio-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/apptio-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/apptio-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/apptio-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-apptio-test-user"></a>Skapa en Apptio testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Apptio. Arbeta med [Apptio supportteamet](https://www.apptio.com/about/contact) att lägga till användare i Apptio-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Apptio.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Apptio, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Apptio**.

    ![Länken Apptio i listan med program](./media/apptio-tutorial/tutorial_apptio_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Apptio i åtkomstpanelen du bör få automatiskt loggat in på ditt Apptio program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/apptio-tutorial/tutorial_general_01.png
[2]: ./media/apptio-tutorial/tutorial_general_02.png
[3]: ./media/apptio-tutorial/tutorial_general_03.png
[4]: ./media/apptio-tutorial/tutorial_general_04.png

[100]: ./media/apptio-tutorial/tutorial_general_100.png

[200]: ./media/apptio-tutorial/tutorial_general_200.png
[201]: ./media/apptio-tutorial/tutorial_general_201.png
[202]: ./media/apptio-tutorial/tutorial_general_202.png
[203]: ./media/apptio-tutorial/tutorial_general_203.png
