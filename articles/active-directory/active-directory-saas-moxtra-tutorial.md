---
title: 'Självstudier: Azure Active Directory-integrering med Moxtra | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Moxtra.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: 1d0b89cf0d090795a305b5bfcce070136cf0289a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352046"
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>Självstudier: Azure Active Directory-integrering med Moxtra

I kursen får lära du att integrera Moxtra med Azure Active Directory (AD Azure).

Integrera Moxtra med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Moxtra
- Du kan aktivera användarna att automatiskt hämta loggat in på Moxtra (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Moxtra, behöver du följande:

- En Azure AD-prenumeration
- En Moxtra enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Moxtra från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-moxtra-from-the-gallery"></a>Att lägga till Moxtra från galleriet
Du måste lägga till Moxtra från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Moxtra i Azure AD.

**Utför följande steg för att lägga till Moxtra från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Moxtra**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_search.png)

5. Välj i resultatpanelen **Moxtra**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Moxtra baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Moxtra motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Moxtra upprättas.

I Moxtra, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Moxtra, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Moxtra](#creating-a-moxtra-test-user)**  – du har en motsvarighet för Britta Simon i Moxtra som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Moxtra program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Moxtra:**

1. I Azure-portalen på den **Moxtra** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_samlbase.png)

3. På den **Moxtra domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_url.png)

    I den **inloggnings-URL** textruta Skriv en URL som: `https://www.moxtra.com/service/#login`

4. Moxtra program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel på den här konfigurationen. 

    ![Konfigurera enkel inloggning](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_attributes.png)
    
5. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ------------------- | -------------------- |    
    | Förnamn | User.givenName |
    | Efternamn | User.surname |
    | idpid    | < SAML enhets-ID > 

    > [!Note]
    > Värdet för **idpid** attributet är inte verkliga. Du kan hämta det faktiska värdet från **Snabbreferens** avsnittet **Moxtra Configuration**.
    
    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-moxtra-tutorial/tutorial_attribute_04.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-moxtra-tutorial/tutorial_attribute_05.png)

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. Klicka på **OK**.
    
5. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-moxtra-tutorial/tutorial_general_400.png)

7. På den **Moxtra Configuration** klickar du på **konfigurera Moxtra** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_configure.png) 

8. I ett nytt webbläsarfönster inloggning till webbplatsen Moxtra företag som administratör.

9. Klicka på i verktygsfältet på vänster **administratörskonsolen > SAML enkel inloggning**, och klicka sedan på **ny**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png) 

10. På den **SAML** utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. I den **namn** textruta, ange ett namn för din konfiguration (t.ex.: *SAML*). 
  
    b. I den **IdP enhets-ID** textruta klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen. 
 
    c. I **Inloggningswebbadressen** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen. 
 
    d. I den **AuthnContextClassRef** textruta typen **urn: oasis: namn: tc: SAML:2.0:ac:classes:Password**. 
 
    e. I den **NameID Format** textruta typen **urn: oasis: namn: tc: SAML:1.1:nameid-format: e-postadress**. 
 
    f. Öppna certifikat som du har hämtat från Azure-portalen i anteckningar, kopiera innehållet och klistrar in det i den **certifikat** textruta.    
 
    g. Skriv din e-postdomän SAML i textrutan SAML e-domän.    
  
    >[!NOTE]
    >Klicka för att se stegen för att verifiera domänen på ”**jag**” nedan.

    h. Klicka på **uppdatering**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-moxtra-test-user"></a>Skapa en testanvändare Moxtra

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Moxtra.

**Utför följande steg för att skapa en användare som kallas Britta Simon i Moxtra:**

1. Logga in på webbplatsen Moxtra företag som administratör.

2. Klicka på i verktygsfältet på vänster **administratörskonsolen > Användarhantering**, och sedan **Lägg till användare**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png) 

3. På den **Lägg till användare** dialogrutan, utför följande steg:
  
    a. I den **Förnamn** textruta typen **Britta**.
  
    b. I den **efternamn** textruta typen **Simon**.
  
    c. I den **e-post** textruta typen Brittas e-postadressen samma som på Azure-portalen.
  
    d. I den **Division** textruta typen **Dev**.
  
    e. I den **avdelning** textruta typen **IT**.
  
    f. Välj **administratör**.
  
    g. Klicka på **Lägg till**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Moxtra.

![Tilldela användare][200] 

**Om du vill tilldela Moxtra Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Moxtra**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Moxtra på åtkomstpanelen du bör få automatiskt loggat in på ditt Moxtra program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png

