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
ms.openlocfilehash: d91a64762181fd4c261f1ba964d366253912f009
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35966472"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Självstudier: Azure Active Directory-integrering med Adobe

I kursen får lära du att integrera Adobe inloggning med Azure Active Directory (AD Azure).

Integrera Adobe inloggning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Adobe inloggning.
- Du kan aktivera användarna att automatiskt hämta loggat in på Adobe logga (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats--Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Adobe, behöver du:

- En Azure AD-prenumeration
- En Adobe logga enkel inloggning aktiverad prenumeration

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägger till Adobe logga från galleriet.
2. Konfigurera och testa Azure AD enkel inloggning.

## <a name="add-adobe-sign-from-the-gallery"></a>Lägg till Adobe registrering från galleriet
Du måste lägga till Adobe logga från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Adobe inloggning i Azure AD.

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan, Välj den **Azure Active Directory** ikon. 

    ![Skärmbild av Azure Active Directory-ikon][1]

2. Bläddra till **företagsprogram** > **alla program**.

    ![Skärmbild av Azure Active Directory menyer med företagsprogram och alla program som är markerat][2]
    
3. Om du vill lägga till ett nytt program, Välj **nytt program** överst i dialogrutan.

    ![Skärmbild av nya program-alternativet överst i dialogrutan][3]

4. I sökrutan skriver **Adobe logga**.

    ![Skärmbild av sökrutan](./media/adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. Välj i resultatpanelen **Adobe logga**, och välj sedan **Lägg till**.

    ![Skärmbild av resultatrutan](./media/adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Adobe, baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD att identifiera ett länkat förhållande mellan en Azure AD-användare och relaterade användaren Adobe tecken.

För att upprätta den länka relationen i Adobe logga, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn**.

Slutför följande byggblock för att konfigurera och testa Azure AD enkel inloggning med Adobe:

1. [Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on) att användarna ska kunna använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en testanvändare Adobe logga](#creating-an-adobe-sign-test-user) har en motsvarighet för Britta Simon Adobe loggar som är kopplad till Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#testing-single-sign-on) att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Adobe signera programmet.

1. I Azure-portalen på den **Adobe logga** programmet integration anger **enkel inloggning**.

    ![Skärmbild av Adobe logga programmet integration sida med enkel inloggning markerat][4]

2. På den **enkel inloggning** i dialogrutan för **läge**väljer **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Skärmbild av enkel inloggning i dialogrutan med läget markerat](./media/adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. I den **Adobe logga domän och URL: er** avsnittet, utför följande steg:

    ![Skärmbild av Adobe logga domän och URL: er](./media/adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. I den **inloggnings-URL** text skriver en URL som använder följande mönster: `https://<companyname>.echosign.com/`

    b. I den **identifierare** text skriver en URL som använder följande mönster: `https://<companyname>.echosign.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Adobe logga klienten supportteamet](https://helpx.adobe.com/in/contact/support.html) att hämta dessa värden.

4. I den **SAML-signeringscertifikat** väljer **Certificate(Base64)**, och sedan spara certifikatfilen på datorn.

    ![Skärmbild av SAML signeringscertifikat avsnitt](./media/adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. Välj **Spara**.

    ![Skärmbild av spara](./media/adobe-echosign-tutorial/tutorial_general_400.png)

6. I den **Adobe logga Configuration** väljer **Konfigurera Adobe logga** att öppna den **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL**, **SAML enhets-ID**, och **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens** avsnitt.

    ![Skärmbild av Adobe logga konfigurationsavsnittet, med Konfigurera Adobe markerat](./media/adobe-echosign-tutorial/tutorial_adobesign_configure.png)

7. Innan konfigurationen, kontakta den [Adobe logga klienten supportteamet](https://helpx.adobe.com/in/contact/support.html) till godkända din domän i Adobe logga. Här är hur du lägger till domänen:

    a. Den [Adobe logga klienten supportteamet](https://helpx.adobe.com/in/contact/support.html) skickar en slumpmässigt genererad token. Token är för din domän så här: **adobe-sign-verifiering = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publicera verifieringstoken för i en DNS-post för text och meddela den [Adobe logga klienten supportteamet](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Det kan ta ett par dagar eller längre. Observera att DNS-spridningen fördröjningar betyder att ett värde som är publicerad i DNS inte kanske visas för en timme. IT-administratören bör vara kunskap om hur du publicerar denna token i en DNS-post för text.
    
    c. När du meddela den [Adobe logga klienten supportteamet](https://helpx.adobe.com/in/contact/support.html) via supportärende, när token som har publicerats de verifiera domänen och lägga till den i ditt konto.
    
    d. Här är i allmänhet hur du publicerar token på en DNS-post:

    * Logga in på ditt domänkonto
    * Hitta sidan för att uppdatera DNS-posten. Den här sidan kan anropas Datorhantering, namnet Serverhantering eller avancerade inställningar.
    * Hitta TXT-poster för din domän.
    * Lägg till en TXT-post med fullständig token värdet som angavs av Adobe.
    * Spara ändringarna.

8. I en annan webbläsarfönster loggar du in på ditt Adobe logga företagets webbplats som administratör.

9. SAML-menyn väljer du **kontoinställningar** > **SAML inställningar**.
   
    ![Skärmbild av Adobe signera SAML inställningssidan](./media/adobe-echosign-tutorial/ic789520.png "konto")

10. I den **SAML inställningar** avsnittet, utför följande steg:
  
    ![Skärmbild av SAML inställningar](./media/adobe-echosign-tutorial/ic789521.png "SAML-inställningar")
   
    a. Under **SAML läge**väljer **SAML obligatoriska**.
   
    b. Välj **Tillåt Echosign Kontoadministratörer kan logga in med hjälp av autentiseringsuppgifterna Echosign**.
   
    c. Under **Användarskapelse**väljer **Lägg automatiskt till användare som autentiseras via SAML**.

    d. Klistra in **SAML enhets-ID**, som du har kopierat från Azure-portalen i den **entitet ID/utfärdar-URL** textruta.
    
    e. Klistra in **SAML enkel inloggning Tjänstwebbadress**, som du har kopierat från Azure-portalen i den **inloggningen URL/SSO Endpoint** textruta.
   
    f. Klistra in **Sign-Out URL**, som du har kopierat från Azure-portalen i den **logga ut URL/SLO Endpoint** textruta.

    g. Öppna din hämtade **Certificate(Base64)** fil i anteckningar. Kopiera innehållet i den till Urklipp och klistra in den till den **IdP certifikat** textruta.

    h. Välj **spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare med namnet Britta Simon i Azure-portalen.

![Skärmbild av test användarnamn i Azure-portalen][100]

1. I den **Azure-portalen**, i den vänstra rutan, Välj den **Azure Active Directory** ikon.

    ![Skärmbild av Azure AD-ikon](./media/adobe-echosign-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och välj **alla användare**.
    
    ![Skärmbild av Azure AD-menyer med användare och grupper och alla användare som är markerat](./media/adobe-echosign-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan **Lägg till**.
 
    ![Skärmbild av överkant alla dialogrutan användare med Lägg till alternativet markerat](./media/adobe-echosign-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skärmbild av dialogrutan](./media/adobe-echosign-tutorial/create_aaduser_04.png) 

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** text skriver BrittaSimon e-postadress.

    c. Välj **visa lösenordet**, och anteckna värdet för den **lösenord**.

    d. Välj **Skapa**.
 
### <a name="create-an-adobe-sign-test-user"></a>Skapa en Adobe logga testanvändare

Om du vill aktivera Azure AD-användare att logga in till Adobe inloggning, måste de etableras i Adobe logga. Det här är en manuell aktivitet.

>[!NOTE]
>Du kan använda andra Adobe logga användare skapa verktyg eller API: er som tillhandahålls av Adobe logga för att etablera Azure AD-användarkonton. 

1. Logga in på ditt **Adobe logga** företagets webbplats som administratör.

2. Välj på menyn högst upp **konto**. I den vänstra rutan, markera **användare och grupper** > **skapa en ny användare**.
   
    ![Skärmbild av Adobe logga företagets webbplats med kontot, användare och grupper, och skapa en ny användare markerade](./media/adobe-echosign-tutorial/ic789524.png "konto")
   
3. I den **skapa nya användare** avsnittet, utför följande steg:
   
    ![Skärmbild av skapa nya användare avsnittet](./media/adobe-echosign-tutorial/ic789525.png "skapa användare")
   
    a. Typ av **e-postadress**, **Förnamn**, och **efternamn** av ett giltigt Azure AD-kontot som du vill etablera i textrutorna relaterade.
   
    b. Välj **skapa användare**.

>[!NOTE]
>Azure Active Directory kontoinnehavaren får ett e-postmeddelande som innehåller en länk för att bekräfta kontot, innan den aktiveras. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning, genom att bevilja åtkomst till Adobe logga.

![Skärmbild av Azure portal enkel inloggning][200] 

1. Öppna program i Azure-portalen. Gå till vyn directory genom att gå till **företagsprogram**, och välj **alla program**.

    ![Skärmbild av Azure portal program visas med företagsprogram och alla program som är markerat][201] 

2. Välj i listan med program **Adobe logga**.

    ![Skärmbild av listan med program med Adobe markerat](./media/adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. Välj på menyn till vänster **användare och grupper**.

    ![Skärmbild av menyn med användare och grupper som är markerat][202] 

4. Välj **Lägg till**. I den **Lägg uppdrag** väljer **användare och grupper**.

    ![Skärmbild av användare och grupper och lägger till avsnittet för tilldelning][203]

5. I **användare och grupper** dialogrutan i användarlistan väljer **Britta Simon**.

6. I den **användare och grupper** dialogrutan klickar du på **Välj**.

7. I den **Lägg uppdrag** dialogrutan **tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

När du väljer panelen Adobe logga på åtkomstpanelen ska hämta loggas du automatiskt i tillämpningsprogrammet Adobe logga. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/adobe-echosign-tutorial/tutorial_general_01.png
[2]: ./media/adobe-echosign-tutorial/tutorial_general_02.png
[3]: ./media/adobe-echosign-tutorial/tutorial_general_03.png
[4]: ./media/adobe-echosign-tutorial/tutorial_general_04.png

[100]: ./media/adobe-echosign-tutorial/tutorial_general_100.png

[200]: ./media/adobe-echosign-tutorial/tutorial_general_200.png
[201]: ./media/adobe-echosign-tutorial/tutorial_general_201.png
[202]: ./media/adobe-echosign-tutorial/tutorial_general_202.png
[203]: ./media/adobe-echosign-tutorial/tutorial_general_203.png
