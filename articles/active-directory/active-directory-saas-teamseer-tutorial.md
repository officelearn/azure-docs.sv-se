---
title: 'Självstudier: Azure Active Directory-integrering med TeamSeer | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TeamSeer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: b4fd5b65bf0f5ce90aa93f89aad4617976b3a490
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Självstudier: Azure Active Directory-integrering med TeamSeer

I kursen får lära du att integrera TeamSeer med Azure Active Directory (AD Azure).

Integrera TeamSeer med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till TeamSeer
- Du kan aktivera användarna att automatiskt hämta loggat in på TeamSeer (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med TeamSeer, behöver du följande:

- En Azure AD-prenumeration
- En TeamSeer enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till TeamSeer från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-teamseer-from-the-gallery"></a>Att lägga till TeamSeer från galleriet
Du måste lägga till TeamSeer från galleriet i listan över hanterade SaaS-appar för att konfigurera TeamSeer i till Azure AD-integrering.

**Utför följande steg för att lägga till TeamSeer från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **TeamSeer**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_search.png)

5. Välj i resultatpanelen **TeamSeer**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med TeamSeer baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i TeamSeer motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i TeamSeer upprättas.

I TeamSeer, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med TeamSeer, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare TeamSeer](#creating-a-teamseer-test-user)**  – du har en motsvarighet för Britta Simon i TeamSeer som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt TeamSeer program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med TeamSeer:**

1. I Azure-portalen på den **TeamSeer** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_samlbase.png)

3. På den **TeamSeer domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_url.png)

     I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://www.teamseer.com/<companyid>`

    > [!NOTE] 
    > Värdet är inte verkliga. Uppdatera värdet med det faktiska inloggnings-URL. Kontakta [TeamSeer klienten supportteamet](http://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) värdet hämtas. 
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-teamseer-tutorial/tutorial_general_400.png)

6. På den **TeamSeer Configuration** klickar du på **konfigurera TeamSeer** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_configure.png)

7. I en annan webbläsarfönster loggar du in på webbplatsen TeamSeer företag som administratör.

8. Gå till **HR Admin**.
   
    ![HR Admin](./media/active-directory-saas-teamseer-tutorial/ic789634.png "HR Admin")

9. Klicka på **installationsprogrammet**.
   
    ![Installationsprogrammet](./media/active-directory-saas-teamseer-tutorial/ic789635.png "installationen")

10. Klicka på **ställa in information om SAML provider**.
   
    ![Inställningar för SAML](./media/active-directory-saas-teamseer-tutorial/ic789636.png "SAML-inställningar")

11. Utför följande steg i avsnittet SAML-providern information:
   
    ![Inställningar för SAML](./media/active-directory-saas-teamseer-tutorial/ic789637.png "SAML-inställningar")   

    a. Klistra in den **inloggning tjänst-URL för enkel** i ett värde till den **URL** textruta.
          
    b. Öppna din Base64-kodade certifikatet i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **IdP offentliga certifikat** textruta.

12. Utför följande steg för att slutföra konfigurationen av SAML-providern:
    
    ![Inställningar för SAML](./media/active-directory-saas-teamseer-tutorial/ic789638.png "SAML-inställningar") 

    a. I den **testa e-postadresser**, Skriv test användarens e-postadress. 
  
    b. I den **utfärdaren** textruta skriver utfärdar-URL för tjänstleverantören. 
  
    c. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-teamseer-test-user"></a>Skapa en testanvändare TeamSeer

Om du vill aktivera Azure AD-användare kan logga in på TeamSeer måste de vara etablerade i till ShiftPlanning. När det gäller TeamSeer är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt **TeamSeer** företagets webbplats som administratör.

2. Utför följande steg:
   
    ![HR Admin](./media/active-directory-saas-teamseer-tutorial/ic789640.png "HR Admin")  
 
    a. Gå till **HR Admin \> användare**.
  
    b. Klicka på **kör guiden Ny användare**.

3. I den **användarinformation** avsnittet, utför följande steg:
   
    ![Användarinformation](./media/active-directory-saas-teamseer-tutorial/ic789641.png "användarinformation")

    a. Typ av **Förnamn**, **efternamn**, **användarnamn (e-postadress)** av en giltig AAD-konto som du vill etablera i till relaterade textrutor.
  
    b. Klicka på **Nästa**.

4. Följ den på skärmen för att lägga till en ny användare och klicka på **Slutför**.

>[!NOTE]
>Du kan använda något annat TeamSeer användarens konto skapas verktyg eller API: er som tillhandahålls av TeamSeer att etablera Azure AD-användarkonton. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TeamSeer.

![Tilldela användare][200] 

**Om du vill tilldela TeamSeer Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **TeamSeer**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Om du vill testa dina inställningar för enkel inloggning, öppna åtkomstpanelen. Mer information om åtkomstpanelen finns [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_203.png

