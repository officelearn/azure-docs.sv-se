---
title: 'Självstudier: Azure Active Directory-integration med Boomi | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: cf925e0e0e7b6b4c10b6b21d17214f91473a9026
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432957"
---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Självstudier: Azure Active Directory-integration med Boomi

I den här självstudien får du lära dig hur du integrerar Boomi med Azure Active Directory (AD Azure).

Integrera Boomi med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Boomi.
- Du kan aktivera användarna att automatiskt få loggat in på Boomi (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Boomi, behöver du följande objekt:

- En Azure AD-prenumeration
- En Boomi enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Boomi från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-boomi-from-the-gallery"></a>Att lägga till Boomi från galleriet
För att konfigurera integrering av Boomi i Azure AD, som du behöver lägga till Boomi från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Boomi från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Boomi**väljer **Boomi** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Boomi i resultatlistan](./media/boomi-tutorial/tutorial_boomi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Boomi baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Boomi är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Boomi upprättas.

I Boomi, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Boomi, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Boomi](#create-a-boomi-test-user)**  – du har en motsvarighet för Britta Simon i Boomi som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Boomi program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Boomi:**

1. I Azure-portalen på den **Boomi** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/boomi-tutorial/tutorial_boomi_samlbase.png)

1. På den **Boomi domän och URL: er** avsnittet, utför följande steg:

    ![Boomi domän och URL: er med enkel inloggning för information](./media/boomi-tutorial/tutorial_boomi_url.png)

    a. I den **identifierare** textrutan anger du ett URL: `https://platform.boomi.com/`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE] 
    > Svars-URL-värdet är inte verkliga. Uppdatera värdet med faktiska svars-URL. Kontakta [Boomi supportteamet](https://boomi.com/company/contact/) att hämta värdet.
 
1. Boomi program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. Följande skärmbild visar ett exempel för detta.
    
    ![Konfigurera enkel inloggning](./media/boomi-tutorial/tutorial_attribute.png)

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan för varje rad som visas i tabellen nedan, utför följande steg:

    | Attributnamn | Attributvärde |
    | -------------- | --------------- |
    | FEDERATION_ID | User.Mail |
    
    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.
    
    ![Konfigurera enkel inloggning](./media/boomi-tutorial/tutorial_officespace_04.png)
    
    ![Konfigurera enkel inloggning](./media/boomi-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.
    
    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **OK**.

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/boomi-tutorial/tutorial_boomi_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/boomi-tutorial/tutorial_general_400.png)

1. På den **Boomi Configuration** klickar du på **konfigurera Boomi** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Boomi konfiguration](./media/boomi-tutorial/tutorial_boomi_configure.png) 

1. Logga in på webbplatsen Boomi företag som en administratör i ett annat webbläsarfönster. 

1. Gå till **företagsnamn** och gå till **konfigurera**.

1. Klicka på den **SSO alternativ** fliken och utföra stegen nedan.

    ![Konfigurera enkel inloggning på App-sida](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Kontrollera **aktivera SAML enkel inloggning** kryssrutan.

    b. Klicka på **Import** att ladda upp det nedladdade certifikatet från Azure AD för att **providern identitetscertifikat**.
    
    c. I den **inloggnings-URL för identitetsprovider** textrutan anger du värdet för **SAML enkel inloggning för tjänst-URL** från konfigurationsfönstret i Azure AD-program.

    d. Som **Federation Id plats**väljer **Federation-Id är i FEDERATION_ID attributelement** alternativknappen. 

    e. Klicka på **spara** knappen.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/boomi-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/boomi-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/boomi-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/boomi-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-a-boomi-test-user"></a>Skapa en Boomi testanvändare

För att aktivera Azure AD-användare att logga in på Boomi, måste de etableras i Boomi. När det gäller Boomi är etablering en manuell aktivitet.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Utför följande steg för att etablera ett användarkonto:

1. Logga in på webbplatsen Boomi företag som administratör.

1. När du loggat in öppnar du **Användarhantering** och gå till **användare**.

    ![Användare](./media/boomi-tutorial/tutorial_boomi_001.png "användare")

1. Klicka på **+** ikonen och **Lägg till/Underhåll användarroller** dialogruta öppnas.

    ![Användare](./media/boomi-tutorial/tutorial_boomi_002.png "användare")

    ![Användare](./media/boomi-tutorial/tutorial_boomi_003.png "användare")

    a. I den **användarens e-postadress** textrutan typ e-postmeddelandet av användare som BrittaSimon@contoso.com.
    
    b. I den **Förnamn** textrutan Ange först namnet på användaren som Britta.

    c. I den **efternamn** textrutan anger efternamn för användaren som Simon.
    
    d. Ange användarens **Federations-ID**. Varje användare ha ett Federation-ID som unikt identifierar användare på kontot.
    
    e. Tilldela den **standardanvändare** du användaren rollen. Tilldela inte rollen Administratör eftersom som skulle ge honom normal atmosfär åtkomst samt enkel inloggning.
    
    f. Klicka på **OK**.
    
    > [!NOTE]
    > Användaren får inte en e-postmeddelande som Välkommen med ett lösenord som kan användas för att logga in på kontot AtomSphere eftersom sitt lösenord hanteras via identitetsprovidern. Du kan använda alla andra Boomi användare konto verktyg för att skapa eller API: er som tillhandahålls av Boomi att etablera AAD-användarkonton.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Boomi.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Boomi, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Boomi**.

    ![Länken Boomi i listan med program](./media/boomi-tutorial/tutorial_boomi_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Boomi i åtkomstpanelen du bör få automatiskt loggat in på ditt Boomi program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/boomi-tutorial/tutorial_general_01.png
[2]: ./media/boomi-tutorial/tutorial_general_02.png
[3]: ./media/boomi-tutorial/tutorial_general_03.png
[4]: ./media/boomi-tutorial/tutorial_general_04.png

[100]: ./media/boomi-tutorial/tutorial_general_100.png

[200]: ./media/boomi-tutorial/tutorial_general_200.png
[201]: ./media/boomi-tutorial/tutorial_general_201.png
[202]: ./media/boomi-tutorial/tutorial_general_202.png
[203]: ./media/boomi-tutorial/tutorial_general_203.png

