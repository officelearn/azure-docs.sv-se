---
title: "Självstudier: Azure Active Directory-integrering med SAML SSO för Jira resolution GmbH | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML SSO för Jira resolution GmbH."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeedes
ms.openlocfilehash: cde5983710185d1e46a5601b16bbfb1c0fcae382
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Självstudier: Azure Active Directory-integrering med SAML SSO för Jira resolution GmbH

I kursen får lära du att integrera SAML SSO för Jira resolution GmbH med Azure Active Directory (AD Azure).

Integrera SAML SSO för Jira resolution GmbH med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SAML SSO för Jira resolution GmbH
- Du kan aktivera användarna att automatiskt hämta loggat in på SAML SSO för Jira resolution GmbH (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med SAML SSO för Jira resolution GmbH, behöver du följande:

- En Azure AD-prenumeration
- En SAML SSO för Jira av upplösning GmbH enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SAML SSO för Jira resolution GmbH från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Att lägga till SAML SSO för Jira resolution GmbH från galleriet
Du måste lägga till SAML SSO för Jira resolution GmbH från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SAML SSO för Jira resolution GmbH i Azure AD.

**Utför följande steg för att lägga till SAML SSO för Jira resolution GmbH från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **SAML SSO för Jira resolution GmbH**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_search.png)

5. Välj i resultatpanelen **SAML SSO för Jira resolution GmbH**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med SAML SSO för Jira resolution GmbH baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, Azure AD som behöver veta vilka motsvarande användaren i SAML SSO för Jira resolution GmbH är att en användare i Azure AD. Med andra ord en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SAML SSO för Jira resolution GmbH måste upprättas.

SAML SSO för Jira resolution GmbH, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med SAML SSO för Jira resolution GmbH, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en SAML SSO för Jira av upplösning GmbH testanvändare](#creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user)**  – du har en motsvarighet för Britta Simon SAML SSO för Jira resolution GmbH som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din SAML SSO för Jira resolution GmbH program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SAML SSO för Jira resolution GmbH:**

1. I Azure-portalen på den **SAML SSO för Jira resolution GmbH** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_samlbase.png)

3. På den **SAML SSO Jira resolution GmbH domän och URL: er** om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_url_1.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster:`https://<server-base-url>/plugins/servlet/samlsso`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster:`https://<server-base-url>/plugins/servlet/samlsso`

4. Kontrollera **visa avancerade inställningar för URL: en**. Om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_url_2.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Kontakta [SAML SSO för Jira resolution GmbH klienten supportteam](https://www.resolution.de/go/support) att hämta dessa värden. 

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samlssojira-tutorial/tutorial_general_400.png)
    
7. I en annan webbläsarfönstret, logga in på ditt **SAML SSO för Jira upplösning GmbH admin Portal** som administratör.

8. Hovra över kugge och klicka på den **tillägg**.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-samlssojira-tutorial/addon1.png)

9. Du omdirigeras till sidan med administratörsåtkomst. Ange den **lösenord** och på **Bekräfta** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samlssojira-tutorial/addon2.png)

10. Klicka under tillägg fliken avsnitt **söka efter nya tillägg**. Sök **SAML enkel inloggning (SSO) för JIRA** och på **installera** för att installera den nya SAML-plugin-programmet.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samlssojira-tutorial/addon7.png)

11. Plugin-installationen startar. Klicka på **Stäng**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samlssojira-tutorial/addon8.png)

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samlssojira-tutorial/addon9.png)

12. Klicka på **Hantera**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samlssojira-tutorial/addon10.png)
    
13. Klicka på **konfigurera** att konfigurera nya plugin-programmet.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samlssojira-tutorial/addon11.png)

14. På **SAML SingleSignOn Plugin Configuration** klickar du på **lägga till ytterligare identitetsleverantör** för att konfigurera inställningarna för identitetsleverantör.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samlssojira-tutorial/addon4.png)

15. Utför följande steg på den här sidan:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samlssojira-tutorial/addon5.png)
 
    a. Lägg till **namn** av identitetsleverantören (t.ex Azure AD).
    
    b. Lägg till **beskrivning** av identitetsleverantören (t.ex Azure AD).

    c. Klicka på **XML** och välj den **Metadata** fil som du har hämtat från Azure-portalen.

    d. Klicka på **belastningen** knappen.

    e. Den läser IdP-metadata och fylls fälten som är markerade i skärmbilden. 

16. Klicka på **Spara inställningar** för att spara inställningarna.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samlssojira-tutorial/addon6.png)

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Skapa en SAML SSO för Jira av upplösning GmbH testanvändare

Om du vill aktivera Azure AD-användare kan logga in på SAML SSO för Jira resolution GmbH etableras de i SAML SSO för Jira resolution GmbH.  
SAML SSO för Jira resolution GmbH är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt SAML SSO för Jira av upplösning GmbH företagets plats som administratör.

2. Hovra över kugge och klicka på den **Användarhantering**.

    ![Lägga till medarbetare](./media/active-directory-saas-samlssojira-tutorial/user1.png) 

3. Du omdirigeras till administratörsåtkomst sidan om du vill ange **lösenord** och på **Bekräfta** knappen.

    ![Lägga till medarbetare](./media/active-directory-saas-samlssojira-tutorial/user2.png) 

4. Under **Användarhantering** avsnittet klickar du på **skapa användare**.

    ![Lägga till medarbetare](./media/active-directory-saas-samlssojira-tutorial/user3.png) 

5. På den **”skapa nya användare”** dialogrutan utför följande steg:

    ![Lägga till medarbetare](./media/active-directory-saas-samlssojira-tutorial/user4.png) 

    a. I den **e-postadress** textruta typen e-postadressen för användaren som Brittasimon@contoso.com.

    b. I den **fullständiga namn** textruta fullständiga typnamnet för användaren som Britta Simon.

    c. I den **användarnamn** textruta, ange den e-posten för användare som Brittasimon@contoso.com.

    d. I den **lösenord** textruta skriver du lösenordet för användaren.

    e. Klicka på **skapa användare**.   

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAML SSO för Jira resolution GmbH.

![Tilldela användare][200] 

**Om du vill tilldela SAML SSO för Jira Britta Simon resolution GmbH, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SAML SSO för Jira resolution GmbH**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på SAML SSO för Jira av upplösning GmbH panelen på åtkomstpanelen du bör få automatiskt loggat in på ditt SAML SSO för Jira resolution GmbH program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_203.png

