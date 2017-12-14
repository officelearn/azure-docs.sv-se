---
title: "Självstudier: Azure Active Directory-integrering med SuccessFactors | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SuccessFactors."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: b9545599b4ac02927c38931777a3cee623a4e940
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Självstudier: Azure Active Directory-integrering med SuccessFactors

I kursen får lära du att integrera SuccessFactors med Azure Active Directory (AD Azure).

Integrera SuccessFactors med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SuccessFactors.
- Du kan aktivera användarna att automatiskt hämta loggat in på SuccessFactors (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med SuccessFactors, behöver du följande:

- En Azure AD-prenumeration
- En SuccessFactors enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SuccessFactors från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-successfactors-from-the-gallery"></a>Att lägga till SuccessFactors från galleriet
Du måste lägga till SuccessFactors från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SuccessFactors i Azure AD.

**Utför följande steg för att lägga till SuccessFactors från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SuccessFactors**väljer **SuccessFactors** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![SuccessFactors i resultatlistan](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med SuccessFactors baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i SuccessFactors motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SuccessFactors upprättas.

I SuccessFactors, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med SuccessFactors, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare SuccessFactors](#create-a-successfactors-test-user)**  – du har en motsvarighet för Britta Simon i SuccessFactors som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt SuccessFactors program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SuccessFactors:**

1. I Azure-portalen på den **SuccessFactors** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_samlbase.png)

3. På den **SuccessFactors domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och SuccessFactors domän med enkel inloggning information](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. I den **identifierare** textruta Skriv en URL med följande mönster:
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. I den **Reply URL** textruta Skriv en URL med följande mönster:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|
         
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Kontakta [SuccessFactors klienten supportteamet](https://www.successfactors.com/en_us/support.html) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-successfactors-tutorial/tutorial_general_400.png)
    
6. På den **SuccessFactors Configuration** klickar du på **konfigurera SuccessFactors** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_configure.png) 

7. I en annan webbläsarfönstret, logga in på ditt **SuccessFactors administrationsportalen** som administratör.
    
8. Besök **programsäkerhet** och inbyggd **enkel inloggning på funktionen**. 

9. Placera alla värden i den **återställa Token** och på **spara Token** att aktivera SAML SSO.
   
    ![Konfigurera enkel inloggning på app-sida][11]

    > [!NOTE] 
    > Det här värdet används som växeln på/av. Om inget värde har sparats är SAML SSO Aktiverat. Om ett tomt värde har sparats är SAML SSO OFF.

10. Inbyggd nedan skärmbild och utföra följande åtgärder:
   
    ![Konfigurera enkel inloggning på app-sida][12]
   
    a. Välj den **SAML v2 SSO** alternativknapp
   
    b. Ange den **namn för SAML part garanterar**(till exempel SAML utfärdaren + företagsnamn).
   
    c. I den **utfärdar-URL** textruta klistra in den **SAML enhets-ID** värde som du har kopierat från Azure-portalen.
   
    d. Välj **svar (kunden genereras/IdP/AP)** som **kräver obligatorisk signatur**.
   
    e. Välj **aktiverat** som **aktivera SAML-flaggan**.
   
    f. Välj **nr** som **signatur på förfrågan inloggning (SA genereras/SP/RP)**.
   
    g. Välj **webbläsare/Post-profilen** som **SAML profil**.
   
    h. Välj **nr** som **genomdriva giltighetsperioden för certifikatet**.
   
    Jag. Kopiera innehållet i den hämta certifikatfilen från Azure-portalen och klistrar in det i den **SAML verifiera certifikatet** textruta.

    > [!NOTE] 
    > Certifikatets innehåll måste börja certifikat och certifikat sluttaggar.

11. Gå till SAML V2 och utför följande steg:
   
    ![Konfigurera enkel inloggning på app-sida][13]
   
    a. Välj **Ja** som **stöder SP-initierad globala logga ut**.
   
    b. I den **globala logga ut tjänst-URL (LogoutRequest mål)** textruta klistra in den **Sign-Out URL** värde som du har kopierat formuläret Azure-portalen.
   
    c. Välj **nr** som **kräver sp måste kryptera alla NameID elementet**.
   
    d. Välj **Ospecificerad** som **NameID Format**.
   
    e. Välj **Ja** som **aktivera sp initierade inloggning (AuthnRequest)**.
   
    f. I den **begäran om att skicka som företagsomfattande utgivaren** textruta klistra in **SAML inloggning tjänst-URL för enkel** värde som du har kopierat från Azure-portalen.

12. Utför de här stegen om du vill göra inloggningen användarnamn skiftlägeskänsligt.
   
    ![Konfigurera enkel inloggning][29]
    
    a. Besök **Företagsinställningar**(nästan längst ned).
   
    b. Markera kryssrutan bredvid **aktivera icke skiftlägeskänslig användarnamn**.
   
    c.Click **spara**.
   
    > [!NOTE] 
    > Om du försöker göra detta kontrollerar systemet om skapas ett dublettnamn i SAML-inloggning. Till exempel om kunden har användarnamn Användare1 och Användare1. Tar bort skiftlägeskänslighet gör dessa dubbletter. Systemet får du ett felmeddelande och aktivera inte funktionen. Kunden måste ändra något av användarnamnet så att du har stavat olika.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-successfactors-test-user"></a>Skapa en testanvändare SuccessFactors

Om du vill aktivera Azure AD-användare kan logga in på SuccessFactors etableras de i SuccessFactors.  
När det gäller SuccessFactors är etablering en manuell aktivitet.

För att få användare som skapats i SuccessFactors, måste du kontakta den [SuccessFactors supportteam](https://www.successfactors.com/en_us/support.html).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SuccessFactors.

![Tilldela rollen][200] 

**Om du vill tilldela SuccessFactors Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SuccessFactors**.

    ![Länken SuccessFactors i listan med program](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen SuccessFactors på åtkomstpanelen du bör få automatiskt loggat in på ditt SuccessFactors program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png

[100]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_203.png

