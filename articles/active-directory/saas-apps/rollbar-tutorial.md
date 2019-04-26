---
title: 'Självstudier: Azure Active Directory-integrering med Rollbar | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Rollbar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/04/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ebedddf5e62e41bd0ef2d4c426df448a81a1e2d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60349799"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Självstudier: Azure Active Directory-integrering med Rollbar

I den här självstudien får du lära dig hur du integrerar Rollbar med Azure Active Directory (AD Azure).

Integrera Rollbar med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Rollbar.
- Du kan aktivera användarna att automatiskt få loggat in på Rollbar (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Rollbar, behöver du följande objekt:

- En Azure AD-prenumeration
- En Rollbar enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Rollbar från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-rollbar-from-the-gallery"></a>Att lägga till Rollbar från galleriet
För att konfigurera integrering av Rollbar i Azure AD, som du behöver lägga till Rollbar från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Rollbar från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Rollbar**väljer **Rollbar** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Rollbar i resultatlistan](./media/rollbar-tutorial/tutorial_rollbar_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Rollbar baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Rollbar är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Rollbar upprättas.

I Rollbar, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Rollbar, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Rollbar](#create-a-rollbar-test-user)**  – du har en motsvarighet för Britta Simon i Rollbar som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Rollbar program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Rollbar:**

1. I Azure-portalen på den **Rollbar** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/rollbar-tutorial/tutorial_rollbar_samlbase.png)

1. På den **Rollbar domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Rollbar domän och URL: er med enkel inloggning för information](./media/rollbar-tutorial/tutorial_rollbar_url.png)

    a. I den **identifierare** textrutan anger du URL: `https://saml.rollbar.com`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://rollbar.com/<accountname>/saml/sso/azure/`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Rollbar domän och URL: er med enkel inloggning för information](./media/rollbar-tutorial/tutorial_rollbar_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://rollbar.com/<accountname>/saml/login/azure/`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Kontakta [Rollbar klienten supportteamet](mailto:support@rollbar.com) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/rollbar-tutorial/tutorial_rollbar_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/rollbar-tutorial/tutorial_general_400.png)
    
1. I ett annat webbläsarfönster logga du in på webbplatsen Rollbar företag som administratör.

1. Klicka på den **profilinställningar** i övre högra hörnet och sedan på **kontonamn inställningar**.
    
    ![Konfiguration](./media/rollbar-tutorial/general.png)

1. Klicka på **identitetsprovider** under säkerhet.

    ![Konfiguration](./media/rollbar-tutorial/configure1.png)

1. I den **SAML-identitetsprovider** avsnittet, utför följande steg:
    
    ![Konfiguration](./media/rollbar-tutorial/configure2.png)

    a. Välj **AZURE** från den **SAML-identitetsprovider** listrutan.

    b. Öppna metadatafilen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **SAML-Metadata** textrutan.

    c. Klicka på **Spara**.

1. När du klickar på Spara knappen skärmen blir så här:
    
    ![Konfiguration](./media/rollbar-tutorial/configure3.png)
    > [!NOTE] 
    > För att slutföra följande steg, måste du först lägga till dig själv som en användare Rollbar-app i Azure.
    a. Om du vill kräva att alla användare att autentisera via Azure, och klicka sedan på **logga in via din identitetsprovider** återautentisera via Azure.  

    b.  När du är tillbaka till skärmen, väljer den **kräver inloggning via SAML-identitetsprovider** kryssrutan.

    b. Klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/rollbar-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/rollbar-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/rollbar-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/rollbar-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-rollbar-test-user"></a>Skapa en Rollbar testanvändare

Om du vill aktivera Azure AD-användare att logga in på Rollbar, måste de etableras i Rollbar. När det gäller Rollbar är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen Rollbar företag som administratör.

1. Klicka på den **profilinställningar** i övre högra hörnet och sedan på **kontonamn inställningar**.

    ![Användare](./media/rollbar-tutorial/general.png)

1. Klicka på **Användare**.
    
    ![Lägga till medarbetare](./media/rollbar-tutorial/user1.png)

1. Klicka på **bjuda in gruppmedlemmar**.

    ![Bjuda in](./media/rollbar-tutorial/user2.png)

1. I textrutan, anger du namnet på användaren som **brittasimon\@contoso.com** och klicka på **Lägg till/inbjudan**.

    ![Bjuda in](./media/rollbar-tutorial/user3.png)

1. Användaren får en inbjudan och efter att du godkänt den han/hon har skapats i systemet.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Rollbar.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Rollbar, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Rollbar**.

    ![Länken Rollbar i listan med program](./media/rollbar-tutorial/tutorial_rollbar_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Rollbar i åtkomstpanelen du bör få automatiskt loggat in på ditt Rollbar program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rollbar-tutorial/tutorial_general_01.png
[2]: ./media/rollbar-tutorial/tutorial_general_02.png
[3]: ./media/rollbar-tutorial/tutorial_general_03.png
[4]: ./media/rollbar-tutorial/tutorial_general_04.png

[100]: ./media/rollbar-tutorial/tutorial_general_100.png

[200]: ./media/rollbar-tutorial/tutorial_general_200.png
[201]: ./media/rollbar-tutorial/tutorial_general_201.png
[202]: ./media/rollbar-tutorial/tutorial_general_202.png
[203]: ./media/rollbar-tutorial/tutorial_general_203.png

