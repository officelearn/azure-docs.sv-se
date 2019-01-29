---
title: 'Självstudier: Azure Active Directory-integrering med FloQast | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FloQast.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 013cb57d-567c-44d0-a119-e6ba6e607153
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeedes
ms.openlocfilehash: f46714d2d4860abd1857e6ae16f98848678336aa
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55184790"
---
# <a name="tutorial-azure-active-directory-integration-with-floqast"></a>Självstudier: Azure Active Directory-integrering med FloQast

I den här självstudien får du lära dig hur du integrerar FloQast med Azure Active Directory (AD Azure).

Integrera FloQast med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till FloQast.
- Du kan aktivera användarna att automatiskt få loggat in på FloQast (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med FloQast, behöver du följande objekt:

- En Azure AD-prenumeration
- En FloQast enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till FloQast från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-floqast-from-the-gallery"></a>Att lägga till FloQast från galleriet
För att konfigurera integrering av FloQast i Azure AD, som du behöver lägga till FloQast från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till FloQast från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **FloQast**väljer **FloQast** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![FloQast i resultatlistan](./media/floqast-tutorial/tutorial_floqast_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med FloQast baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i FloQast är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i FloQast upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med FloQast, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare FloQast](#create-a-floqast-test-user)**  – du har en motsvarighet för Britta Simon i FloQast som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt FloQast program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med FloQast:**

1. I Azure-portalen på den **FloQast** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/floqast-tutorial/tutorial_floqast_samlbase.png)

1. På den **FloQast domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![FloQast domän och URL: er med enkel inloggning för information](./media/floqast-tutorial/tutorial_floqast_url.png)

     I den **identifierare** textrutan anger du ett URL: `https://go.floqast.com/`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![FloQast domän och URL: er med enkel inloggning för information](./media/floqast-tutorial/tutorial_floqast_url1.png)

     I rutan **Inloggnings-URL** anger du en URL: `https://go.floqast.com/login/sso`
     
1. FloQast program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. Följande skärmbild visar ett exempel på detta.
    
    ![Konfigurera enkel inloggning för attributet](./media/floqast-tutorial/tutorial_floqast_attribute.png)
    
1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ------------------- | -------------------- |    
    | FirstName           | user.givenname |
    | LastName        | user.surname |
    | E-post       | user.mail    |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning för Lägg till](./media/floqast-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning Addattb](./media/floqast-tutorial/tutorial_attribute_05.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Lämna **Namnrymd** tom.
    
    e. Klicka på **OK**.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara metadatafilen på datorn och utför följande steg:

    ![Länk för nedladdning av certifikatet](./media/floqast-tutorial/tutorial_floqast_certificate.png)

    a. Kontrollera **visa avancerade inställningar för signering av certifikat**.

    ![Certifikat-kontroll](./media/floqast-tutorial/tutorial_floqast_certificateassertion.png)

    b. Välj **signering alternativet** som **inloggning SAML-svar och försäkran**.

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning – knappen Spara](./media/floqast-tutorial/tutorial_general_400.png)
    
1. Att konfigurera enkel inloggning på **FloQast** sida, som du behöver skicka de hämtade **XML-Metadata för** till [FloQast supportteamet](mailto:support@floqast.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/floqast-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/floqast-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/floqast-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/floqast-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-floqast-test-user"></a>Skapa en FloQast testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i FloQast. Arbeta med [FloQast supportteamet](mailto:support@floqast.com) att lägga till användare i FloQast-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till FloQast.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon FloQast, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **FloQast**.

    ![Länken FloQast i listan med program](./media/floqast-tutorial/tutorial_floqast_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen FloQast i åtkomstpanelen du bör få automatiskt loggat in på ditt FloQast program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/floqast-tutorial/tutorial_general_01.png
[2]: ./media/floqast-tutorial/tutorial_general_02.png
[3]: ./media/floqast-tutorial/tutorial_general_03.png
[4]: ./media/floqast-tutorial/tutorial_general_04.png

[100]: ./media/floqast-tutorial/tutorial_general_100.png

[200]: ./media/floqast-tutorial/tutorial_general_200.png
[201]: ./media/floqast-tutorial/tutorial_general_201.png
[202]: ./media/floqast-tutorial/tutorial_general_202.png
[203]: ./media/floqast-tutorial/tutorial_general_203.png

