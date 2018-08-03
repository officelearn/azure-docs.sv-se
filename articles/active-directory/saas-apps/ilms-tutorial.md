---
title: 'Självstudier: Azure Active Directory-integration med iLMS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.openlocfilehash: 0e67e97a68ca333dff366dd5e0222c96a1022557
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425280"
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Självstudier: Azure Active Directory-integration med iLMS

I den här självstudien får du lära dig hur du integrerar iLMS med Azure Active Directory (AD Azure).

Integrera iLMS med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till iLMS
- Du kan aktivera användarna att automatiskt få loggat in på iLMS (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med iLMS, behöver du följande objekt:

- En Azure AD-prenumeration
- En iLMS enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Du bör inte använda din produktionsmiljö såvida inte detta är nödvändigt.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till iLMS från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-ilms-from-the-gallery"></a>Att lägga till iLMS från galleriet
För att konfigurera integrering av iLMS i Azure AD, som du behöver lägga till iLMS från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till iLMS från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **iLMS**.

    ![Skapa en Azure AD-användare för testning](./media/ilms-tutorial/tutorial_ilms_search.png)

1. I resultatpanelen väljer **iLMS**, klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/ilms-tutorial/tutorial_ilms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med iLMS baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i iLMS är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i iLMS upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i iLMS.

Om du vill konfigurera och testa Azure AD enkel inloggning med iLMS, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare iLMS](#creating-an-ilms-test-user)**  – du har en motsvarighet för Britta Simon i iLMS som är länkad till en Azure AD-representation av henne.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt iLMS program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med iLMS:**

1. I Azure-portalen på den **iLMS** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/ilms-tutorial/tutorial_ilms_samlbase.png)

1. På den **iLMS domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/tutorial_ilms_url.png)

    a. I den **identifierare** textrutan klistra in den **identifierare** värde som du kopierar från **tjänstleverantör** avsnittet för SAML-inställningar i administrationsportalen för iLMS.

    b. I den **svars-URL** textrutan klistra in den **slutpunkt (URL)** värde som du kopierar från **tjänstleverantör** avsnittet för SAML-inställningar i iLMS admin-portalen med följande mönster `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

    >[!Note]
    >Den här 123456 är en exempel-värdet för identifierare.

1. Kontrollera **visa avancerade URL-inställningar**, om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/tutorial_ilms_url1.png)

    I den **inloggnings-URL** textrutan klistra in den **slutpunkt (URL)** värde som du kopierar från **tjänstleverantör** avsnittet för SAML-inställningar i administrationsportalen för iLMS som `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`     

1. Om du vill aktivera JIT-etablering, iLMS program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för detta.
    
    ![Konfigurera enkel inloggning](./media/ilms-tutorial/4.png)
    
    Skapa **avdelning, Region** och **Division** attribut och Lägg till namnet på attributen i iLMS. Dessa attribut som visas ovan krävs.  

    > [!NOTE] 
    > Du måste aktivera **skapa Un-recognized användarkonto** i iLMS att mappa dessa attribut. Följ instruktionerna [här](http://support.inspiredelearning.com/customer/portal/articles/2204526) att få en uppfattning på konfigurationen av attribut.

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ---------------| --------------- |    
    | Division | User.Department |
    | region | User.state |
    | Avdelning | User.jobtitle |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/tutorial_ilms_04.png)

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/tutorial_ilms_05.png)
    
    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.
    
    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **Ok**

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/tutorial_ilms_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/tutorial_general_400.png)

1. Logga in på i ett annat webbläsarfönster din **iLMS administrationsportalen** som administratör.

1. Klicka på **SSO:SAML** under **inställningar** flik för att öppna SAML-inställningar och utför följande steg:
    
    ![Konfigurera enkel inloggning](./media/ilms-tutorial/1.png) 

    a. Expandera den **tjänstleverantör** avsnitt och kopiera den **identifierare** och **slutpunkt (URL)** värde.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/2.png) 

    b. Under **identitetsprovidern** klickar du på **importera Metadata**.
    
    c. Välj den **Metadata** har laddat ned från Azure-portalen från **SAML-signeringscertifikat** avsnittet.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png) 

    d. Om du vill aktivera JIT etablering för att skapa iLMS konton för un-identifiera användarna, följer du dessa steg:
        
       - Kontrollera **skapa icke erkända användarkonto**.
       
       ![Configure Single Sign-On](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

       -  Mappa attribut i Azure AD med attribut i iLMS. Ange namnet på attribut eller standardvärdet i attributkolumnen.

    e. Gå till **affärsregler** fliken och utför följande steg: 
        
       ![Configure Single Sign-On](./media/ilms-tutorial/5.png)

       - Kontrollera **skapa Un-recognized regioner, avdelningar och avdelningar** skapa regioner, avdelningar och avdelningar som inte redan finns vid tidpunkten för enkel inloggning.
        
       - Kontrollera **uppdatering användaren profilen under inloggning** att ange om användarens profil uppdateras med varje enkel inloggning. 
        
       - Om den **”uppdatering tomma värden för icke obligatoriska fält i användarprofilen”** alternativet är markerat, valfritt profil fält som är tom vid inloggningen kommer även orsaka användarens iLMS profil som innehåller tomma värden för dessa fält.
        
       - Kontrollera **skicka fel e-postmeddelande** och ange e-postadress för användaren där du vill ta emot fel e-postmeddelandet.

1. Klicka på **spara** för att spara inställningarna.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/save.png)

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
    
### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/ilms-tutorial/create_aaduser_01.png) 

1. Gå till **användare och grupper** och klicka på **alla användare** att visa en lista över användare.
    
    ![Skapa en Azure AD-användare för testning](./media/ilms-tutorial/create_aaduser_02.png) 

1. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/ilms-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/ilms-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-ilms-test-user"></a>Skapa en iLMS testanvändare

Programmet stöder bara i tid användaretablering och -autentiserade användare skapas automatiskt i programmet. JIT fungerar om du har klickat på den **skapa Un-recognized användarkonto** kryssrutan under SAML konfigurationsinställning i administrationsportalen för iLMS.

Följ stegen nedan om du vill skapa en användare manuellt:

1. Logga in på webbplatsen iLMS företag som administratör.

1. Klicka på **”registrera användare”** under **användare** fliken för att öppna **registrera användaren** sidan. 
   
   ![Lägg till medarbetare](./media/ilms-tutorial/3.png)

1. På den **”registrera användare”** utför följande steg.

    ![Lägg till medarbetare](./media/ilms-tutorial/create_testuser_add.png)

    a. I den **Förnamn** textrutan först typnamn Britta.
   
    b. I den **efternamn** textrutan anger efternamn Simon.

    c. I den **e-post-ID** textrutan skriver du e-postadressen för Britta Simon konto.

    d. I den **Region** listrutan Välj värdet för regionen.

    e. I den **Division** listrutan Välj värdet för av.

    f. I den **avdelning** listrutan Välj värdet för avdelning.

    g. Klicka på **Spara**.

    > [!NOTE] 
    > Du kan skicka e-post för registrering till användaren genom att välja **skicka e-post från registrering** kryssrutan.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst till iLMS.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon iLMS, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **iLMS**.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/tutorial_ilms_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen iLMS i åtkomstpanelen du bör få automatiskt loggat in på ditt iLMS program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ilms-tutorial/tutorial_general_01.png
[2]: ./media/ilms-tutorial/tutorial_general_02.png
[3]: ./media/ilms-tutorial/tutorial_general_03.png
[4]: ./media/ilms-tutorial/tutorial_general_04.png

[100]: ./media/ilms-tutorial/tutorial_general_100.png

[200]: ./media/ilms-tutorial/tutorial_general_200.png
[201]: ./media/ilms-tutorial/tutorial_general_201.png
[202]: ./media/ilms-tutorial/tutorial_general_202.png
[203]: ./media/ilms-tutorial/tutorial_general_203.png

