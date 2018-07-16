---
title: 'Självstudier: Azure Active Directory-integration med Tableau Server | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 4c88d6a8b22e4c5c19112c30e15bc960bc8ed1b5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045231"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Självstudier: Azure Active Directory-integration med Tableau Server

I den här självstudien får du lära dig hur du integrerar Tableau Server med Azure Active Directory (AD Azure).

Integrera Tableau Server med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Tableau Server
- Du kan aktivera användarna att automatiskt få loggat in på Tableau Server (enkel inloggning) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Tableau Server behöver du följande objekt:

- En Azure AD-prenumeration
- En Tableau Server enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Tableau Server från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-tableau-server-from-the-gallery"></a>Att lägga till Tableau Server från galleriet
För att konfigurera integrering av Tableau Server i Azure AD, som du behöver lägga till Tableau Server från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Tableau Server från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Tableau Server**.

    ![Skapa en Azure AD-användare för testning](./media/tableauserver-tutorial/tutorial_tableauserver_search.png)

5. I resultatpanelen väljer **Tableau Server**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/tableauserver-tutorial/tutorial_tableauserver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Tableau Server baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Tableau Server är att en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Tableau Server upprättas.

I Tableau Server tilldelar du värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Tableau Server, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Tableau Server](#creating-a-tableau-server-test-user)**  – du har en motsvarighet för Britta Simon i Tableau Server som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Tableau Server-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Tableau Server:**

1. I Azure-portalen på den **Tableau Server** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial_tableauserver_samlbase.png)

3. På den **Tableau Server-domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial_tableauserver_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://azure.<domain name>.link`
    
    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://azure.<domain name>.link`

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://azure.<domain name>.link/wg/saml/SSO/index.html`
     
    > [!NOTE] 
    > Föregående värden är inte riktiga värden. Senare kan uppdatera du värdena med faktiska URL och identifierare från konfigurationssidan Tableau Server. 

4. Tableau Server-programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **”användarattribut”** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för samma.
    
    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/3.png)
    
5. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ---------------| --------------- |    
    | användarnamn | *User.mailNickname* |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial_officespace_04.png)

    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial_officespace_05.png)
    
    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.
    
    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **Ok**


6. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial_tableauserver_certificate.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial_general_400.png)
<CS>
8. För att få SSO konfigurerats för ditt program kan behöva du inloggning till Tableau Server-klienten som en administratör.
   
   a. I Tableau Server-konfiguration klickar du på den **SAML** fliken.
  
    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial_tableauserver_001.png) 
  
   b. Markera kryssrutan för **Använd SAML för enkel inloggning**.
   
   c. Tableau Server Retur-URL – URL: en som Tableau Server-användare kommer att använda, till exempel http://tableau_server. Med hjälp av http://localhost rekommenderas inte. Med en URL med ett avslutande snedstreck (till exempel http://tableau_server/) stöds inte. Kopiera **Tableau Server Retur-URL** och klistra in den till Azure AD **inloggning på URL: en** -textrutan i **Tableau Server-domän och URL: er** avsnittet.
   
   d. SAML entitets-ID – entitets-ID som unikt identifierar din Tableau Server-installation att IDP: N. Du kan ange din Tableau Server URL: en igen här, om du vill, men den behöver inte vara din Tableau Server-URL. Kopiera **SAML entitets-ID** och klistra in den till Azure AD **identifierare** -textrutan i **Tableau Server-domän och URL: er** avsnittet.
     
   e. Klicka på den **exportera metadatafil** och öppna den i redigeringsprogrammet text. Leta upp URL för Konsumenttjänst för försäkran med Http Post och Index 0 och kopiera URL: en. Klistra in den till Azure AD **svars-URL** -textrutan i **Tableau Server-domän och URL: er** avsnittet.
   
   f. Leta upp din Federationsmetadata-fil som hämtats från Azure-portalen och ladda upp den i den **IDP: N för SAML-metadatafil**.
   
   g. Klicka på den **OK** knappen Tableau Server konfigurationssida.
   
    >[!NOTE] 
    >Kunden behöver ladda upp alla certifikat i Tableau Server SAML SSO-konfigurationen och kommer få ignoreras i flödet för enkel inloggning.
    >Om du behöver för att konfigurera SAML på Tableau Server finns i den här artikeln [konfigurera SAML](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm).
    >
<CE>

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/tableauserver-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/tableauserver-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/tableauserver-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/tableauserver-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-tableau-server-test-user"></a>Skapa en testanvändare Tableau Server

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Tableau Server. Du måste etablera alla användare i Tableau server. 

Det aktuella användarnamnet för användaren bör matcha värdet som du har konfigurerat i det anpassade attributet Azure AD av **användarnamn**. Integrationen ska fungera med korrekt mappning [konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on).

>[!NOTE]
>Om du vill skapa en användare manuellt kan behöva du kontakta Tableau Server-administratör i din organisation.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Tableau Server.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Tableau Server, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Tableau Server**.

    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial_tableauserver_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Tableau Server i åtkomstpanelen du bör få automatiskt loggat in på ditt Tableau Server-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/tableauserver-tutorial/tutorial_general_04.png

[100]: ./media/tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/tableauserver-tutorial/tutorial_general_201.png
[202]: ./media/tableauserver-tutorial/tutorial_general_202.png
[203]: ./media/tableauserver-tutorial/tutorial_general_203.png

