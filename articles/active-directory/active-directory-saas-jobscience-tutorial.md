---
title: "Självstudier: Azure Active Directory-integrering med Jobscience | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jobscience."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 66bec35a8f17482433dbf02827b90620d1cff378
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Självstudier: Azure Active Directory-integrering med Jobscience

I kursen får lära du att integrera Jobscience med Azure Active Directory (AD Azure).

Integrera Jobscience med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Jobscience
- Du kan aktivera användarna att automatiskt hämta loggat in på Jobscience (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Jobscience, behöver du följande:

- En Azure AD-prenumeration
- En Jobscience enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad här: [utvärderingsversion erbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Jobscience från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-jobscience-from-the-gallery"></a>Att lägga till Jobscience från galleriet
Du måste lägga till Jobscience från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Jobscience i Azure AD.

**Utför följande steg för att lägga till Jobscience från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Jobscience**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_search.png)

5. Välj i resultatpanelen **Jobscience**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Jobscience baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Jobscience motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Jobscience upprättas.

I Jobscience, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Jobscience, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Jobscience](#creating-a-jobscience-test-user)**  – du har en motsvarighet för Britta Simon i Jobscience som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Jobscience program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Jobscience:**

1. I Azure-portalen på den **Jobscience** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_samlbase.png)

3. På den **Jobscience domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_url.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska inloggnings-URL. Hämta det här värdet genom att [Jobscience klienten supportteamet](https://www.jobscience.com/support) eller från SSO-profil skapas som beskrivs senare i självstudierna. 
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-jobscience-tutorial/tutorial_general_400.png)

6. På den **Jobscience Configuration** klickar du på **konfigurera Jobscience** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_configure.png) 

7. Logga in på webbplatsen Jobscience företag som administratör.

8. Gå till **installationsprogrammet**.
   
   ![Installationsprogrammet](./media/active-directory-saas-jobscience-tutorial/IC784358.png "installationen")

9. I det vänstra navigeringsfönstret i den **administrera** klickar du på **domänhantering** Expandera avsnittet relaterade och klicka sedan på **min domän** att öppna den **min domän** sidan. 
   
   ![Min domän](./media/active-directory-saas-jobscience-tutorial/ic767825.png "min domän")

10. Om du vill verifiera att din domän har ställts in korrekt, se till att den är i ”**steg 4 distribueras till användarna**” och granska din ”**Mina Domäninställningar**”.

    ![Domänen som distribuerats till användaren](./media/active-directory-saas-jobscience-tutorial/ic784377.png "domän som har distribuerats till användaren")

11. Klicka på webbplatsen Jobscience företagets **säkerhetsåtgärder**, och klicka sedan på **inställningar för enkel inloggning**.
    
    ![Säkerhetsåtgärder](./media/active-directory-saas-jobscience-tutorial/ic784364.png "säkerhetsåtgärder")

12. I den **inställningar för enkel inloggning** avsnittet, utför följande steg:
    
    ![Enkel inloggning inställningar](./media/active-directory-saas-jobscience-tutorial/ic781026.png "enkel inloggning inställningar")
    
    a. Välj **SAML aktiverat**.

    b. Klicka på **Ny**.

13. På den **SAML enkel inloggning inställningen redigera** dialogrutan, utför följande steg:
    
    ![SAML enkel inloggning inställningen](./media/active-directory-saas-jobscience-tutorial/ic784365.png "SAML enkel inloggning inställningen")
    
    a. I den **namn** textruta, ange ett namn för din konfiguration.

    b. I **utfärdaren** textruta klistra in värdet för **SAML enhets-ID**, som du har kopierat från Azure-portalen.

    c. I den **enhets-Id** textruta typ`https://salesforce-jobscience.com`

    d. Klicka på **Bläddra** att ladda upp din Azure AD-certifikatet.

    e. Som **SAML identitetstyp**väljer **Assertion innehåller Federation-ID från användarobjektet**.

    f. Som **SAML identitet plats**väljer **identitet är i elementet NameIdentfier i instruktionen ämne**.

    g. I **identitet providern inloggnings-URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.

    h. I **identitet providern logga ut URL** textruta klistra in värdet för **Sign-Out URL**, som du har kopierat från Azure-portalen.

    Jag. Klicka på **Spara**.

14. I det vänstra navigeringsfönstret i den **administrera** klickar du på **domänhantering** Expandera avsnittet relaterade och klicka sedan på **min domän** att öppna den **min domän** sidan. 
    
    ![Min domän](./media/active-directory-saas-jobscience-tutorial/ic767825.png "min domän")

15. På den **min domän** sidan den **inloggningen sidan anpassning** klickar du på **redigera**.
    
    ![Inloggningssidan anpassning](./media/active-directory-saas-jobscience-tutorial/ic767826.png "inloggningssidan anpassning")

16. På den **inloggningen sidan anpassning** sidan den **Autentiseringstjänsten** avsnitt, namnet på din **SAML SSO inställningar** visas. Markera den och klicka sedan på **spara**.
    
    ![Inloggningssidan anpassning](./media/active-directory-saas-jobscience-tutorial/ic784366.png "inloggningssidan anpassning")

17. Få en SP initieras för enkel inloggning på inloggnings-URL, klicka på den **inställningar för enkel inloggning** i den **säkerhetsåtgärder** menyn avsnitt.

    ![Säkerhetsåtgärder](./media/active-directory-saas-jobscience-tutorial/ic784368.png "säkerhetsåtgärder")
    
    Klicka på SSO-profilen som du har skapat i steget ovan. Den här sidan visas för enkel inloggning på URL: en för ditt företag (till exempel [https://companyname.my.salesforce.com?so=companyid](https://companyname.my.salesforce.com?so=companyid).    

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-jobscience-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-jobscience-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-jobscience-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-jobscience-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-jobscience-test-user"></a>Skapa en testanvändare Jobscience

För att aktivera Azure AD-användare kan logga in på Jobscience etableras de i Jobscience. När det gäller Jobscience är etablering en manuell aktivitet.

>[!NOTE]
>Du kan använda något annat Jobscience användarens konto skapas verktyg eller API: er som tillhandahålls av Jobscience för att etablera Azure Active Directory användarkonton.
>  
        
**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på ditt **Jobscience** företagets webbplats som administratör.

2. Gå till installationsprogrammet.
   
   ![Installationsprogrammet](./media/active-directory-saas-jobscience-tutorial/ic784358.png "installationen")
3. Gå till **hantera användare \> användare**.
   
   ![Användare](./media/active-directory-saas-jobscience-tutorial/ic784369.png "användare")
4. Klicka på **ny användare**.
   
   ![Alla användare](./media/active-directory-saas-jobscience-tutorial/ic784370.png "alla användare")
5. På den **Redigera användare** dialogrutan, utför följande steg:
   
   ![Redigera användare](./media/active-directory-saas-jobscience-tutorial/ic784371.png "Redigera användare")
   
   a. I den **Förnamn** textruta, ange ett förnamn för användaren som Britta.
   
   b. I den **efternamn** textruta anger efternamn för användaren som Simon.
   
   c. I den **Alias** textruta skriver ett aliasnamn för användaren som brittas.

   d. I den **e-post** textruta typen e-postadressen för användaren som Brittasimon@contoso.com.

   e. I den **användarnamn** textruta, Skriv ett användarnamn för användaren som Brittasimon@contoso.com.

   f. I den **smeknamn** textruta typnamn nick för användaren som Simon.

   g. Klicka på **Spara**.

    
> [!NOTE]
> Azure Active Directory kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta sina konton innan den aktiveras.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Jobscience.

![Tilldela användare][200] 

**Om du vill tilldela Jobscience Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Jobscience**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Jobscience på åtkomstpanelen du bör få automatiskt loggat in på ditt Jobscience program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_203.png

