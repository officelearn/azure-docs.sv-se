---
title: "Självstudier: Azure Active Directory-integrering med ServiceNow | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ServiceNow och ServiceNow Express."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d8eb99e-8ce5-4ba4-8b54-5c3d9ae573f6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: a91fab90a94b655b93c8ae9064ea4836b80d7678
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Självstudier: Azure Active Directory-integrering med ServiceNow
I kursen får lära du att integrera ServiceNow och ServiceNow Express med Azure Active Directory (AD Azure).

Integrera ServiceNow och ServiceNow Express med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till ServiceNow och ServiceNow Express
* Du kan aktivera användarna att automatiskt hämta loggat in på ServiceNow och ServiceNow Express (Single Sign-On) med sina Azure AD-konton
* Du kan hantera dina konton i en central plats – den klassiska Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav
För att konfigurera Azure AD-integrering med ServiceNow och ServiceNow Express, behöver du följande:

* En Azure AD-prenumeration
* För ServiceNow, instans eller innehavare för ServiceNow Calgary version eller högre
* För ServiceNow Express, en instans av ServiceNow Express, Helsingfors version eller högre
* ServiceNow-klient måste ha den [flera enkel inloggning på Plugin-providern](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) aktiverat. Detta kan göras med [att skicka en serviceförfrågan](https://hi.service-now.com). 

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.
> 
> 

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

* Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
* Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till ServiceNow från galleriet
2. Konfigurera och testa Azure AD enkel inloggning för ServiceNow eller ServiceNow Express

## <a name="adding-servicenow-from-the-gallery"></a>Att lägga till ServiceNow från galleriet
Du måste lägga till ServiceNow från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av ServiceNow eller ServiceNow Express i Azure AD. 

**Utför följande steg för att lägga till ServiceNow från galleriet:**

1. I den **klassiska Azure-portalen**, klicka på det vänstra navigeringsfönstret **Active Directory**. 
   
    ![Active Directory][1]
2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.
3. Klicka för att öppna vyn program i vyn directory **program** på huvudmenyn.
   
    ![Program][2]
4. Klicka på **Lägg till** längst ned på sidan.
   
    ![Program][3]
5. På den **vad vill du göra** dialogrutan klickar du på **lägga till ett program från galleriet**.
   
    ![Program][4]
6. I sökrutan skriver **ServiceNow**.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)
7. I resultatfönstret, Välj **ServiceNow**, och klicka sedan på **Slutför** lägga till programmet.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
Du konfigurera och testa Azure AD enkel inloggning med ServiceNow eller ServiceNow Express baserat på en testanvändare som kallas ”Britta Simon” i det här avsnittet.

Azure AD måste du känna till användaren i ServiceNow motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i ServiceNow upprättas.
Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i ServiceNow. Om du vill konfigurera och testa Azure AD enkel inloggning med ServiceNow, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning för ServiceNow](#configuring-azure-ad-single-sign-on-for-servicenow)**  - om du vill att användarna kan använda den här funktionen.
2. **[Konfigurera Azure AD enkel inloggning för ServiceNow Express](#configuring-azure-ad-single-sign-on-for-servicenow-express)**  - om du vill att användarna kan använda den här funktionen.
3. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Skapa en testanvändare ServiceNow](#creating-a-servicenow-test-user)**  – du har en motsvarighet för Britta Simon i ServiceNow som är kopplad till Azure AD-representation av henne.
5. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
6. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

> [!NOTE]
> Om du vill konfigurera ServiceNow utelämna steg 2. Likaså om du vill konfigurera ServiceNow Express utelämna steg 1.
> 
> 

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>Konfigurera Azure AD-Single Sign-On för ServiceNow
1. I den klassiska Azure AD-portalen på den **ServiceNow** integreringssidan för programmet, klickar du på **Konfigurera enkel inloggning** att öppna den **Konfigurera enkel inloggning** dialogrutan.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Konfigurera enkel inloggning")

2. På den **hur vill du att användarna kan logga in på ServiceNow** väljer **Microsoft Azure AD enkel inloggning**, och klicka sedan på **nästa**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Konfigurera enkel inloggning")

3. På den **konfigurera Appinställningar** utför följande steg:
   
    ![Konfigurera app-URL](./media/active-directory-saas-servicenow-tutorial/IC769497.png "konfigurera app-URL")
   
    a. i den **ServiceNow logga URL** textruta, Skriv URL: en som används av dina användare logga in i tillämpningsprogrammet ServiceNow följer mönstret: `https://<instance-name>.service-now.com`.
   
    b. I den **identifierare** textruta, Skriv URL: en används av dina användare logga in i tillämpningsprogrammet ServiceNow följer mönstret: `https://<instance-name>.service-now.com`.
   
    c. Klicka på **Nästa**

4. Om du vill att Azure AD automatiskt konfigurera ServiceNow för SAML-baserad autentisering, ange ditt ServiceNow-instansnamnet och administratörsanvändarnamnet admin-lösenordet i den **automatiskt konfigurera enkel inloggning** formuläret och klicka på *konfigurera*. Observera att administratörsanvändarnamnet tillhandahålls måste ha den **security_admin** roll i ServiceNow för detta ska fungera. Annars för att manuellt konfigurera ServiceNow för att använda Azure AD som en SAML-identitetsprovider, klickar du på **manuellt konfigurera program för enkel inloggning**, klicka på **nästa** och utför följande steg.
   
    ![Konfigurera app-URL](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "konfigurera app-URL")

5. På den **Konfigurera enkel inloggning på ServiceNow** klickar du på **hämta certifikat**, spara certifikatfilen lokalt på datorn.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Konfigurera enkel inloggning")

6. Inloggning till ServiceNow-programmet som administratör.

7. Aktivera den *integrering – flera enkel inloggning installationsprogrammet för providern* plugin-programmet genom att följa nästa steg:
   
    a. I navigeringsfönstret till vänster, gå till **System Definition** avsnittet och klicka sedan på **plugin-program**.
   
    ![Konfigurera app-URL](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "aktivera plugin-program")
   
    b. Sök efter *integrering – flera enkel inloggning installationsprogrammet för providern*.
   
    ![Konfigurera app-URL](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "aktivera plugin-program")
   
    c. Välj plugin-programmet. Rigth Klicka och välj **aktivera eller uppgradering**.
   
    d. Klicka på den **aktivera** knappen.

8. I navigeringsfönstret till vänster klickar du på **egenskaper**.  
   
    ![Konfigurera app-URL](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "konfigurera app-URL")

9. På den **flera egenskaper för SSO** dialogrutan, utför följande steg:
   
    ![Konfigurera app-URL](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "konfigurera app-URL")
   
    a. Som **aktivera flera providern SSO**väljer **Ja**.
   
    b. Som **Aktivera felsökningsloggning har flera providern SSO integration**väljer **Ja**.
   
    c. I **fältet på användaren tabell som...**  textruta typen **user_name**.
   
    d. Klicka på **Spara**.

10. I navigeringsfönstret till vänster klickar du på **x509 certifikat**.
    
     ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Konfigurera enkel inloggning")

11. På den **X.509-certifikat** dialogrutan klickar du på **ny**.
    
     ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Konfigurera enkel inloggning")

12. På den **X.509-certifikat** dialogrutan, utför följande steg:
    
     ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Konfigurera enkel inloggning")
    
     a. Klicka på **Ny**.
    
     b. I den **namn** textruta, ange ett namn för din konfiguration (t.ex.: **TestSAML2.0**).
    
     c. Välj **Active**.
    
     d. Som **Format**väljer **PEM**.
    
     e. Som **typen**väljer **förtroende Store Cert**.
    
     f. Öppna Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **PEM certifikat** textruta.
    
     g. Klicka på **uppdatering**.

13. I navigeringsfönstret till vänster klickar du på **identitetsleverantörer**.
    
     ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Konfigurera enkel inloggning")

14. På den **identitetsleverantörer** dialogrutan klickar du på **ny**:
    
     ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Konfigurera enkel inloggning")

15. På den **identitetsleverantörer** dialogrutan klickar du på **SAML2 Update1?**:
    
     ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Konfigurera enkel inloggning")

16. I dialogrutan Egenskaper för SAML2 Update1 utför du följande steg:
    
     ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Konfigurera enkel inloggning")

    a. i den **namn** textruta, ange ett namn för din konfiguration (t.ex.: **SAML 2.0**).

    b. I den **Info** textruta typen **e-post** eller **user_name**, beroende på vilket fält som används för att unikt identifiera användarna i din ServiceNow-distribution. 

    > [!NOTE] 
    > Du kan configue Azure AD för att generera Azure AD användar-ID (användarens huvudnamn) eller e-postadressen som den unika identifieraren i SAML-token genom att gå till den **ServiceNow > attribut > enkel inloggning** avsnitt i den klassiska Azure-portalen och mappa det önskade fältet som ska den **nameidentifier** attribut. Värdet för det markerade attributet i Azure AD (t.ex. användarens huvudnamn) måste överensstämma med värdet som lagras i ServiceNow för det angivna fältet (t.ex. användarnamn)

    c. I den klassiska Azure AD-portalen kopiera den **identitet Provider-ID** värdet och klistrar in det i den **identitet providern URL** textruta.

    d. I den klassiska Azure AD-portalen kopiera den **autentiserings-URL för begäran** värdet och klistrar in det i den **identitetsleverantörens AuthnRequest** textruta.

    e. I den klassiska Azure AD-portalen kopiera den **tjänst-URL för enkel Sign-Out** värdet och klistrar in det i den **identitetsleverantörens SingleLogoutRequest** textruta.

    f. I den **ServiceNow Homepage** textruta ange Webbadressen till startsidan för ServiceNow-instans.

    > [!NOTE] 
    > Startsidan för ServiceNow-instans är en sammansättning av din **ServieNow klient URL** och **/navpage.do** (t.ex.:`https://fabrikam.service-now.com/navpage.do`).

    g. I den **enhets-ID / utfärdaren** textruta anger du URL för din ServiceNow-klient.

    h. I den **målgruppen URL** textruta anger du URL för din ServiceNow-klient. 

    Jag. I den **protokollet bindningen för den IDP SingleLogoutRequest** textruta typen **urn: oasis: namn: tc: SAML:2.0:bindings:HTTP-omdirigera**.

    j. Ange i textrutan NameID princip **urn: oasis: namn: tc: SAML:1.1:nameid-format: Okänt**.

    k. Avmarkera **skapa en AuthnContextClass**.

    l. I den **AuthnContextClassRef metoden**, typen `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`. Det här krävs bara om du endast organisation i molnet. Om du använder en lokal AD FS eller MFA för autentisering bör du inte konfigurera det här värdet. 

    m. I **klockan skeva** textruta typen **60**.

    n. Som **enkel inloggning på skriptet**väljer **MultiSSO_SAML2_Update1**.

    o. Som **x509 certifikat**, Välj det certifikat som du har skapat i föregående steg.

    p. Klicka på **skicka**. 

1. Välj bekräftelsen konfiguration för enkel inloggning på den klassiska Azure AD-portalen och klicka sedan på **nästa**. 
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Konfigurera enkel inloggning")

2. På den **enkel inloggning bekräftelse** klickar du på **Slutför**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Konfigurera enkel inloggning")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>Konfigurera Azure AD-Single Sign-On för ServiceNow Express
1. I den klassiska Azure AD-portalen på den **ServiceNow** integreringssidan för programmet, klickar du på **Konfigurera enkel inloggning** att öppna den **Konfigurera enkel inloggning** dialogrutan.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Konfigurera enkel inloggning")

2. På den **hur vill du att användarna kan logga in på ServiceNow** väljer **Microsoft Azure AD enkel inloggning**, och klicka sedan på **nästa**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Konfigurera enkel inloggning")

3. På den **konfigurera Appinställningar** utför följande steg:
   
    ![Konfigurera app-URL](./media/active-directory-saas-servicenow-tutorial/IC769497.png "konfigurera app-URL")
   
    a. i den **ServiceNow logga URL** textruta, Skriv URL: en som används av dina användare logga in i tillämpningsprogrammet ServiceNow följer mönstret: `https://<instance-name>.service-now.com`.
   
    b. I den **utfärdar-URL** textruta, Skriv URL: en används av dina användare logga in i tillämpningsprogrammet ServiceNow följer mönstret `https://<instance-name>.service-now.com`.
   
    c. Klicka på **Nästa**

4. Klicka på **manuellt konfigurera program för enkel inloggning**, klicka på **nästa** och utför följande steg.
   
    ![Konfigurera app-URL](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "konfigurera app-URL")

5. På den **Konfigurera enkel inloggning på ServiceNow** klickar du på **hämta certifikat**, spara certifikatfilen lokalt på datorn och klicka sedan på **nästa**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Konfigurera enkel inloggning")

6. Inloggning till ServiceNow Express-programmet som administratör.

7. I navigeringsfönstret till vänster klickar du på **enkel inloggning**.  
   
    ![Konfigurera app-URL](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "konfigurera app-URL")

8. På den **enkel inloggning** dialogrutan, klicka på ikonen konfigurationen i det övre högra hörnet och ange följande egenskaper:
   
    ![Konfigurera app-URL](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "konfigurera app-URL")
   
    a. Växla **aktivera flera providern SSO** till höger.
   
    b. Växla **Aktivera felsökningsloggning för flera providern SSO integration** till höger.
   
    c. I **fältet på användaren tabell som...**  textruta typen **user_name**.
9. På den **enkel inloggning** dialogrutan klickar du på **Lägg till nytt certifikat**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Konfigurera enkel inloggning")
10. På den **X.509-certifikat** dialogrutan, utför följande steg:
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Konfigurera enkel inloggning")
    
    a. I den **namn** textruta, ange ett namn för din konfiguration (t.ex.: **TestSAML2.0**).
    
    b. Välj **Active**.
    
    c. Som **Format**väljer **PEM**.
    
    d. Som **typen**väljer **förtroende Store Cert**.
    
    e. Skapa en Base64-kodad fil från din hämtat certifikat.
    
    > [!NOTE]
    > Mer information finns i [hur du konverterar ett binärt certifikat till en textfil](http://youtu.be/PlgrzUZ-Y1o).
    > 
    > 
    
    f. Öppna Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **PEM certifikat** textruta.
    
    g. Klicka på **uppdatering**.
11. På den **enkel inloggning** dialogrutan klickar du på **lägga till nya IdP**.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Konfigurera enkel inloggning")
12. På den **lägga till nya identitetsleverantör** dialogrutan under **konfigurera identitetsleverantören**, utför följande steg:
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Konfigurera enkel inloggning")

    a. i den **namn** textruta, ange ett namn för din konfiguration (t.ex.: **SAML 2.0**).

    b. I den klassiska Azure AD-portalen kopiera den **identitet Provider-ID** värdet och klistrar in det i den **identitet providern URL** textruta.

    c. I den klassiska Azure AD-portalen kopiera den **autentiserings-URL för begäran** värdet och klistrar in det i den **identitetsleverantörens AuthnRequest** textruta.

    d. I den klassiska Azure AD-portalen kopiera den **tjänst-URL för enkel Sign-Out** värdet och klistrar in det i den **identitetsleverantörens SingleLogoutRequest** textruta.

    e. Som **providern identitetscertifikat**, Välj det certifikat som du har skapat i föregående steg.


1. Klicka på **avancerade inställningar**, och under **ytterligare identitetsegenskaper providern**, utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Konfigurera enkel inloggning")
   
    a. I den **protokollet bindningen för den IDP SingleLogoutRequest** textruta typen **urn: oasis: namn: tc: SAML:2.0:bindings:HTTP-omdirigera**.
   
    b. I den **NameID princip** textruta typen **urn: oasis: namn: tc: SAML:1.1:nameid-format: Okänt**.    
   
    c. I den **AuthnContextClassRef metoden**, typen **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.
   
    d. Avmarkera **skapa en AuthnContextClass**.

2. Under **ytterligare egenskaper**, utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Konfigurera enkel inloggning")
   
    a. I den **ServiceNow Homepage** textruta ange Webbadressen till startsidan för ServiceNow-instans.
   
    > [!NOTE]
    > Startsidan för ServiceNow-instans är en sammansättning av din **ServieNow klient URL** och **/navpage.do** (t.ex.: `https://fabrikam.service-now.com/navpage.do`).
    > 
    > 
   
    b. I den **enhets-ID / utfärdaren** textruta anger du URL för din ServiceNow-klient.
   
    c. I den **Målgrupps-URI** textruta anger du URL för din ServiceNow-klient. 
   
    d. I **klockan skeva** textruta typen **60**.
   
    e. I den **Info** textruta typen **e-post** eller **user_name**, beroende på vilket fält som används för att unikt identifiera användarna i din ServiceNow-distribution.
   
    > [!NOTE]
    > Du kan configue Azure AD för att generera Azure AD användar-ID (användarens huvudnamn) eller e-postadressen som den unika identifieraren i SAML-token genom att gå till den **ServiceNow > attribut > enkel inloggning** avsnitt i den klassiska Azure-portalen och mappa det önskade fältet som ska den **nameidentifier** attribut. Värdet för det markerade attributet i Azure AD (t.ex. användarens huvudnamn) måste överensstämma med värdet som lagras i ServiceNow för det angivna fältet (t.ex. användarnamn)
    > 
    > 
   
    f. Klicka på **Spara**. 

3. Välj bekräftelsen konfiguration för enkel inloggning på den klassiska Azure AD-portalen och klicka sedan på **nästa**. 
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Konfigurera enkel inloggning")

4. På den **enkel inloggning bekräftelse** klickar du på **Slutför**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Konfigurera enkel inloggning")

## <a name="configuring-user-provisioning"></a>Konfigurera användaretablering
Syftet med det här avsnittet är att beskriva hur du aktiverar användaretablering för Active Directory-användarkonton till ServiceNow.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Utför följande steg för att konfigurera användaretablering:
1. I den klassiska Azure Management-portalen på den **ServiceNow** integreringssidan för programmet, klickar du på **konfigurera användaretablering**. 
   
    ![Användaretablering](./media/active-directory-saas-servicenow-tutorial/IC769498.png "användaretablering")

2. På den **ange dina autentiseringsuppgifter för ServiceNow om du vill aktivera automatisk användaretablering** anger du följande inställningar:
   
     a. I den **ServiceNow instansnamn** textruta skriver du namnet på ServiceNow-instansen.
   
     b. I den **ServiceNow-administratörsanvändarnamnet** textruta skriver du namnet på ServiceNow-administratörskonto.
   
     c. I den **ServiceNow adminlösenord** textruta skriver du lösenordet för det här kontot.
   
     d. Klicka på **Validera** att kontrollera konfigurationen.
   
     e. Klicka på den **nästa** för att öppna den **nästa steg** sidan.
   
     f. Om du vill etablera alla användare till det här programmet, väljer du ”**automatiskt etablera alla användarkonton i katalogen för det här programmet**”. 
   
    ![Nästa steg](./media/active-directory-saas-servicenow-tutorial/IC698804.png "nästa steg")
   
     g. På den **nästa steg** klickar du på **Slutför** att spara konfigurationen.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
I det här avsnittet kan du skapa en testanvändare i den klassiska portalen kallas Britta Simon.

![Skapa Azure AD-användare][20]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **klassiska Azure-portalen**, klicka på det vänstra navigeringsfönstret **Active Directory**.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.

3. Klicka för att visa en lista över användare, på menyn upp **användare**.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. Öppna den **Lägg till användare** i verktygsfältet längst ned i dialogrutan klickar du på **Lägg till användare**.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. På den **berätta om den här användaren** dialogrutan utför följande steg:
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 
   
    a. Välj ny användare i din organisation som typ av användare.
   
    b. I användarnamnet **textruta**, typen **BrittaSimon**.
   
    c. Klicka på **Nästa**.

6. På den **användarprofil** dialogrutan utför följande steg:
   
   ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 
   
   a. I den **Förnamn** textruta typen **Britta**.  
   
   b. I den **efternamn** textruta typ, **Simon**.
   
   c. I den **visningsnamn** textruta typen **Britta Simon**.
   
   d. I den **rollen** väljer **användaren**.
   
   e. Klicka på **Nästa**.

7. På den **skaffa tillfälligt lösenord** dialogrutan sidan, klickar du på **skapa**.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. På den **skaffa tillfälligt lösenord** dialogrutan utför följande steg:
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 
   
    a. Anteckna värdet för den **nytt lösenord**.
   
    b. Klicka på **Complete** (Slutför).   

### <a name="creating-a-servicenow-test-user"></a>Skapa en testanvändare ServiceNow
I det här avsnittet skapar du en användare som kallas Britta Simon i ServiceNow. I det här avsnittet skapar du en användare som kallas Britta Simon i ServiceNow. Om du inte vet hur du lägger till en användare i ditt ServiceNow eller snabba ServiceNow-konto, Kontakta supportteamet för ServiceNow.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare
I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja sin åtkomst till ServiceNow.

![Tilldela användare][200] 

**Om du vill tilldela ServiceNow Britta Simon utför du följande steg:**

1. På den klassiska portalen för att öppna vyn program i katalogen vyn klickar du på **program** på huvudmenyn.
   
    ![Tilldela användare][201] 

2. Välj i listan med program **ServiceNow**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

3. Klicka på menyn högst upp **användare**.
   
    ![Tilldela användare][203] 

4. Välj i listan alla användare **Britta Simon**.

5. Klicka på i verktygsfältet längst ned i **tilldela**.
   
    ![Tilldela användare][205]

### <a name="testing-single-sign-on"></a>Testa enkel inloggning
Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen ServiceNow på åtkomstpanelen du bör få automatiskt loggat in på ditt ServiceNow-program.

## <a name="additional-resources"></a>Ytterligare resurser
* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png
