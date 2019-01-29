---
title: 'Självstudier: Azure Active Directory-integrering med Apptio | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Apptio.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: b23eba91-7698-47e7-ae75-0ceafd739965
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/6/2017
ms.author: jeedes
ms.openlocfilehash: c3024304bd58a30edaf77117ce8849e6bb12fa94
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203070"
---
# <a name="tutorial-azure-active-directory-integration-with-apptio"></a>Självstudier: Azure Active Directory-integrering med Apptio

I den här självstudien får du lära dig hur du integrerar Apptio med Azure Active Directory (AD Azure).

Integrera Apptio med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Apptio.
- Du kan aktivera användarna att automatiskt få loggat in på Apptio (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Apptio, behöver du följande objekt:

- En Azure AD-prenumeration
- En Apptio enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Apptio från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-apptio-from-the-gallery"></a>Att lägga till Apptio från galleriet
För att konfigurera integrering av Apptio i Azure AD, som du behöver lägga till Apptio från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Apptio från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Apptio**väljer **Apptio** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Apptio i resultatlistan](./media/apptio-tutorial/tutorial_apptio_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Apptio baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Apptio är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Apptio upprättas.

I Apptio, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Apptio, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare Apptio](#create-an-apptio-test-user)**  – du har en motsvarighet för Britta Simon i Apptio som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Apptio program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Apptio:**

1. I Azure-portalen på den **Apptio** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/apptio-tutorial/tutorial_apptio_samlbase.png)

3. På den **Apptio domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i IDP-initierad läge:

    ![Apptio domän och URL: er med enkel inloggning för information](./media/apptio-tutorial/tutorial_apptio_url.png)

    I den **identifierare** textrutan angett värde: `urn:federation:apptio`

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/apptio-tutorial/tutorial_apptio_certificate.png) 

5. Apptio programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. Tänk på att **användaridentifierare** mappas med **user.mail**. Det finns andra tre attribut - **fullname, e-post och rollen** som du behöver konfigurera. Följande skärmbild visar ett exempel på detta.

    ![Konfigurera enkel inloggning](./media/apptio-tutorial/tutorial_apptio_attributes.png)     
    
    > [!NOTE]
    > Klicka på [här](../../role-based-access-control/role-assignments-portal.md) veta hur du konfigurerar **rollen** i Azure AD
    
6. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden och utför följande steg:

    | Attributnamn | Attributvärde |
    | -------------- | -------------------- |    
    | FullName       | User.DisplayName |
    | e-post           | user.mail |
    | roll           | user.assignedrole |
    
    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/apptio-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/apptio-tutorial/tutorial_attribute_05.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **OK**.

8. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning – knappen Spara](./media/apptio-tutorial/tutorial_general_400.png)
    
9. Att konfigurera enkel inloggning på **Apptio** sida, som du behöver skicka de hämtade **XML-Metadata för** till [Apptio supportteamet](https://www.apptio.com/about/contact). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

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

I det här avsnittet skapar du en användare som kallas Britta Simon i Apptio. Arbeta med [Apptio supportteamet](https://www.apptio.com/about/contact) att lägga till användare i Apptio-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

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
