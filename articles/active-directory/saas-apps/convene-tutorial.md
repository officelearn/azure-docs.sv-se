---
title: 'Självstudier: Azure Active Directory-integration med Convene | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Convene.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2540ac46-1aea-496b-a8c5-575a2690f7db
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: jeedes
ms.openlocfilehash: 6ff502f46cfd96b767191d11e3fcc53eb1be81d2
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45608076"
---
# <a name="tutorial-azure-active-directory-integration-with-convene"></a>Självstudier: Azure Active Directory-integration med Convene

I den här självstudien får du lära dig hur du integrerar Convene med Azure Active Directory (AD Azure).

Integrera Convene med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Convene.
- Du kan aktivera användarna att automatiskt få loggat in på Convene (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Convene, behöver du följande objekt:

- En Azure AD-prenumeration
- En Convene enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Convene från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-convene-from-the-gallery"></a>Att lägga till Convene från galleriet
Om du vill konfigurera integreringen av Convene i Azure AD, som du behöver lägga till Convene från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Convene från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Convene**väljer **Convene** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Kalla i resultatlistan](./media/convene-tutorial/tutorial_convene_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Convene baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Convene är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Convene upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Convene, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Convene](#create-a-convene-test-user)**  – du har en motsvarighet för Britta Simon i Convene som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Convene program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Convene:**

1. I Azure-portalen på den **Convene** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/convene-tutorial/tutorial_convene_samlbase.png)

3. På den **kalla domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Kalla domän och URL: er enkel inloggning för information](./media/convene-tutorial/tutorial_convene_url.png)

    I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://portal.convene.me.uk/saml/acs/<UID>`

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Kalla domän och URL: er enkel inloggning för information](./media/convene-tutorial/tutorial_convene_url1.png)

    I den **inloggnings-URL** textrutan anger du ett URL: `https://portal.convene.me.uk/login`
     
    > [!NOTE] 
    > Svars-URL-värdet är inte verkliga. Uppdatera värdet med faktiska svars-URL. Kontakta [kalla klienten supportteamet](mailto:support@convene.me.uk) att hämta värdet.

5. Kalla program förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för detta.
    
    ![Konfigurera enkel inloggning attb](./media/convene-tutorial/tutorial_convene_attribute.png)

6. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/convene-tutorial/tutorial_convene_certificate.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/convene-tutorial/tutorial_general_400.png)
    
8. På den **kalla Configuration** klickar du på **konfigurera kalla** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Kalla konfiguration](./media/convene-tutorial/tutorial_convene_configure.png) 

9. Att konfigurera enkel inloggning på **Convene** sida, som du behöver skicka de hämtade **certifikat (Base64)**, **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL**till [Convene supportteamet](mailto:support@convene.me.uk). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/convene-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/convene-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/convene-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/convene-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-convene-test-user"></a>Skapa en Convene testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Convene. Kalla stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Convene om det inte finns ännu.

>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Convene supportteamet](mailto:support@convene.me.uk).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Convene.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Convene, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Convene**.

    ![Länken Convene i listan med program](./media/convene-tutorial/tutorial_convene_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Convene i åtkomstpanelen du bör få automatiskt loggat in på ditt Convene program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/convene-tutorial/tutorial_general_01.png
[2]: ./media/convene-tutorial/tutorial_general_02.png
[3]: ./media/convene-tutorial/tutorial_general_03.png
[4]: ./media/convene-tutorial/tutorial_general_04.png

[100]: ./media/convene-tutorial/tutorial_general_100.png

[200]: ./media/convene-tutorial/tutorial_general_200.png
[201]: ./media/convene-tutorial/tutorial_general_201.png
[202]: ./media/convene-tutorial/tutorial_general_202.png
[203]: ./media/convene-tutorial/tutorial_general_203.png

