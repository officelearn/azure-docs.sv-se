---
title: 'Självstudier: Azure Active Directory-integration med Adobe Sign | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Adobe Sign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: d5cdc2ec0c6cfcf52f84629485d0dd879fbf6fa2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054006"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Självstudier: Azure Active Directory-integration med Adobe Sign

I den här självstudien får du lära dig hur du integrerar Adobe Sign med Azure Active Directory (AD Azure).

Integrera Adobe Sign med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Adobe Sign.
- Du kan aktivera användarna att automatiskt få loggat in på Adobe Sign (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Mer information om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Adobe Sign, behöver du:

- En Azure AD-prenumeration
- Ett Adobe Sign enkel inloggning aktiverat prenumeration

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägger till Adobe Sign från galleriet.
2. Konfigurera och testa Azure AD enkel inloggning.

## <a name="add-adobe-sign-from-the-gallery"></a>Lägg till Adobe Sign från galleriet
För att konfigurera integrering av Adobe Sign i Azure AD, som du behöver lägga till Adobe Sign från galleriet i din lista över hanterade SaaS-appar.

1. I den [Azure-portalen](https://portal.azure.com), i rutan till vänster väljer du den **Azure Active Directory** ikon. 

    ![Skärmbild av Azure Active Directory-ikonen][1]

2. Bläddra till **företagsprogram** > **alla program**.

    ![Skärmbild av Azure Active Directory-menyer, med Enterprise-program och alla program som är markerat][2]
    
3. Om du vill lägga till ett nytt program, Välj **nytt program** överst i dialogrutan.

    ![Skärmbild av nytt program-alternativ överst i dialogrutan][3]

4. I sökrutan skriver **Adobe Sign**.

    ![Skärmbild av sökrutan](./media/adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. I resultatpanelen väljer **Adobe Sign**, och välj sedan **Lägg till**.

    ![Skärmbild av resultatrutan](./media/adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Adobe Sign, baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD ska kunna identifiera ett länkat förhållande mellan en Azure AD-användare och relaterade användaren i Adobe Sign.

För att etablera den länkade relationen i Adobe Sign, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn**.

Om du vill konfigurera och testa Azure AD enkel inloggning med Adobe Sign, utför du följande byggblock:

1. [Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on) vill tillåta att användarna använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en testanvändare med Adobe Sign](#creating-an-adobe-sign-test-user) har en motsvarighet för Britta Simon i Adobe Sign som är kopplad till Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#testing-single-sign-on) att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program med Adobe Sign.

1. I Azure-portalen på den **Adobe Sign** application integration markerar **enkel inloggning**.

    ![Skärmbild av Adobe Sign integration programsidan med enkel inloggning för markerat][4]

2. På den **enkel inloggning** dialogrutan för **läge**väljer **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Skärmbild av enkel inloggning för dialogrutan med läget markerat](./media/adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. I den **Adobe Sign domän och URL: er** avsnittet, utför följande steg:

    ![Skärmbild av Adobe Sign-domän och URL: er](./media/adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. I den **inloggnings-URL** text skriver en URL som använder följande mönster: `https://<companyname>.echosign.com/`

    b. I den **identifierare** text skriver en URL som använder följande mönster: `https://<companyname>.echosign.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska inloggnings-URL och identifierare. Kontakta [Adobe Sign klienten supportteamet](https://helpx.adobe.com/in/contact/support.html) att hämta dessa värden.

4. I den **SAML-signeringscertifikat** väljer **Certificate(Base64)**, och sedan spara certifikatfilen på datorn.

    ![Skärmbild av SAML-signeringscertifikat avsnittet](./media/adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. Välj **Spara**.

    ![Skärmbild av spara](./media/adobe-echosign-tutorial/tutorial_general_400.png)

6. I den **Adobe Sign Configuration** väljer **Konfigurera Adobe Sign** att öppna den **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning**, **SAML entitets-ID**, och **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens** avsnittet.

    ![Skärmbild av Adobe logga konfigurationsavsnittet, med Konfigurera Adobe Sign markerat](./media/adobe-echosign-tutorial/tutorial_adobesign_configure.png)

7. Innan konfigurationen, kontakta den [Adobe Sign klienten supportteamet](https://helpx.adobe.com/in/contact/support.html) godkänna din domän i Adobe Sign. Här är hur du lägger till domänen:

    a. Den [Adobe Sign klienten supportteamet](https://helpx.adobe.com/in/contact/support.html) skickar du en slumpmässigt genererad token. Token är för din domän, enligt följande: **adobe-logga-verifiering = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publicera verifieringstoken i en DNS-post i text och meddela den [Adobe Sign klienten supportteamet](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Det kan ta några dagar eller längre. Observera att DNS-spridningen fördröjningar innebär att ett värde som publicerats i DNS kanske inte visas för en timme eller mer. IT-administratören bör vara kunskap om hur du publicerar den här token i en DNS-post i text.
    
    c. När du meddela den [Adobe Sign klienten supportteamet](https://helpx.adobe.com/in/contact/support.html) via support-ärende när token har publicerats kan de verifiera domänen och lägga till den i ditt konto.
    
    d. Här är i allmänhet hur du publicerar token för en DNS-post:

    * Logga in på ditt domänkonto
    * Leta reda på sidan för att uppdatera DNS-posten. Den här sidan kan anropas DNS-hantering, hantering av namnhantering eller avancerade inställningar.
    * Hitta TXT-poster för din domän.
    * Lägga till en TXT-post med den fullständiga token värde som tillhandahålls av Adobe.
    * Spara ändringarna.

8. I ett annat webbläsarfönster, loggar du in din Adobe Sign företagets webbplats som administratör.

9. I SAML-menyn väljer **kontoinställningar** > **SAML-inställningar**.
   
    ![Skärmbild av Adobe Sign SAML inställningssidan](./media/adobe-echosign-tutorial/ic789520.png "konto")

10. I den **SAML-inställningar** avsnittet, utför följande steg:
  
    ![Skärmbild av SAML-inställningar](./media/adobe-echosign-tutorial/ic789521.png "SAML-inställningar")
   
    a. Under **SAML läge**väljer **SAML obligatoriska**.
   
    b. Välj **Tillåt Echosign för Kontoadministratörer att logga in med sina autentiseringsuppgifter för Echosign**.
   
    c. Under **Användargenereringen**väljer **Lägg automatiskt till användare som autentiseras via SAML**.

    d. Klistra in **SAML entitets-ID**, som du har kopierat från Azure-portalen till den **Entity ID/utfärdar-URL** textrutan.
    
    e. Klistra in **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen till den **URL/SSO Inloggningsslutpunkt** textrutan.
   
    f. Klistra in **URL: en för utloggning**, som du har kopierat från Azure-portalen till den **slutpunkten för utloggning URL/SLO** textrutan.

    g. Öppna din hämtade **Certificate(Base64)** -filen i anteckningar. Kopiera innehållet i den till Urklipp och klistra in den till den **IdP certifikat** textrutan.

    h. Välj **spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
Målet med det här avsnittet är att skapa en testanvändare med namnet Britta Simon i Azure-portalen.

![Skärmbild av testanvändarnamn i Azure portal][100]

1. I den **Azure-portalen**, i rutan till vänster väljer du den **Azure Active Directory** ikon.

    ![Skärmbild av Azure AD-ikon](./media/adobe-echosign-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och välj **alla användare**.
    
    ![Skärmbild av Azure AD-menyer, med användare och grupper och alla användare som är markerat](./media/adobe-echosign-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan **Lägg till**.
 
    ![Skärmbild av längst upp alla användare dialogrutan med Lägg till alternativet markerat](./media/adobe-echosign-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skärmbild av dialogrutan](./media/adobe-echosign-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta, typ **BrittaSimon**.

    b. I den **användarnamn** text skriver BrittaSimon e-postadress.

    c. Välj **visa lösenord**, och anteckna värdet för den **lösenord**.

    d. Välj **Skapa**.
 
### <a name="create-an-adobe-sign-test-user"></a>Skapa ett Adobe Sign testanvändare

Om du vill aktivera Azure AD-användare att logga in på Adobe Sign, måste de etableras i Adobe Sign. Det här är en manuell aktivitet.

>[!NOTE]
>Du kan använda andra verktyg för Adobe Sign användare konto att skapa eller tillhandahålls av Adobe Sign-API: er för att etablera användarkonton i Azure AD. 

1. Logga in på din **Adobe Sign** företagets plats som administratör.

2. I menyn högst upp väljer **konto**. Välj i den vänstra rutan **användare och grupper** > **skapa en ny användare**.
   
    ![Skärmbild av Adobe Sign företagets webbplats med kontot, användare och grupper, och skapa en ny användare markerat](./media/adobe-echosign-tutorial/ic789524.png "konto")
   
3. I den **skapa nya användare** avsnittet, utför följande steg:
   
    ![Skärmbild av skapa nya användare avsnittet](./media/adobe-echosign-tutorial/ic789525.png "skapa användare")
   
    a. Skriv den **e-postadress**, **Förnamn**, och **efternamn** av en giltig Azure AD-konto som du vill etablera i textrutorna relaterade.
   
    b. Välj **skapa användare**.

>[!NOTE]
>Azure Active Directory-kontoinnehavare tar emot ett e-postmeddelande som innehåller en länk för att bekräfta kontot, innan den blir aktiv. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning, genom att bevilja åtkomst till Adobe Sign.

![Skärmbild av Azure portal enkel inloggning][200] 

1. Öppna program i Azure-portalen. Bläddra till vyn directory, gå till **företagsprogram**, och välj **alla program**.

    ![Skärmbild av Azure portalprogram visa med Enterprise-program och alla program som är markerat][201] 

2. I listan med program väljer **Adobe Sign**.

    ![Skärmbild av listan med program med Adobe Sign markerat](./media/adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. I menyn till vänster väljer **användare och grupper**.

    ![Skärmbild av menyn med användare och grupper som är markerat][202] 

4. Välj **Lägg till**. I den **Lägg till tilldelning** väljer **användare och grupper**.

    ![Skärmbild av användare och grupper SID- och Lägg till tilldelning][203]

5. I **användare och grupper** dialogrutan på användarlistan väljer **Britta Simon**.

6. I den **användare och grupper** dialogrutan klickar du på **Välj**.

7. I den **Lägg till tilldelning** dialogrutan **tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

När du väljer panelen Adobe Sign i åtkomstpanelen, bör du få automatiskt loggat in på ditt Adobe Sign-program. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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
