---
title: "Självstudier: Azure Active Directory-integrering med Allocadia | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Allocadia."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c415fc55-6dc1-49f2-a8a2-2fc6e3790d65
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: jeedes
ms.openlocfilehash: 8e97c365383ecdb72cc1cd449b522b75875fc1db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-allocadia"></a>Självstudier: Azure Active Directory-integrering med Allocadia

I kursen får lära du att integrera Allocadia med Azure Active Directory (AD Azure).

Integrera Allocadia med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Allocadia
- Du kan aktivera användarna att automatiskt hämta loggat in på Allocadia (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Allocadia, behöver du följande:

- En Azure AD-prenumeration
- En Allocadia enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Allocadia från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-allocadia-from-the-gallery"></a>Att lägga till Allocadia från galleriet
Du måste lägga till Allocadia från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Allocadia i Azure AD.

**Utför följande steg för att lägga till Allocadia från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Allocadia**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_search.png)

5. Välj i resultatpanelen **Allocadia**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Allocadia baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Allocadia motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Allocadia upprättas.

I Allocadia, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Allocadia, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Allocadia](#creating-an-allocadia-test-user)**  – du har en motsvarighet för Britta Simon i Allocadia som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Allocadia program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Allocadia:**

1. I Azure-portalen på den **Allocadia** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_samlbase.png)

3. På den **Allocadia domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: 
       
     För testmiljö-`https://na2standby.allocadia.com`
    
     För produktionsmiljö-`https://na2.allocadia.com`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: 
    
     För testmiljö-`https://na2standby.allocadia.com/allocadia/saml/SSO`
    
     För produktionsmiljö-`https://na2.allocadia.com/allocadia/saml/SSO`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare och Reply-URL. Kontakta [Allocadia supportteamet](mailTo:support@allocadia.com) att hämta dessa värden.

4. Allocadia program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel på den här konfigurationen. 

    ![Konfigurera enkel inloggning](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_attributes.png)
    
5. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden och utför följande steg:
    
    | Attributets namn | Attributvärdet |
    | ------------------- | -------------------- |    
    | Förnamn | User.givenName |
    | Efternamn | User.surname |
    | E-post | User.Mail |
    
    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-allocadia-tutorial/tutorial_attribute_04.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-allocadia-tutorial/tutorial_attribute_05.png)

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
 
    d. Klicka på **OK**.



6. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_certificate.png) 


7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-allocadia-tutorial/tutorial_general_400.png)

8. Konfigurera enkel inloggning på **Allocadia** sida, måste du skicka den hämtade **XML-Metadata för** till [Allocadia supportteamet](mailTo:support@allocadia.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-allocadia-test-user"></a>Skapa en testanvändare Allocadia

Programmet stöder bara i tid användaretablering. Efter autentisering skapas användare i programmet automatiskt.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Allocadia.

![Tilldela användare][200] 

**Om du vill tilldela Allocadia Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Allocadia**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Allocadia på åtkomstpanelen du bör få automatiskt loggat in på ditt Allocadia program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_203.png

