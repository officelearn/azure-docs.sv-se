---
title: "Självstudier: Azure Active Directory-integrering med SuccessFactors | Microsoft Docs"
description: "Lär dig hur du använder SuccessFactors med Azure Active Directory för att aktivera enkel inloggning, Automatisk etablering och mycket mer!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: e85a38ccbe25263ac42bc76351416b023fb77c87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Självstudier: Azure Active Directory-integrering med SuccessFactors
Syftet med den här kursen är att visa dig hur du integrerar SuccessFactors med Azure Active Directory (AD Azure).

Integrera SuccessFactors med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SuccessFactors
* Du kan aktivera användarna att automatiskt hämta loggat in på SuccessFactors (Single Sign-On) med sina Azure AD-konton
* Du kan hantera dina konton i en central plats – den klassiska Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav
För att konfigurera Azure AD-integrering med SuccessFactors, behöver du följande:

* En giltig Azure-prenumeration
* En klient i SuccessFactors

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.
> 
> 

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

* Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
* Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
Syftet med den här kursen är att du ska testa Azure AD enkel inloggning i en testmiljö.

Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SuccessFactors från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-successfactors-from-the-gallery"></a>Att lägga till SuccessFactors från galleriet
Du måste lägga till SuccessFactors från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SuccessFactors i Azure AD.

**Utför följande steg för att lägga till SuccessFactors från galleriet:**

1. I den klassiska Azure-portalen på panelen vänstra navigeringsfönstret klickar du på **Active Directory**.
   
    ![Konfigurera enkel inloggning][1]
2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.
3. Klicka för att öppna vyn program i vyn directory **program** på huvudmenyn.
   
    ![Konfigurera enkel inloggning][2]
4. Klicka på **Lägg till** längst ned på sidan.
   
    ![Program][3]
5. På den **vad vill du göra** dialogrutan klickar du på **lägga till ett program från galleriet**.
   
    ![Konfigurera enkel inloggning][4]
6. I den **sökrutan**, typen **SuccessFactors**.
   
    ![Konfigurera enkel inloggning][5]
7. Välj i resultatpanelen **SuccessFactors**, och klicka sedan på **Slutför** lägga till programmet.
   
    ![Konfigurera enkel inloggning][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
Syftet med det här avsnittet är att visa dig hur du konfigurerar och testa Azure AD enkel inloggning med SuccessFactors baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD motsvarighet användaren i SuccessFactors till en användare i Azure AD är okänt. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SuccessFactors upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i SuccessFactors.

Om du vill konfigurera och testa Azure AD enkel inloggning med SuccessFactors, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare SuccessFactors](#creating-a-successfactors-test-user)**  – du har en motsvarighet för Britta Simon i SuccessFactors som är kopplad till Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning
I det här avsnittet Aktivera Azure AD enkel inloggning i den klassiska portalen och konfigurera enkel inloggning i ditt SuccessFactors program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SuccessFactors:**

1. I den klassiska Azure-portalen på den **SuccessFactors** integreringssidan för programmet, klickar du på **Konfigurera enkel inloggning** att öppna den **Konfigurera enkel inloggning** dialogrutan.
   
    ![Konfigurera enkel inloggning][7]
2. På den **hur vill du att användarna kan logga in på SuccessFactors** väljer **Microsoft Azure AD enkel inloggning**, och klicka sedan på **nästa**.
   
    ![Konfigurera enkel inloggning][8]
3. På den **konfigurera App-URL** , utför följande steg, och klickar sedan på **nästa**.
   
    ![Konfigurera enkel inloggning][9]
   
    a. I den **logga URL** textruta, ange ett URL-Adressen med något av följande mönster: 
   
    |  |
    | --- |
    | `https://<company name>.successfactors.com/<company name>` |
    | `https://<company name>.sapsf.com/<company name>` |
    | `https://<company name>.successfactors.eu/<company name>` |
    | `https://<company name>.sapsf.eu` |
   
    b. I den **Reply URL** textruta, ange ett URL-Adressen med något av följande mönster: 
   
    |  |
    | --- |
    | `https://<company name>.successfactors.com/<company name>` |
    | `https://<company name>.sapsf.com/<company name>` |
    | `https://<company name>.successfactors.eu/<company name>` |
    | `https://<company name>.sapsf.eu` |
    | `https://<company name>.sapsf.eu/<company name>` |
   
    c. Klicka på **Nästa**. 

    > [!NOTE]
    > Observera att detta inte är verkliga värden. Du måste uppdatera dessa värden med de faktiska logga URL och Reply-URL. För att få dessa värden kan kontakta [SuccessFactors supportteam](https://www.successfactors.com/en_us/support.html).

1. På den **Konfigurera enkel inloggning på SuccessFactors** klickar du på **hämta certifikat**, och sedan spara certifikatfilen lokalt på datorn.
   
    ![Konfigurera enkel inloggning][10]

2. Logga in i ett annat webbläsarfönster din **SuccessFactors administrationsportalen** som administratör.

3. Besök **programsäkerhet** och inbyggd **enkel inloggning på funktionen**. 

4. Placera alla värden i den **återställa Token** och på **spara Token** att aktivera SAML SSO.
   
    ![Konfigurera enkel inloggning på app-sida][11]

    > [!NOTE] 
    > Det här värdet används bara som växeln på/av. Om inget värde har sparats är SAML SSO Aktiverat. Om ett tomt värde har sparats är SAML SSO OFF.

1. Inbyggd nedan skärmbild och utför följande åtgärder.
   
    ![Konfigurera enkel inloggning på app-sida][12]
   
    a. Välj den **SAML v2 SSO** alternativknapp
   
    b. Ange SAML garanterar part Name(e.g. SAml issuer + company name).
   
    c. I den **SAML utfärdaren** textruta ange värdet för **utfärdar-URL** från guiden Konfigurera program för Azure AD.
   
    d. Välj **svar (kunden genereras/IdP/AP)** som **kräver obligatorisk signatur**.
   
    e. Välj **aktiverat** som **aktivera SAML-flaggan**.
   
    f. Välj **nr** som **signatur på förfrågan inloggning (SA genereras/SP/RP)**.
   
    g. Välj **webbläsare/Post-profilen** som **SAML profil**.
   
    h. Välj **nr** som **genomdriva giltighetsperioden för certifikatet**.
   
    Jag. Kopiera innehållet i filen hämtat certifikat och klistrar in det i den **SAML verifiera certifikatet** textruta.

    > [!NOTE] 
    > Certifikatets innehåll måste börja certifikat och certifikat sluttaggar.

1. Gå till SAML V2 och utför följande steg:
   
    ![Konfigurera enkel inloggning på app-sida][13]
   
    a. Välj **Ja** som **stöder SP-initierad globala logga ut**.
   
    b. I den **globala logga ut tjänst-URL (LogoutRequest mål)** textruta ange värdet för **Remote logga ut URL** från guiden Konfigurera program för Azure AD.
   
    c. Välj **nr** som **kräver sp måste kryptera alla NameID elementet**.
   
    d. Välj **Ospecificerad** som **NameID Format**.
   
    e. Välj **Ja** som **aktivera sp initierade inloggning (AuthnRequest)**.
   
    f. I den **begäran om att skicka som företagsomfattande utgivaren** textruta ange värdet för **Remote inloggnings-URL** från guiden Konfigurera program för Azure AD.
2. Utför de här stegen om du vill göra inloggningen användarnamn inte skiftlägeskänsligt.
   
    a. Besök **Företagsinställningar**(nästan längst ned).
   
    b. Markera kryssrutan bredvid **aktivera icke skiftlägeskänslig användarnamn**.
   
    c.Click **spara**.
   
    ![Konfigurera enkel inloggning][29]

    > [!NOTE] 
    > Om du försöker göra detta kontrollerar systemet om det skapar en dubblett SAML-inloggningsnamn. Till exempel om kunden har användarnamn Användare1 och Användare1. Tar bort skiftlägeskänslighet gör dessa dubbletter. Systemet får du ett felmeddelande och kommer inte att aktivera funktionen. Kunden måste ändra något av användarnamnet så att det är faktiskt stavat olika. 

1. Välj bekräftelsen konfiguration för enkel inloggning på den klassiska Azure-portalen och klicka sedan på **Slutför** att stänga den **Konfigurera enkel inloggning** dialogrutan.
   
    ![Program][14]
2. På den **enkel inloggning bekräftelse** klickar du på **Slutför**.
   
    ![Program][15]

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i den klassiska portalen kallas Britta Simon.

![Skapa Azure AD-användare][16]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **klassiska Azure-portalen**, klicka på det vänstra navigeringsfönstret **Active Directory**.
   
    ![Skapa en testanvändare i Azure AD][17]
2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.
3. Klicka för att visa en lista över användare, på menyn upp **användare**.
   
    ![Skapa en testanvändare i Azure AD][18]
4. Öppna den **Lägg till användare** i verktygsfältet längst ned i dialogrutan klickar du på **Lägg till användare**.
   
    ![Skapa en testanvändare i Azure AD][19]
5. På den **berätta om den här användaren** dialogrutan utför följande steg:
   
    ![Skapa en testanvändare i Azure AD][20]
   
    a. Välj ny användare i din organisation som typ av användare.
   
    b. I användarnamnet **textruta**, typen **BrittaSimon**.
   
    c. Klicka på **Nästa**.
6. På den **användarprofil** dialogrutan utför följande steg:
   
    ![Skapa en testanvändare i Azure AD][21]
   
    a. I den **Förnamn** textruta typen **Britta**.  
   
    b. I den **efternamn** textruta typ, **Simon**.
   
    c. I den **visningsnamn** textruta typen **Britta Simon**.
   
    d. I den **rollen** väljer **användaren**.
   
    e. Klicka på **Nästa**.
7. På den **skaffa tillfälligt lösenord** dialogrutan sidan, klickar du på **skapa**.
   
    ![Skapa en testanvändare i Azure AD][22]
8. På den **skaffa tillfälligt lösenord** dialogrutan utför följande steg:
   
    ![Skapa en testanvändare i Azure AD][23]
   
    a. Anteckna värdet för den **nytt lösenord**.
   
    b. Klicka på **Complete** (Slutför).  

### <a name="creating-a-successfactors-test-user"></a>Skapa en testanvändare SuccessFactors
För att aktivera Azure AD-användare att logga in på SuccessFactors etableras de i SuccessFactors.  
När det gäller SuccessFactors är etablering en manuell aktivitet.

För att få användare som skapats i SuccessFactors, måste du kontakta den [SuccessFactors supportteam](https://www.successfactors.com/en_us/support.html).

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare
Syftet med det här avsnittet är att aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja sin åtkomst till SuccessFactors.

![Tilldela användare][24]

**Om du vill tilldela SuccessFactors Britta Simon utför du följande steg:**

1. På den klassiska portalen för att öppna vyn program i katalogen vyn klickar du på **program** på huvudmenyn.
   
    ![Tilldela användare][25]
2. Välj i listan med program **SuccessFactors**.
   
    ![Konfigurera enkel inloggning][26]
3. Klicka på menyn högst upp **användare**.
   
    ![Tilldela användare][27]
4. Välj i listan användare **Britta Simon**.
5. Klicka på i verktygsfältet längst ned i **tilldela**.
   
    ![Tilldela användare][28]

### <a name="testing-single-sign-on"></a>Testa enkel inloggning
Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen SuccessFactors på åtkomstpanelen du bör få automatiskt loggat in på ditt SuccessFactors program.

## <a name="additional-resources"></a>Ytterligare resurser
* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_00.png
[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_01.png
[6]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_02.png
[7]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_03.png
[8]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_04.png
[9]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_05.png
[10]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_06.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png

[16]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_00.png
[17]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png
[18]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png
[19]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png
[20]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png
[21]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_05.png
[22]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_06.png
[23]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_07.png

[24]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_07.png
[25]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_08.png
[26]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_11.png
[27]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_09.png
[28]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_10.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png
