---
title: "Självstudier: Azure Active Directory-integrering med Salesforce Sandbox | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce Sandbox."
services: active-directory
documentationCenter: na
author: MarkusVi
manager: mtillman
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: markvi
ms.openlocfilehash: d5c2b74995e0c0794628e2c79f66969835e0fabb
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Självstudier: Azure Active Directory-integrering med Salesforce Sandbox

I kursen får lära du att integrera Salesforce Sandbox med Azure Active Directory (AD Azure).

Integrera Salesforce Sandbox med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Salesforce Sandbox
- Du kan aktivera användarna att automatiskt hämta loggat in på Salesforce begränsat läge (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Salesforce Sandbox behöver du följande:

- En Azure AD-prenumeration
- En Salesforce Sandbox enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Salesforce Sandbox från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Att lägga till Salesforce Sandbox från galleriet
Du måste lägga till Salesforce Sandbox från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Salesforce Sandbox i Azure AD.

**Utför följande steg för att lägga till Salesforce Sandbox från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Salesforce Sandbox**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_search.png)

5. Välj i resultatpanelen **Salesforce Sandbox**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Salesforce Sandbox baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Salesforce Sandbox motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Salesforce Sandbox upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Salesforce Sandbox.

Om du vill konfigurera och testa Azure AD enkel inloggning med Salesforce Sandbox, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Salesforce Sandbox](#creating-a-salesforce-sandbox-test-user)**  – du har en motsvarighet för Britta Simon i Salesforce Sandbox som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Salesforce-Sandbox-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Salesforce Sandbox:**

1. I Azure-portalen på den **Salesforce Sandbox** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. På den **Salesforce Sandbox domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_url.png)

     I den **inloggnings-URL** textruta Skriv det värde som använder följande mönster:`https://<subdomain>.my.salesforce.com`

    > [!NOTE] 
    > Det här värdet är inte verkligt. Uppdatera det här värdet med det faktiska inloggnings-URL. Kontakta [Salesforce Sandbox klienten supportteamet](https://help.salesforce.com/support) att hämta det här värdet.


4. Om du redan har konfigurerat enkel inloggning för en annan Salesforce Sandbox-instans i katalogen, så måste du även konfigurera den **identifierare** ska ha samma värde som den **inloggning URL**. 
    
    >[!Note]
    >Den **identifierare** fältet finns genom att kontrollera den **visa avancerade inställningar** kryssruta på den **konfigurera App-URL** sidan av dialogrutan 


5. På den **SAML-signeringscertifikat** klickar du på **certifikat** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_400.png)

7. På den **Salesforce Sandbox Configuration** klickar du på **konfigurera Salesforce Sandbox** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_configure.png) 
<CS>
8. Öppna en ny flik i webbläsaren och logga in på ditt Salesforce-Sandbox-administratörskonto.

9. Klicka på menyn högst upp **installationsprogrammet**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforcesandbox-tutorial/IC781024.png)
10. I navigeringsfönstret till vänster klickar du på **säkerhetsåtgärder**, och klicka sedan på **inställningar för enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforcesandbox-tutorial/IC781025.png)
11. Utför följande steg i avsnittet Inställningar för enkel inloggning: ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforcesandbox-tutorial/IC781026.png)
     
     a.  Välj **SAML aktiverat**. 

     b.  Klicka på **Ny**.

12. Utför följande steg på avsnittet SAML enkel inloggning inställningar:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforcesandbox-tutorial/IC781027.png)

    a.In textrutan namn skriver du namnet på konfigurationen (t.ex.: *SPSSOWAAD\_Test*). 

    b. Klistra in **SMAL enhets-ID** värde i den **utfärdaren** textruta.

    c. I den **enhets-Id** textruta typen **https://test.salesforce.com** om det är den första Salesforce Sandbox-instans som du lägger till din katalog. Om du redan har lagt till en instans av Salesforce Sandbox sedan för den **enhets-ID** Skriv i den **logga URL**, som ska vara i formatet:`http://company.my.salesforce.com`  
 
    d. Klicka på **Bläddra** att överföra hämtat certifikat.  

    e. Som **SAML identitetstyp**väljer **Assertion innehåller Federation-ID från användarobjektet**.
 
    f. Som **SAML identitet plats**väljer **identitet är i elementet NameIdentifier i instruktionen ämne**.

    g. Klistra in **inloggning tjänst-URL för enkel** till den **identitet providern inloggnings-URL** textruta. 

    h. SFDC har inte stöd för SAML logga ut.  Som en tillfällig lösning kan du klistra in 'https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0' till den **identitet providern logga ut URL** textruta.

    i. Som **providern initierade begära Tjänstbindning**väljer **HTTP POST**. 

    j. Klicka på **Spara**.

### <a name="enable-your-domain"></a>Aktivera din domän
Det här avsnittet förutsätter att du redan har skapat en domän.  Mer information finns i [definierar domännamn](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

**Utför följande steg om du vill aktivera din domän:**

1. I det vänstra navigeringsfönstret klickar du på **domänhantering**, och klicka sedan på **min domän.**
   
     ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforcesandbox-tutorial/IC781029.png)
   
   >[!NOTE]
   >Kontrollera att din domän har konfigurerats korrekt. 

2. I den **sidan inloggningsinställningar** klickar du på **redigera**, sedan som **Autentiseringstjänsten**, Välj namnet på SAML enkel inloggning inställningen från föregående avsnitt och klickar slutligen **spara**.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforcesandbox-tutorial/IC781030.png)

Så snart du har en domän som har konfigurerats, bör användarna använda domän-URL för att logga in till Salesforce sandbox.  

Om du vill hämta värdet för URL: en, klickar du på SSO-profil som du har skapat i föregående avsnitt.    

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare går du till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-salesforce-sandbox-test-user"></a>Skapa en testanvändare Salesforce Sandbox

I det här avsnittet skapas en användare som kallas Britta Simon i Salesforce Sandbox. Salesforce Sandbox stöder just-in-time-allokering som är aktiverad som standard.
Det finns ingen åtgärd objekt i det här avsnittet. Om en användare inte redan finns i Salesforce Sandbox, skapas en ny när du försöker komma åt Salesforce Sandbox.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Salesforce Sandbox.

![Tilldela användare][200] 

**Om du vill tilldela Salesforce Sandbox Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Salesforce Sandbox**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Om du vill testa inställningarna för enkel inloggning, öppna åtkomstpanelen. Mer information om åtkomstpanelen finns [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurera Användaretablering](active-directory-saas-salesforce-sandbox-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_203.png

