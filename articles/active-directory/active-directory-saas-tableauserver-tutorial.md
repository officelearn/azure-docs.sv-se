---
title: 'Självstudier: Azure Active Directory-integrering med Tableau Server | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: e2db0f36be5c102ebf87a2ca2ed4bb53b81cb742
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Självstudier: Azure Active Directory-integrering med Tableau Server

I kursen får lära du att integrera Tableau Server med Azure Active Directory (AD Azure).

Integrera Tableau Server med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Tableau Server
- Du kan aktivera användarna att automatiskt hämta loggat in på Tableau Server (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Tableau Server behöver du följande:

- En Azure AD-prenumeration
- En Tableau Server enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Tableau Server från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-tableau-server-from-the-gallery"></a>Att lägga till Tableau Server från galleriet
Du måste lägga till Tableau Server från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Tableau Server i Azure AD.

**Om du vill lägga till Tableau Server från galleriet, utför du följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Tableau Server**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_search.png)

5. Välj i resultatpanelen **Tableau Server**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Tableau Server baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Tableau Server till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Tableau Server upprättas.

I Tableau Server, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Tableau Server, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Tableau Server](#creating-a-tableau-server-test-user)**  – du har en motsvarighet för Britta Simon i Tableau-Server som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i serverprogrammet Tableau.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Tableau Server:**

1. I Azure-portalen på den **Tableau Server** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_samlbase.png)

3. På den **URL: er och Tableau serverdomänen** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://azure.<domain name>.link`
    
    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://azure.<domain name>.link`

    c. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://azure.<domain name>.link/wg/saml/SSO/index.html`
     
    > [!NOTE] 
    > Föregående värden är inte verkliga värden. Senare kan uppdatera du värdena med faktiska URL och identifierare från konfigurationssidan Tableau Server. 

4. Tableau serverprogrammet förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den **”användarattribut”** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för samma.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauserver-tutorial/3.png)
    
5. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ---------------| --------------- |    
    | användarnamn | *User.mailNickname* |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauserver-tutorial/tutorial_officespace_04.png)

    ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauserver-tutorial/tutorial_officespace_05.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **Ok**


6. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_certificate.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_400.png)
<CS>
8. För att få SSO konfigurerats för ditt program måste logga in till din Tableau Server-klient som administratör.
   
   a. I Tableau Server-konfiguration klickar du på den **SAML** fliken.
  
    ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_001.png) 
  
   b. Markera kryssrutan för **Använd SAML för enkel inloggning**.
   
   c. Tableau Server Retur-URL – URL: en som Tableau Server-användare kommer åt, t.ex http://tableau_server. Med hjälp av http://localhost rekommenderas inte. Med en URL som avslutande snedstreck (till exempel http://tableau_server/) stöds inte. Kopiera **Tableau Server Retur-URL** och klistra in den till Azure AD **logga URL** TextBox-kontroll i **URL: er och Tableau serverdomänen** avsnitt.
   
   d. SAML enhets-ID, enhets-ID som unikt identifierar din Tableau serverinstallation till IdP. Du kan ange Tableau Server URL: en igen här, om du vill, men behöver inte vara Tableau-Serveradress. Kopiera **SAML enhets-ID** och klistra in den till Azure AD **identifierare** TextBox-kontroll i **URL: er och Tableau serverdomänen** avsnitt.
     
   e. Klicka på den **exportera metadatafil** och öppna den i redigeringsprogrammet text. Leta upp Assertion konsumenten tjänst-URL med Http Post och Index 0 och kopiera URL-Adressen. Klistra in den till Azure AD **Reply URL** TextBox-kontroll i **URL: er och Tableau serverdomänen** avsnitt.
   
   f. Leta upp din Federationsmetadata-fil som hämtas från Azure-portalen och sedan ladda upp den i den **SAML Idp metadatafil**.
   
   g. Klicka på den **OK** knappen på sidan Tableau serverkonfiguration.
   
    >[!NOTE] 
    >Kunden måste överföra alla certifikat i Tableau Server SAML SSO-konfigurationen och kommer hämta ignoreras i flödet för enkel inloggning.
    >Om du behöver hjälp konfigurerar SAML på Tableau Server finns i den här artikeln [konfigurera SAML](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm).
    >
<CE>

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-tableau-server-test-user"></a>Skapa en testanvändare Tableau Server

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Tableau Server. Du måste etablera alla användare i Tableau-servern. 

Att användarnamnet för användaren som ska matcha värdet som du har konfigurerat i Azure AD-attributet för **användarnamn**. Integrationen ska fungera med korrekt mappning [konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on).

>[!NOTE]
>Om du behöver skapa en användare manuellt, måste du kontakta serveradministratören Tableau i din organisation.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Tableau Server.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Tableau Server, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Tableau Server**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Tableau Server på åtkomstpanelen du bör få automatiskt loggat in på serverprogrammet Tableau.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_203.png

