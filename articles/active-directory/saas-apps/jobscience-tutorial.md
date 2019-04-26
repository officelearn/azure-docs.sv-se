---
title: 'Självstudier: Azure Active Directory-integrering med Jobscience | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jobscience.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8199f106c234e216a0982dc9e51413ccf30ae93a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60268697"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Självstudier: Azure Active Directory-integrering med Jobscience

I den här självstudien får du lära dig hur du integrerar Jobscience med Azure Active Directory (AD Azure).

Integrera Jobscience med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Jobscience
- Du kan aktivera användarna att automatiskt få loggat in på Jobscience (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Jobscience, behöver du följande objekt:

- En Azure AD-prenumeration
- En Jobscience enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en testmiljö för Azure AD kan du få en tre månaders kostnadsfri utvärdering här: [Utvärderingserbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Jobscience från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-jobscience-from-the-gallery"></a>Att lägga till Jobscience från galleriet
För att konfigurera integrering av Jobscience i Azure AD, som du behöver lägga till Jobscience från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Jobscience från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Jobscience**.

    ![Skapa en Azure AD-användare för testning](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. I resultatpanelen väljer **Jobscience**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Jobscience baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Jobscience är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Jobscience upprättas.

I Jobscience, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Jobscience, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Jobscience](#creating-a-jobscience-test-user)**  – du har en motsvarighet för Britta Simon i Jobscience som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Jobscience program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Jobscience:**

1. I Azure-portalen på den **Jobscience** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. På den **Jobscience domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster:  `http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Få ut värde [Jobscience klienten supportteamet](https://www.jobscience.com/support) eller från SSO-profil skapas som beskrivs senare i självstudien. 
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/jobscience-tutorial/tutorial_general_400.png)

1. På den **Jobscience Configuration** klickar du på **konfigurera Jobscience** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Logga in på webbplatsen Jobscience företag som administratör.

1. Gå till **installationsprogrammet**.
   
   ![Konfiguration](./media/jobscience-tutorial/IC784358.png "Konfiguration")

1. I det vänstra navigeringsfönstret i den **administrera** klickar du på **domänhantering** Expandera avsnittet relaterade och klicka sedan på **min domän** att öppna **Min domän** sidan. 
   
   ![My Domain](./media/jobscience-tutorial/ic767825.png "My Domain")

1. För att verifiera att din domän har ställts in korrekt, se till att den är i ”**steg 4 distribueras till användarna**” och granska din ”**Mina Domäninställningar**”.

    ![Domän som har distribuerats till användaren](./media/jobscience-tutorial/ic784377.png "domän som har distribuerats till användare")

1. Klicka på webbplatsen Jobscience företagets **säkerhetskontroller**, och klicka sedan på **inställningar för enkel inloggning**.
    
    ![Säkerhetskontroller](./media/jobscience-tutorial/ic784364.png "Säkerhetskontroller")

1. I den **inställningar för enkel inloggning** avsnittet, utför följande steg:
    
    ![Inställningar för enkel inloggning](./media/jobscience-tutorial/ic781026.png "Inställningar för enkel inloggning")
    
    a. Välj **SAML-aktiverat** .

    b. Klicka på **Ny**.

1. På den **SAML enkel inloggning inställningen redigera** dialogrutan utför följande steg:
    
    ![SAML enkel inloggning inställningen](./media/jobscience-tutorial/ic784365.png "SAML enkel inloggning för inställningen")
    
    a. I textrutan **Namn** skriver du ett namn för konfigurationen.

    b. I **utfärdare** textrutan klistra in värdet för **SAML entitets-ID**, som du har kopierat från Azure-portalen.

    c. I den **entitets-Id** textrutan typ `https://salesforce-jobscience.com`

    d. Klicka på **Bläddra** att överföra din Azure AD-certifikatet.

    e. Som **SAML identitetstypen**väljer **Assertion innehåller Federation-ID från användarobjektet**.

    f. Som **SAML identitet plats**väljer **identitet är i NameIdentfier-elementet i instruktionen ämne**.

    g. I **inloggnings-URL för identitetsprovider** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    h. I **utloggnings-URL för identitetsprovider** textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.

    i. Klicka på **Spara**.

1. I det vänstra navigeringsfönstret i den **administrera** klickar du på **domänhantering** Expandera avsnittet relaterade och klicka sedan på **min domän** att öppna **Min domän** sidan. 
    
    ![My Domain](./media/jobscience-tutorial/ic767825.png "My Domain")

1. På den **min domän** sidan den **inloggning anpassningen** klickar du på **redigera**.
    
    ![Inloggningssidan anpassning](./media/jobscience-tutorial/ic767826.png "inloggningssidan för anpassning")

1. På den **inloggning anpassningen** sidan den **autentiseringstjänst** avsnittet, namnet på din **SAML SSO-inställningar** visas. Markera den och klicka sedan på **spara**.
    
    ![Inloggningssidan anpassning](./media/jobscience-tutorial/ic784366.png "inloggningssidan för anpassning")

1. Att hämta SP initiera enkelinloggning för inloggnings-URL-Klicka på den **Single Sign On inställningar** i den **säkerhetskontroller** menyn avsnittet.

    ![Säkerhetskontroller](./media/jobscience-tutorial/ic784368.png "Säkerhetskontroller")
    
    Klicka på SSO-profilen som du har skapat i ovanstående steg. Den här sidan visar enkelinloggning på URL: en för ditt företag (till exempel [ https://companyname.my.salesforce.com?so=companyid ](https://companyname.my.salesforce.com?so=companyid).    

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/jobscience-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-jobscience-test-user"></a>Skapa en Jobscience testanvändare

För att aktivera Azure AD-användare att logga in på Jobscience, måste de etableras i Jobscience. När det gäller Jobscience är etablering en manuell aktivitet.

>[!NOTE]
>Du kan använda alla andra Jobscience användare konto verktyg för att skapa eller API: er som tillhandahålls av Jobscience för att etablera Azure Active Directory användarkonton.
>  
        
**Utför följande steg för att konfigurera användarförsörjning:**

1. Logga in på din **Jobscience** företagets plats som administratör.

1. Gå till installationsprogrammet.
   
   ![Konfiguration](./media/jobscience-tutorial/ic784358.png "Konfiguration")
1. Gå till **hantera användare \> användare**.
   
   ![Användare](./media/jobscience-tutorial/ic784369.png "Användare")
1. Klicka på **Ny användare**.
   
   ![Alla användare](./media/jobscience-tutorial/ic784370.png "alla användare")
1. På den **Redigera användare** dialogrutan utför följande steg:
   
   ![Redigera användare](./media/jobscience-tutorial/ic784371.png "Redigera användare")
   
   a. I den **Förnamn** textrutan anger du ett första namn för användaren som Britta.
   
   b. I den **efternamn** textrutan skriver du ett efternamn för användaren som Simon.
   
   c. I den **Alias** textrutan skriver du ett aliasnamn för användaren som brittas.

   d. I textrutan **E-post** skriver du e-postadressen för användaren: Brittasimon@contoso.com.

   e. I den **användarnamn** textrutan, skriver du in ett användarnamn för användaren som Brittasimon@contoso.com.

   f. I den **smeknamn** textrutan skriver en nick namnet på användaren som Simon.

   g. Klicka på **Spara**.

    
> [!NOTE]
> Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Jobscience.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Jobscience, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Jobscience**.

    ![Konfigurera enkel inloggning](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Jobscience i åtkomstpanelen du bör få automatiskt loggat in på ditt Jobscience program.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png

