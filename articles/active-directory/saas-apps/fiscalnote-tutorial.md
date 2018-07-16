---
title: 'Självstudier: Azure Active Directory-integration med FiscalNote | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FiscalNote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 55274f26-be7e-4514-964c-7186ecb55c4a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 1eb11b52a89e25603939af9ec42d922d6e7ac0df
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047417"
---
# <a name="tutorial-azure-active-directory-integration-with-fiscalnote"></a>Självstudier: Azure Active Directory-integration med FiscalNote

I den här självstudien får du lära dig hur du integrerar FiscalNote med Azure Active Directory (AD Azure).

Integrera FiscalNote med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till FiscalNote.
- Du kan aktivera användarna att automatiskt få loggat in på FiscalNote (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med FiscalNote, behöver du följande objekt:

- En Azure AD-prenumeration
- En FiscalNote enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till FiscalNote från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-fiscalnote-from-the-gallery"></a>Att lägga till FiscalNote från galleriet
För att konfigurera integrering av FiscalNote i Azure AD, som du behöver lägga till FiscalNote från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till FiscalNote från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **FiscalNote**väljer **FiscalNote** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![FiscalNote i resultatlistan](./media/fiscalnote-tutorial/tutorial_fiscalnote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med FiscalNote baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i FiscalNote är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i FiscalNote upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med FiscalNote, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare FiscalNote](#create-a-fiscalnote-test-user)**  – du har en motsvarighet för Britta Simon i FiscalNote som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt FiscalNote program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med FiscalNote:**

1. I Azure-portalen på den **FiscalNote** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/fiscalnote-tutorial/tutorial_fiscalnote_samlbase.png)

3. På den **FiscalNote domän och URL: er** avsnittet, utför följande steg:

    ![FiscalNote domän och URL: er med enkel inloggning för information](./media/fiscalnote-tutorial/tutorial_fiscalnote_url.png)
    
    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<InstanceName>.fiscalnote.com/login?client=<ClientID>&redirect_uri=https://app.fiscalnote.com/saml-login.html&audience=https://api.fiscalnote.com/&connection=<CONNECTION_NAME>&response_type=id_token%20token`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `urn:auth0:fiscalnote:<CONNECTIONNAME>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [FiscalNote klienten supportteamet](mailto:support@fiscalnote.com) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Raw)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/fiscalnote-tutorial/tutorial_fiscalnote_certificate.png)

5. FiscalNote programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program.

    ![Konfigurera enkel inloggning](./media/fiscalnote-tutorial/tutorial_attribute.png)

6. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i föregående bild och utför följande steg:
           
    | Attributnamn | Attributvärde |
    | ---------------| ----------------|
    | namn | User.userPrincipalName|
    | givenName| User.givenName|
    | familyName| User.surname|
    | e-post| User.Mail|
    
    a. Ta bort befintliga attribut och lägga till nya attribut. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/fiscalnote-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurera enkel inloggning](./media/fiscalnote-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Lämna namnområde som tomt.
    
    e. Klicka på **OK**.

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/fiscalnote-tutorial/tutorial_general_400.png)

8. På den **FiscalNote Configuration** klickar du på **konfigurera FiscalNote** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![FiscalNote konfiguration](./media/fiscalnote-tutorial/tutorial_fiscalnote_configure.png) 

9. Att konfigurera enkel inloggning på **FiscalNote** sida, som du behöver skicka de hämtade **Certificate(Raw), URL: en för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** till [FiscalNote supportteam](mailto:support@fiscalnote.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/fiscalnote-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/fiscalnote-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/fiscalnote-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/fiscalnote-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-fiscalnote-test-user"></a>Skapa en FiscalNote testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i FiscalNote. FiscalNote stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt FiscalNote om det inte finns ännu.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [FiscalNote supportteamet](mailto:support@fiscalnote.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till FiscalNote.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon FiscalNote, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **FiscalNote**.

    ![Länken FiscalNote i listan med program](./media/fiscalnote-tutorial/tutorial_fiscalnote_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen FiscalNote i åtkomstpanelen du bör få automatiskt loggat in på ditt FiscalNote program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/fiscalnote-tutorial/tutorial_general_01.png
[2]: ./media/fiscalnote-tutorial/tutorial_general_02.png
[3]: ./media/fiscalnote-tutorial/tutorial_general_03.png
[4]: ./media/fiscalnote-tutorial/tutorial_general_04.png

[100]: ./media/fiscalnote-tutorial/tutorial_general_100.png

[200]: ./media/fiscalnote-tutorial/tutorial_general_200.png
[201]: ./media/fiscalnote-tutorial/tutorial_general_201.png
[202]: ./media/fiscalnote-tutorial/tutorial_general_202.png
[203]: ./media/fiscalnote-tutorial/tutorial_general_203.png

