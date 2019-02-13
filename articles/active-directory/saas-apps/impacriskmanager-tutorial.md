---
title: 'Självstudier: Azure Active Directory-integrering med IMPAC Risk Manager | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och IMPAC Risk Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d77390e-898c-4258-a562-a1181dfe2880
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec119c9e11b21828b1b056fb97598ca7434cfc68
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56197717"
---
# <a name="tutorial-azure-active-directory-integration-with-impac-risk-manager"></a>Självstudier: Azure Active Directory-integrering med IMPAC Risk Manager

Lär dig hur du integrerar IMPAC Risk Manager med Azure Active Directory (AD Azure) i den här självstudien.

Integrera IMPAC Risk Manager med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till IMPAC Risk Manager.
- Du kan aktivera användarna att automatiskt få loggat in till IMPAC Risk Manager (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med IMPAC Risk Manager, behöver du följande objekt:

- En Azure AD-prenumeration
- En IMPAC Risk Manager enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till IMPAC Risk Manager från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-impac-risk-manager-from-the-gallery"></a>Att lägga till IMPAC Risk Manager från galleriet
För att konfigurera integrering av IMPAC Risk Manager till Azure AD, som du behöver lägga till IMPAC risken från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till IMPAC Risk Manager från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **IMPAC Risk Manager**väljer **IMPAC Risk Manager** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![IMPAC Risk Manager i resultatlistan](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med IMPAC Risk Manager baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i IMPAC Risk Manager till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i IMPAC Risk Manager upprättas.

I IMPAC Risk tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med IMPAC Risk Manager, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare IMPAC Risk Manager](#create-a-impac-risk-manager-test-user)**  – du har en motsvarighet för Britta Simon i IMPAC Risk Manager som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt IMPAC Risk Manager-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med IMPAC Risk Manager:**

1. I Azure-portalen på den **IMPAC Risk Manager** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_samlbase.png)

1. På den **IMPAC Risk Manager-domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i IDP-initierad läge:

    ![IMPAC Risk Manager-domän och URL: er med enkel inloggning för information](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_url_new.png)

    a. I den **identifierare** textrutan, ange ett värde som tillhandahålls av IMPAC

    b. I textrutan **Svars-URL** anger du en URL med följande mönster:
    | Miljö | URL-mönster |
    | ---------------|--------------- |    
    | För produktion |`https://www.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | För mellanlagring och utbildning  |`https://staging.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | För utveckling  |`https://dev.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | För QA |`https://QA.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | För testning |`https://test.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![IMPAC Risk Manager-domän och URL: er med enkel inloggning för information](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_url1_new.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster:
    | Miljö | URL-mönster |
    | ---------------|--------------- |    
    | För produktion |`https://www.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | För mellanlagring och utbildning  |`https://staging.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | För utveckling  |`https://dev.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | För QA |`https://QA.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | För testning |`https://test.riskmanager.co.nz/SSOv2/<ClientSuffix>`|

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [IMPAC Risk Manager-klienten supportteamet](mailto:rmsupport@Impac.co.nz) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/impacriskmanager-tutorial/tutorial_general_400.png)
    
1. På den **IMPAC Risk Manager Configuration** klickar du på **konfigurera IMPAC Risk Manager** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänstens URL, SAML entitets-ID** och **URL: en för utloggning** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_configure.png)

1. Att konfigurera enkel inloggning på **IMPAC Risk Manager** sida, som du behöver skicka de hämtade **Certificate(Base64)**, **URL för utloggning, SAML entitets-ID,** och  **Enkel inloggning för tjänsten URL för SAML** till [IMPAC Risk Manager supportteamet](mailto:rmsupport@Impac.co.nz). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/impacriskmanager-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/impacriskmanager-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/impacriskmanager-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/impacriskmanager-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-impac-risk-manager-test-user"></a>Skapa en testanvändare IMPAC Risk Manager

I det här avsnittet skapar du en användare som kallas Britta Simon i IMPAC Risk Manager. Arbeta med [IMPAC Risk Manager supportteamet](mailto:rmsupport@Impac.co.nz) att lägga till användare i IMPAC Risk Manager-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att ge åtkomst till IMPAC Risk Manager.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon till IMPAC Risk Manager, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **IMPAC Risk Manager**.

    ![Länken IMPAC Risk Manager i listan med program](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen IMPAC Risk Manager i åtkomstpanelen du bör få automatiskt loggat in på ditt IMPAC Risk Manager-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/impacriskmanager-tutorial/tutorial_general_01.png
[2]: ./media/impacriskmanager-tutorial/tutorial_general_02.png
[3]: ./media/impacriskmanager-tutorial/tutorial_general_03.png
[4]: ./media/impacriskmanager-tutorial/tutorial_general_04.png

[100]: ./media/impacriskmanager-tutorial/tutorial_general_100.png

[200]: ./media/impacriskmanager-tutorial/tutorial_general_200.png
[201]: ./media/impacriskmanager-tutorial/tutorial_general_201.png
[202]: ./media/impacriskmanager-tutorial/tutorial_general_202.png
[203]: ./media/impacriskmanager-tutorial/tutorial_general_203.png

