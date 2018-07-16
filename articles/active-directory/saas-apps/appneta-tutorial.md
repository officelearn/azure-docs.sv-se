---
title: 'Självstudier: Azure Active Directory-integration med AppNeta Prestandaövervakaren | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AppNeta Prestandaövervakaren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 643a45fb-d6fc-4b32-b721-68899f8c7d44
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: jeedes
ms.openlocfilehash: ccedc0288e313df2639862a14078d8cad9951286
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054557"
---
# <a name="tutorial-azure-active-directory-integration-with-appneta-performance-monitor"></a>Självstudier: Azure Active Directory-integration med AppNeta Prestandaövervakaren

I den här självstudien får du lära dig hur du integrerar AppNeta Prestandaövervakaren med Azure Active Directory (AD Azure).

Integrera AppNeta Prestandaövervakaren med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till AppNeta Prestandaövervakaren.
- Du kan aktivera användarna att automatiskt få loggat in på AppNeta Prestandaövervakaren (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med AppNeta Prestandaövervakaren, behöver du följande objekt:

- En Azure AD-prenumeration
- En AppNeta Prestandaövervakaren enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till AppNeta Prestandaövervakaren från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Att lägga till AppNeta Prestandaövervakaren från galleriet
För att konfigurera integrering av AppNeta Prestandaövervakaren i Azure AD, som du behöver lägga till AppNeta Prestandaövervakaren från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till AppNeta Prestandaövervakaren från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **AppNeta Prestandaövervakaren**väljer **AppNeta Prestandaövervakaren** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![AppNeta Prestandaövervakaren i resultatlistan](./media/appneta-tutorial/tutorial_appneta_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med AppNeta Prestandaövervakaren baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i AppNeta Prestandaövervakaren är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i AppNeta Prestandaövervakaren upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med AppNeta Prestandaövervakaren, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare AppNeta Prestandaövervakaren](#create-an-appneta-performance-monitor-test-user)**  – du har en motsvarighet för Britta Simon i AppNeta Prestandaövervakaren som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt AppNeta Performance Monitor-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med AppNeta Prestandaövervakaren:**

1. I Azure-portalen på den **AppNeta Prestandaövervakaren** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/appneta-tutorial/tutorial_appneta_samlbase.png)

3. På den **AppNeta prestanda övervakaren domän och URL: er** avsnittet, utför följande steg:

    ![AppNeta prestanda övervakaren domän och URL: er med enkel inloggning för information](./media/appneta-tutorial/tutorial_appneta_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.pm.appneta.com`

    b. I den **identifierare** textrutan angett värde: `PingConnect`

    > [!NOTE] 
    > Inloggnings-URL-värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [AppNeta prestanda övervakaren klienten supportteamet](mailto:support@appneta.com) att hämta det här värdet. 

5. AppNeta Prestandaövervakaren programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program.

    ![Konfigurera enkel inloggning](./media/appneta-tutorial/attribute.png)

6. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i föregående bild och utför följande steg:
           
    | Attributnamn | Attributvärde |
    | ---------------| ----------------|
    | Förnamn| User.givenName|
    | Efternamn| User.surname|
    | e-post| User.userPrincipalName|
    | namn| User.userPrincipalName|
    | grupper   | User.assignedroles |
    | telefon| User.telephonenumber |
    | rubrik| User.jobtitle|

    > [!NOTE]
    > ”grupper” refererar till säkerhetsgruppen i Appneta som mappas till en roll i Azure AD. Se [detta](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) dokument som beskriver hur du skapar anpassade roller i Azure AD.
        
    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/appneta-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurera enkel inloggning](./media/appneta-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Lämna namnområde som tomt.
    
    e. Klicka på **OK**.  

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/appneta-tutorial/tutorial_appneta_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/appneta-tutorial/tutorial_general_400.png)

6. Att konfigurera enkel inloggning på **AppNeta Prestandaövervakaren** sida, som du behöver skicka de hämtade **XML-Metadata för** till [AppNeta Prestandaövervakaren supportteamet](mailto:support@appneta.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/appneta-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/appneta-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/appneta-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/appneta-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-appneta-performance-monitor-test-user"></a>Skapa en testanvändare AppNeta Prestandaövervakaren

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i AppNeta Prestandaövervakaren. AppNeta Prestandaövervakaren stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt AppNeta Prestandaövervakaren om det inte finns ännu.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [AppNeta Prestandaövervakaren supportteamet](mailto:support@appneta.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till AppNeta Prestandaövervakaren.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon AppNeta Prestandaövervakaren, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **AppNeta Prestandaövervakaren**.

    ![Länken AppNeta Performance Monitor i listan med program](./media/appneta-tutorial/tutorial_appneta_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen AppNeta Prestandaövervakaren i åtkomstpanelen du bör få automatiskt loggat in på programmets AppNeta Prestandaövervakaren.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/appneta-tutorial/tutorial_general_01.png
[2]: ./media/appneta-tutorial/tutorial_general_02.png
[3]: ./media/appneta-tutorial/tutorial_general_03.png
[4]: ./media/appneta-tutorial/tutorial_general_04.png

[100]: ./media/appneta-tutorial/tutorial_general_100.png

[200]: ./media/appneta-tutorial/tutorial_general_200.png
[201]: ./media/appneta-tutorial/tutorial_general_201.png
[202]: ./media/appneta-tutorial/tutorial_general_202.png
[203]: ./media/appneta-tutorial/tutorial_general_203.png

