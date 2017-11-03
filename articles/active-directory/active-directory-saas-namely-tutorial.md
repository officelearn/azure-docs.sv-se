---
title: "Självstudier: Azure Active Directory-integrering med nämligen | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och nämligen."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9541d5c4-4c82-4b5b-b01a-6a3f75a2b7a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 1d7e8fbcfc757853ab909bbb05522f3dc387715d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Självstudier: Azure Active Directory-integrering med nämligen

I kursen får lära du att integrera nämligen med Azure Active Directory (AD Azure).

Integrera nämligen med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till nämligen
- Du kan ge användarna automatiskt får loggat in på nämligen (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

Integrering med nämligen du behöver följande för att konfigurera Azure AD:

- En Azure AD-prenumeration
- En nämligen enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till nämligen från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-namely-from-the-gallery"></a>Att lägga till nämligen från galleriet
Du måste lägga till nämligen från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av nämligen i Azure AD.

**Utför följande steg för att lägga till nämligen från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **nämligen**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-namely-tutorial/tutorial_namely_search.png)

5. Välj i resultatpanelen **nämligen**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-namely-tutorial/tutorial_namely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med nämligen baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i motsvarande nämligen till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i nämligen upprättas.

I det kan tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD måste enkel inloggning med nämligen dig du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare nämligen](#creating-a-namely-test-user)**  – du har en motsvarighet för Britta Simon i nämligen som är länkade till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din nämligen program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med nämligen:**

1. I Azure-portalen på den **nämligen** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-namely-tutorial/tutorial_namely_samlbase.png)

3. På den **nämligen domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-namely-tutorial/tutorial_namely_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<subdomain>.namely.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [nämligen klienten supportteamet](https://www.namely.com/contact/) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-namely-tutorial/tutorial_namely_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-namely-tutorial/tutorial_general_400.png)

6. På den **nämligen Configuration** klickar du på **konfigurera nämligen** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-namely-tutorial/tutorial_namely_configure.png) 

7. I ett nytt webbläsarfönster, logga in på ditt nämligen företagets webbplats som administratör.

8. Klicka på i verktygsfältet högst upp **företagets**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-namely-tutorial/tutorial_namely_06.png) 

9. Klicka på fliken **Settings** (Inställningar).
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-namely-tutorial/tutorial_namely_07.png) 

10. Klicka på **SAML**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-namely-tutorial/tutorial_namely_08.png) 

11. På den **SAML inställningar** utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-namely-tutorial/tutorial_namely_09.png)
 
    a. Klicka på **aktivera SAML**. 

    b. I den **identitet providern SSO url** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.
    
    c. Öppna din hämtat certifikat i anteckningar, kopiera innehållet och klistrar in det i den **providern identitetscertifikat** textruta.
     
    d. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-namely-test-user"></a>Skapa en nämligen testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i nämligen.

**Utför följande steg för att skapa en användare som kallas Britta Simon i nämligen:**

1. Logga in på ditt nämligen företagets webbplats som administratör.

2. Klicka på i verktygsfältet högst upp **personer**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-namely-tutorial/tutorial_namely_10.png) 

3. Klicka på den **Directory** fliken.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-namely-tutorial/tutorial_namely_11.png) 

4. Klicka på **Lägg till ny Person**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-namely-tutorial/tutorial_namely_12.png)

5. På den **Lägg till ny Person** dialogrutan, utför följande steg:

    a. I den **Förnamn** textruta typen **Britta**.

    b. I den **efternamn** textruta typen **Simon**.

    c. I den **e-post** textruta typ av **e-postadress** av BrittaSimon.

    d. Klicka på **Spara**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till nämligen.

![Tilldela användare][200] 

**Så här tilldelar Britta Simon nämligen utföra följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **nämligen**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-namely-tutorial/tutorial_namely_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.

När du klickar på den nämligen panelen i åtkomstpanelen, du bör få automatiskt loggat in på ditt nämligen program

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-namely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-namely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-namely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-namely-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-namely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-namely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-namely-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-namely-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-namely-tutorial/tutorial_general_203.png

