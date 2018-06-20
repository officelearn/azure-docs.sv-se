---
title: 'Självstudier: Azure Active Directory-integrering med Kantega SSO för Bitbucket | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kantega SSO för Bitbucket.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: dddf05946685b09500c74e9f678b638ee54870bb
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223162"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Självstudier: Azure Active Directory-integrering med Kantega SSO för Bitbucket

I kursen får lära du att integrera Kantega SSO för Bitbucket med Azure Active Directory (AD Azure).

Integrera Kantega SSO för Bitbucket med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Kantega SSO för Bitbucket
- Du kan aktivera användarna att automatiskt hämta loggat in på Kantega SSO för Bitbucket (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Kantega SSO för Bitbucket, behöver du följande:

- En Azure AD-prenumeration
- En Kantega SSO för Bitbucket enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Kantega SSO för Bitbucket från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>Att lägga till Kantega SSO för Bitbucket från galleriet
Du måste lägga till Kantega SSO för Bitbucket från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Kantega SSO för Bitbucket i Azure AD.

**Utför följande steg för att lägga till Kantega SSO för Bitbucket från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Kantega SSO för Bitbucket**.

    ![Skapa en testanvändare i Azure AD](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_search.png)

5. Välj i resultatpanelen **Kantega SSO för Bitbucket**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Kantega SSO för Bitbucket baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren Kantega SSO för Bitbucket till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren Kantega SSO för Bitbucket upprättas.

Kantega SSO för Bitbucket, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Kantega SSO för Bitbucket, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en Kantega SSO för Bitbucket testanvändare](#creating-a-kantega-sso-for-bitbucket-test-user)**  – du har en motsvarighet för Britta Simon Kantega SSO för Bitbucket som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din Kantega SSO för Bitbucket-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Kantega SSO för Bitbucket:**

1. I Azure-portalen på den **Kantega SSO för Bitbucket** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_samlbase.png)

3. I **IDP** initieras läge på den **Kantega SSO Bitbucket domän och URL: er** avsnittet utföra följande steg:

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_url1.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

4. I **SP** initierade läge, kontrollera **visa avancerade inställningar för URL: en** och utföra följande steg:

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_url2.png)
    
    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Dessa värden tas emot under konfigurationen av Bitbucket plugin-programmet som beskrivs senare i självstudierna.

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/tutorial_general_400.png)

7. I en annan webbläsarfönster logga du in på administrationsportalen Bitbucket som administratör.

8. Klicka på kugghjulet och på den **söka efter nya tillägg**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon1.png)

9. Sök **Kantega SSO för Bitbucket SAML & Kerberos** och på **installera** för att installera den nya SAML-plugin-programmet.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon2.png)

10. Plugin-installationen startar.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon31.png)

11. När installationen är klar. Klicka på **Stäng**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon33.png)

12. Klicka på **Hantera**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon34.png)
    
13. Klicka på **konfigurera** att konfigurera nya plugin-programmet.    

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon35.png)

14. I den **SAML** avsnitt. Välj **Azure Active Directory (AD Azure)** från den **Lägg till identitetsleverantör** listrutan.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon4.png)

15. Välj prenumerationsnivån som **grundläggande**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon5.png)

16. På den **appegenskaper** avsnittet, gör du följande:

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. Kopiera den **App-ID URI** värde och använda det som **identifierare, Reply URL och inloggnings-URL** på den **Kantega SSO Bitbucket domän och URL: er** avsnitt i Azure-portalen.

    b. Klicka på **Nästa**.

17. På den **Metadata importera** avsnittet, gör du följande:

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. Välj **metadatafil på datorn**, och överföra metadata-fil som du har hämtat från Azure-portalen.

    b. Klicka på **Nästa**.

18. På den **och enkel inloggning** avsnittet, gör du följande:

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. Lägg till namnet på den identitetsleverantör i **identitet providernamn** textruta (t.ex Azure AD).

    b. Klicka på **Nästa**.

19. Kontrollera signeringscertifikatet och klicka på **nästa**.  

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon9.png)

20. På den **Bitbucket användarkonton** avsnittet, gör du följande:

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. Välj **skapa användare i Bitbuckets interna katalogen om det behövs** och ange rätt namn i gruppen för användare (kan vara flera Nej. av (grupper avgränsade med kommatecken).

    b. Klicka på **Nästa**.

21. Klicka på **Slutför**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon11.png)

22. På den **kända domäner för Azure AD** avsnittet, gör du följande: 

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a. Välj **kända domäner** från den vänstra panelen på sidan.

    b. Ange domännamnet i den **kända domäner** textruta.

    c. Klicka på **Spara**.  

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/kantegassoforbitbucket-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/kantegassoforbitbucket-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/kantegassoforbitbucket-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/kantegassoforbitbucket-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-kantega-sso-for-bitbucket-test-user"></a>Skapa en Kantega SSO för Bitbucket testanvändare

Om du vill aktivera Azure AD-användare kan logga in på Bitbucket, måste de etableras i Bitbucket. Kantega SSO för Bitbucket är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på webbplatsen Bitbucket företag som administratör.

2. Klicka på inställningsikonen.

    ![Lägga till medarbetare](./media/kantegassoforbitbucket-tutorial/user1.png) 

3. Under **Administration** avsnittet klickar du på **användare**.

    ![Lägga till medarbetare](./media/kantegassoforbitbucket-tutorial/user2.png)

4. Klicka på **skapa användare**.

    ![Lägga till medarbetare](./media/kantegassoforbitbucket-tutorial/user3.png)   

5. På den **skapa användare** dialogrutan utför följande steg:

    ![Lägga till medarbetare](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. I den **användarnamn** textruta, ange den e-posten för användare som Brittasimon@contoso.com.
    
    b. I den **fullständiga namn** textruta fullständiga typnamnet för användaren som Britta Simon.
    
    c. I den **e-postadress** textruta typen e-postadressen för användaren som Brittasimon@contoso.com.

    d. I den **lösenord** textruta skriver du lösenordet för användaren.  

    e. I den **Bekräfta lösenord** textruta ditt lösenord för användaren.

    f. Klicka på **skapa användare**.   

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Kantega SSO för Bitbucket.

![Tilldela användare][200] 

**Om du vill tilldela Kantega SSO för Bitbucket Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Kantega SSO för Bitbucket**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på Kantega SSO för Bitbucket-panelen på åtkomstpanelen du bör få automatiskt loggat in på ditt Kantega SSO för Bitbucket-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_01.png
[2]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_02.png
[3]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_03.png
[4]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_04.png

[100]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_100.png

[200]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_200.png
[201]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_201.png
[202]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_202.png
[203]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_203.png

