---
title: 'Självstudier: Azure Active Directory-integration med Trello | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Trello.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.openlocfilehash: ef8e493816321d9aaffbf6e4870ff3e3a9e1180b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041399"
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>Självstudier: Azure Active Directory-integration med Trello

I den här självstudien får du lära dig hur du integrerar Trello med Azure Active Directory (AD Azure).

Integrera Trello med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Trello.
- Du kan aktivera användarna att automatiskt få loggat in på Trello (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Trello, behöver du följande objekt:

- En Azure AD-prenumeration
- Ett Trello enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Trello från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-trello-from-the-gallery"></a>Att lägga till Trello från galleriet
För att konfigurera integrering av Trello i Azure AD, som du behöver lägga till Trello från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Trello från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Trello**väljer **Trello** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Trello i resultatlistan](./media/trello-tutorial/tutorial_trello_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Trello baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Trello är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Trello upprättas.

I Trello, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Trello, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en Trello-testanvändare](#create-a-trello-test-user)**  – du har en motsvarighet för Britta Simon i Trello som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program i Trello.

>[!NOTE]
>Du bör få den **\<enterprise\>** dynamiska datafältet från Trello. Om du inte har värdet dynamiska datafältet, kontakta [Trello supportteamet](mailto:support@trello.com) att hämta det dynamiska datafältet för du enterprise.
    > 

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Trello:**

1. I Azure-portalen på den **Trello** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/trello-tutorial/tutorial_trello_samlbase.png)

3. På den **Trello domän och URL: er** om du vill konfigurera programmet i **IDP-initierad läge**, utför följande steg:

    ![Trello-domän och URL: er med enkel inloggning för information](./media/trello-tutorial/tutorial_trello_url.png)
    
    a. I den **identifierare** textrutan anger du följande URL: `https://trello.com/auth/saml/metadata`
    
    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://trello.com/auth/saml/consume/<enterprise>`

4. Om du vill konfigurera programmet i **SP initierade läge**, utför följande steg:

    ![Trello-domän och URL: er med enkel inloggning för information](./media/trello-tutorial/tutorial_trello_url1.png)

    a. Kontrollera **visa avancerade URL-inställningar**.

    b. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://trello.com/auth/saml/login/<enterprise>` 

5. Trello-program som förväntar SAML-intyg innehålla specifika attribut. Konfigurera följande attribut för det här programmet. Du kan hantera värdena för dessa attribut från den **”användarattribut”** av programmet. Följande skärmbild visar ett exempel för detta.

    ![Konfigurera enkel inloggning](./media/trello-tutorial/tutorial_trello_attribute.png)

6. På den **SAML-tokenattribut** dialogrutan för varje rad som visas i tabellen nedan, utför följande steg:
 
    | Attributnamn | Attributvärde |
    | --- | --- |
    | User.Email | User.Mail |
    | User.FirstName | User.givenName |
    | User.LastName | User.surname |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/trello-tutorial/tutorial_officespace_04.png)

    ![Konfigurera enkel inloggning](./media/trello-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden. 

    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **OK**. 

7. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/trello-tutorial/tutorial_trello_certificate.png) 

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/trello-tutorial/tutorial_general_400.png)
    
9. På den **Trello Configuration** klickar du på **konfigurera Trello** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Trello-konfiguration](./media/trello-tutorial/tutorial_trello_configure.png) 

10. Att konfigurera enkel inloggning på **Trello** sida, som du behöver att gå till den [Trello företagskonfiguration för enkel inloggning](https://trello.com/sso-configuration) sidan för att skicka de hämtade **certifikat (Base64)** och  **Enkel inloggning för tjänsten URL för SAML** till [Trello supportteamet](mailto:support@trello.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/trello-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/trello-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/trello-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/trello-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-trello-test-user"></a>Skapa en Trello-testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Trello. Trello stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Trello om det inte finns ännu.

>[!Note]
>Om du vill skapa en användare manuellt, kontakta [Trello supportteamet](mailto:support@trello.com).


### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Trello.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Trello, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Trello**.

    ![Trello-länk i listan med program](./media/trello-tutorial/tutorial_trello_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Trello-panel i åtkomstpanelen du bör få automatiskt loggat in på ditt Trello-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/trello-tutorial/tutorial_general_01.png
[2]: ./media/trello-tutorial/tutorial_general_02.png
[3]: ./media/trello-tutorial/tutorial_general_03.png
[4]: ./media/trello-tutorial/tutorial_general_04.png

[100]: ./media/trello-tutorial/tutorial_general_100.png

[200]: ./media/trello-tutorial/tutorial_general_200.png
[201]: ./media/trello-tutorial/tutorial_general_201.png
[202]: ./media/trello-tutorial/tutorial_general_202.png
[203]: ./media/trello-tutorial/tutorial_general_203.png

