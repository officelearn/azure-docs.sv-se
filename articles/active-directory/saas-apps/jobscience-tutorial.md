---
title: 'Självstudiekurs: Azure Active Directory-integrering med Jobscience | Microsoft-dokument'
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
ms.openlocfilehash: 23d2694aa4936090367cf881379f81911ae70f9d
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870546"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Självstudiekurs: Azure Active Directory-integrering med Jobscience

I den här självstudien får du lära dig hur du integrerar Jobscience med Azure Active Directory (Azure AD).

Genom att integrera Jobscience med Azure AD får du följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Jobscience
- Du kan göra det möjligt för användarna att automatiskt logga in på Jobscience (Enkel inloggning) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats - Azure-portalen

Om du vill veta mer om Integrering av SaaS-appar med Azure AD läser du [vad som är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Jobscience behöver du följande objekt:

- En Azure AD-prenumeration
- En jobscience-prenumeration med enkel inloggning

> [!NOTE]
> Vi rekommenderar att du inte använder en produktionsmiljö för att testa stegen i den här självstudien.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en utvärderingsmiljö för Azure AD kan du få en utvärderingsversion på en månad här: [Utvärderingsversionserbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien testar du azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två huvudbyggstenar:

1. Lägga till Jobscience från galleriet
1. Konfigurera och testa enkel inloggning i Azure AD

## <a name="adding-jobscience-from-the-gallery"></a>Lägga till Jobscience från galleriet
Om du vill konfigurera integreringen av Jobscience i Azure AD måste du lägga till Jobscience från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Jobscience från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen. 

    ![Active Directory][1]

1. Navigera till **Enterprise-program**. Gå sedan till **Alla program**.

    ![Program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Program][3]

1. Skriv **Jobscience**i sökrutan .

    ![Skapa en testanvändare för Azure AD](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. Välj **Jobscience**på resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

    ![Skapa en testanvändare för Azure AD](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning i Azure AD
I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med Jobscience baserat på en testanvändare som heter "Britta Simon".

För att enkel inloggning ska fungera måste Azure AD veta vad motpartsanvändaren i Jobscience är för en användare i Azure AD. Med andra ord måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Jobscience upprättas.

I Jobscience tilldelar du värdet för **användarnamnet** i Azure AD som värdet för **användarnamnet** för att upprätta länkrelationen.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Jobscience måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med Britta Simon.
1. **[Skapa en Jobscience-testanvändare](#creating-a-jobscience-test-user)** - att ha en motsvarighet till Britta Simon i Jobscience som är kopplad till Azure AD-representationen av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assigning-the-azure-ad-test-user)** – så att Britta Simon kan använda azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)** - för att kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning i Azure AD

I det här avsnittet aktiverar du enkel Azure AD-inloggning i Azure-portalen och konfigurerar enkel inloggning i ditt Jobscience-program.

**Så här konfigurerar du en enkel Azure AD-inloggning med Jobscience:**

1. Klicka på Enkel inloggning på sidan **Jobscience-programintegration** på **Azure-portalen.**

    ![Konfigurera enkel inloggning][4]

1. I dialogrutan **Enkel inloggning** väljer du **Läge** som **SAML-baserad inloggning** för att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. Gör följande i avsnittet **Jobscience Domain and URL:er:**

    ![Konfigurera enkel inloggning](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    Skriv en URL i textrutan **Sign-on URL** med följande mönster:`http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Få det här värdet av [Jobscience Client supportteam](http://www.jobscience.com/support) eller från SSO-profilen du kommer att skapa som förklaras senare i handledningen. 
 
1. Klicka på **Certifikat (Base64)** i avsnittet **SAML-signeringscertifikat** och spara sedan certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/jobscience-tutorial/tutorial_general_400.png)

1. Klicka på **Konfigurera jobbvetenskap** i avsnittet **Jobscience-konfiguration** för att öppna **Konfigurera inloggningsfönster.** Kopiera **url:en för ut signering, SAML-enhets-ID och SAML-tjänsten för enkel inloggning** från **avsnittet Snabbreferens.**

    ![Konfigurera enkel inloggning](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Logga in på din jobscience-företagswebbplats som administratör.

1. Gå till **installationsprogrammet**.
   
   ![Setup](./media/jobscience-tutorial/IC784358.png "Setup")

1. Klicka på **Domänhantering** i avsnittet **Administrera** i det vänstra navigeringsfönstret för att expandera det relaterade avsnittet och klicka sedan på **Min domän** för att öppna sidan **Min domän.** 
   
   ![Min domän](./media/jobscience-tutorial/ic767825.png "Min domän")

1. Kontrollera att domänen har konfigurerats korrekt genom att kontrollera att den finns i "**Steg 4 distribuerad till användare**" och granska dina " Mina**domäninställningar**".

    ![Domän som distribueras till användare](./media/jobscience-tutorial/ic784377.png "Domän som distribueras till användare")

1. Klicka på **Säkerhetskontroller**på webbplatsen Jobscience och klicka sedan på **Inställningar för enkel inloggning**.
    
    ![Säkerhetskontroller](./media/jobscience-tutorial/ic784364.png "Säkerhetskontroller")

1. Gör följande i avsnittet **Inställningar för enkel inloggning:**
    
    ![Inställningar för enkel inloggning](./media/jobscience-tutorial/ic781026.png "Inställningar för enkel inloggning")
    
    a. Välj **SAML-aktiverat **.

    b. Klicka på **Ny**.

1. Gör följande i dialogrutan **Saml Single Sign-On Setting Edit:**
    
    ![SAML Enkel inloggningsinställning](./media/jobscience-tutorial/ic784365.png "SAML Enkel inloggningsinställning")
    
    a. I textrutan **Namn** skriver du ett namn för konfigurationen.

    b. Klistra in värdet för **SAML-entitets-ID**i **Textrutan Utfärdare** som du har kopierat från Azure-portalen.

    c. Skriv textrutan **Entitets-ID** i textrutan`https://salesforce-jobscience.com`

    d. Klicka på **Bläddra** om du vill ladda upp ditt Azure AD-certifikat.

    e. Som **SAML-identitetstyp**väljer du **Kontroll innehåller federations-ID:et från användarobjektet**.

    f. Som **SAML-identitetsplats**väljer du **Identitet i elementet NameIdentfier i ämnesutdraget**.

    g. I **textrutan för url för identitetsproviders inloggning** klistrar du in värdet **för SAML Single Sign-On Service URL**, som du har kopierat från Azure-portalen.

    h. I URL-textrutan **för identitetsprovsidentotyp** klistrar du in värdet **för utloggnings-URL**, som du har kopierat från Azure-portalen.

    i. Klicka på **Spara**.

1. Klicka på **Domänhantering** i avsnittet **Administrera** i det vänstra navigeringsfönstret för att expandera det relaterade avsnittet och klicka sedan på **Min domän** för att öppna sidan **Min domän.** 
    
    ![Min domän](./media/jobscience-tutorial/ic767825.png "Min domän")

1. Klicka på **Redigera**i avsnittet **Branding av inloggningssida** på sidan **Min domän.**
    
    ![Varumärkesprofilering för inloggningssida](./media/jobscience-tutorial/ic767826.png "Varumärkesprofilering för inloggningssida")

1. På sidan Branding av **inloggningssida** visas namnet på **dina SAML SSO-inställningar** i avsnittet **Autentiseringstjänst.** Markera den och klicka sedan på **Spara**.
    
    ![Varumärkesprofilering för inloggningssida](./media/jobscience-tutorial/ic784366.png "Varumärkesprofilering för inloggningssida")

1. För att få SP initierade Single Sign on Login URL klicka på **inställningarna för enkel inloggning** i **menyavsnittet för säkerhetskontroller.**

    ![Säkerhetskontroller](./media/jobscience-tutorial/ic784368.png "Säkerhetskontroller")
    
    Klicka på den SSO-profil som du har skapat i steget ovan. På den här sidan visas url:en enkel `https://companyname.my.salesforce.com?so=companyid`inloggning för ditt företag (till exempel .    

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om den inbäddade dokumentationsfunktionen här: [Azure AD inbäddad dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare för Azure AD
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa Azure AD-användare][100]

**Så här skapar du en testanvändare i Azure AD:**

1. Klicka på Azure Active Directory i det vänstra navigeringsfönstret i **Azure-portalen**. **Azure Active Directory**

    ![Skapa en testanvändare för Azure AD](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Om du vill visa listan över användare går du till **Användare och grupper** och klickar på Alla **användare**.
    
    ![Skapa en testanvändare för Azure AD](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Om du vill öppna dialogrutan **Användare** klickar du på **Lägg till** högst upp i dialogrutan.
 
    ![Skapa en testanvändare för Azure AD](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Gör följande på dialogrutan **Användare:**
 
    ![Skapa en testanvändare för Azure AD](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. Skriv **BrittaSimon**i textrutan **Namn** .

    b. Skriv **e-postadressen** till BrittaSimon i textrutan **Användarnamn.**

    c. Välj **Visa lösenord** och skriv ned värdet för **lösenordet**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-jobscience-test-user"></a>Skapa en jobscience-testanvändare

För att Azure AD-användare ska kunna logga in på Jobscience måste de etableras i Jobscience. När det gäller Jobscience är etablering en manuell aktivitet.

>[!NOTE]
>Du kan använda andra verktyg för att skapa jobbvetenskapsanvändare eller API:er som tillhandahålls av Jobscience för att etablera Azure Active Directory-användarkonton.
>  
        
**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på **jobscience-företagets** webbplats som administratör.

1. Gå till installationsprogrammet.
   
   ![Setup](./media/jobscience-tutorial/ic784358.png "Setup")
1. Gå till **Hantera användare \> **.
   
   ![Användare](./media/jobscience-tutorial/ic784369.png "Användare")
1. Klicka på **Ny användare**.
   
   ![Alla användare](./media/jobscience-tutorial/ic784370.png "Alla användare")
1. Gör följande i dialogrutan **Redigera användare:**
   
   ![Redigera användare](./media/jobscience-tutorial/ic784371.png "Redigera användare")
   
   a. Skriv ett förnamn för användaren som Britta i textrutan **Förnamn.**
   
   b. Skriv ett efternamn på användaren som Simon i textrutan **Efternamn.**
   
   c. Skriv ett aliasnamn för användaren som brittas i textrutan **Alias.**

   d. I textrutan **E-post** skriver du e-postadressen för användaren: Brittasimon@contoso.com.

   e. Skriv **User Name** ett användarnamn för användaren som Brittasimon@contoso.com.

   f. Skriv ett smeknamn för användare som Simon i textrutan **Smeknamn.**

   g. Klicka på **Spara**.

    
> [!NOTE]
> Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Jobscience.

![Tilldela användare][200] 

**Så här tilldelar du Britta Simon till Jobscience:**

1. Öppna programvyn i Azure-portalen och navigera sedan till katalogvyn och gå till **Enterprise-program** och klicka sedan på **Alla program**.

    ![Tilldela användare][201] 

1. Välj **Jobscience**i programlistan .

    ![Konfigurera enkel inloggning](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. Klicka på Användare och **grupper**på menyn till vänster .

    ![Tilldela användare][202] 

1. Klicka på knappen **Lägg till**. Välj sedan **Användare och grupper** i dialogrutan Lägg till **tilldelning.**

    ![Tilldela användare][203]

1. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan Användare.

1. Klicka på **Knappen Välj** i dialogrutan Användare **och grupper.**

1. Klicka på **Knappen Tilldela** i dialogrutan Lägg **till tilldelning.**
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Jobscience i åtkomstpanelen bör du automatiskt logga in på din Jobscience-applikation.
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

