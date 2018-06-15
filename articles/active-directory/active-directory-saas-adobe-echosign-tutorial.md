---
title: 'Självstudier: Azure Active Directory-integrering med Adobe | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Adobe logga.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: c3b7e7178ef68475f331edf058ca0f23661af3ea
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34338881"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Självstudier: Azure Active Directory-integrering med Adobe

I kursen får lära du att integrera Adobe inloggning med Azure Active Directory (AD Azure).

Integrera Adobe inloggning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Adobe inloggning
- Du kan aktivera användarna att automatiskt hämta loggat in på Adobe logga (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Adobe, behöver du följande:

- En Azure AD-prenumeration
- En Adobe logga enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Adobe logga från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-adobe-sign-from-the-gallery"></a>Att lägga till Adobe logga från galleriet
Du måste lägga till Adobe logga från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Adobe inloggning i Azure AD.

**Utför följande steg för att lägga till Adobe logga från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Adobe logga**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. Välj i resultatpanelen **Adobe logga**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Adobe baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Adobe loggar till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Adobe logga upprättas.

I Adobe signera, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Adobe, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Adobe logga](#creating-an-adobe-sign-test-user)**  – du har en motsvarighet för Britta Simon Adobe tecken som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Adobe signera programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Adobe:**

1. I Azure-portalen på den **Adobe logga** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. På den **Adobe logga domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<companyname>.echosign.com/`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<companyname>.echosign.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Adobe logga klienten supportteamet](https://helpx.adobe.com/in/contact/support.html) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_400.png)

6. På den **Adobe logga Configuration** klickar du på **Konfigurera Adobe logga** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_configure.png)

7. Innan konfigurationen måste du kontakta [Adobe logga klienten supportteamet](https://helpx.adobe.com/in/contact/support.html) till godkända din domän i Adobe logga. Följ de nedanstående steg för att lägga till domänen:

    a. [Adobe logga klienten supportteamet](https://helpx.adobe.com/in/contact/support.html) skicka en slumpmässigt genererad token. Token är för din domän, t.ex.: **adobe-sign-verifiering = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Du måste publicera verifieringstoken i en DNS-post för text och meddela [Adobe logga klienten supportteamet](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Du kan förvänta dig att det kan ta ett par dagar eller kanske längre. Observera att DNS-spridningen fördröjningar betyder att ett värde som är publicerad i DNS inte kan vara synlig för en timme. Vi räknar med IT-administratören bör vara kunskap om hur du publicerar denna token i en DNS-post för text.
    
    c. När du meddela [Adobe logga klienten supportteamet](https://helpx.adobe.com/in/contact/support.html) via supportärende, när token som har publicerats ska verifiera domänen och lägga till den i ditt konto.
    
    d. Allmänna steg som du kan vidta för att publicera på en DNS-post - token

    * Logga in på ditt domänkonto
    * Hitta sidan för att uppdatera DNS-posten. Den här sidan kan anropas Datorhantering, namnet Serverhantering eller avancerade inställningar.
    * Hitta TXT-poster för din domän.
    * Lägg till en TXT-post med fullständig token värdet som angavs av Adobe
    * Spara ändringarna.

8. I en annan webbläsarfönster loggar du in på webbplatsen Adobe logga företag som administratör.

9. SAML-menyn klickar du på **kontoinställningar**, och klicka sedan på **SAML inställningar**.
   
    ![Kontot](./media/active-directory-saas-adobe-echosign-tutorial/ic789520.png "konto")

10. I den **SAML inställningar** avsnittet, utför följande steg:
  
    ![Inställningar för SAML](./media/active-directory-saas-adobe-echosign-tutorial/ic789521.png "SAML-inställningar")
   
    a. Som **SAML läge**väljer **SAML obligatoriska**.
   
    b. Välj **Tillåt Adobe logga Kontoadministratörer att logga in med sina autentiseringsuppgifter för inloggning av Adobe**.
   
    c. Som **Användarskapelse**väljer **Lägg automatiskt till användare som autentiseras via SAML**.

    d. Klistra in **SAML enhets-ID**, som du har kopierat från Azure-portalen i den **entitet ID/utfärdar-URL** textruta.
    
    e. Klistra in **SAML enkel inloggning Tjänstwebbadress**, som du har kopierat från Azure-portalen i den **inloggningen URL/SSO Endpoint** textruta.
   
    f. Klistra in **Sign-Out URL**, som du har kopierat från Azure-portalen i den **logga ut URL/SLO Endpoint** textruta.

    g. Öppna din hämtade **Certificate(Base64)** fil i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **IdP certifikat** textruta

    h. Klicka på **spara ändringar**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-adobe-sign-test-user"></a>Skapa en Adobe logga testanvändare

Om du vill aktivera Azure AD-användare kan logga in till Adobe inloggning, måste de etableras i Adobe logga. Adobe logga är etablering en manuell aktivitet.

>[!NOTE]
>Du kan använda något annat Adobe logga användarens konto skapas verktyg eller API: er som tillhandahålls av Adobe logga etablera AAD-användarkonton. 

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt **Adobe logga** företagets webbplats som administratör.

2. Klicka på menyn högst upp **konto**, och klickar sedan på i navigeringsfönstret till vänster **användare och grupper**, och klicka sedan på **skapa en ny användare**.
   
    ![Kontot](./media/active-directory-saas-adobe-echosign-tutorial/ic789524.png "konto")
   
3. I den **skapa nya användare** avsnittet, utför följande steg:
   
    ![Skapa användare](./media/active-directory-saas-adobe-echosign-tutorial/ic789525.png "skapa användare")
   
    a. Typ av **e-postadress**, **Förnamn**, och **efternamn** av en giltig AAD-konto som du vill etablera i relaterade textrutor.
   
    b. Klicka på **skapa användare**.

>[!NOTE]
>Azure Active Directory kontoinnehavaren får ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan den aktiveras. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Adobe logga.

![Tilldela användare][200] 

**Om du vill tilldela Adobe logga Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Adobe logga**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

När du klickar på panelen Adobe logga på åtkomstpanelen du bör få automatiskt loggat in på Adobe signera programmet.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_203.png
