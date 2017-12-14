---
title: "Självstudier: Azure Active Directory-integrering med PolicyStat | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PolicyStat."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: c50c42e888a905cc886c68ff1937b84c34b55280
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Självstudier: Azure Active Directory-integrering med PolicyStat

I kursen får lära du att integrera PolicyStat med Azure Active Directory (AD Azure).

Integrera PolicyStat med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till PolicyStat
- Du kan aktivera användarna att automatiskt hämta loggat in på PolicyStat (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med PolicyStat, behöver du följande:

- En Azure AD-prenumeration
- En PolicyStat enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till PolicyStat från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-policystat-from-the-gallery"></a>Att lägga till PolicyStat från galleriet
Du måste lägga till PolicyStat från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av PolicyStat i Azure AD.

**Utför följande steg för att lägga till PolicyStat från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **PolicyStat**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_search.png)

5. Välj i resultatpanelen **PolicyStat**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med PolicyStat baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i PolicyStat motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i PolicyStat upprättas.

I PolicyStat, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med PolicyStat, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare PolicyStat](#creating-a-policystat-test-user)**  – du har en motsvarighet för Britta Simon i PolicyStat som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt PolicyStat program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med PolicyStat:**

1. I Azure-portalen på den **PolicyStat** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_samlbase.png)

3. På den **PolicyStat domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<companyname>.policystat.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [PolicyStat klienten supportteamet](http://www.policystat.com/support/) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_certificate.png) 

5. Syftet med det här avsnittet är att beskriva hur användarna att autentisera till PolicyStat med sitt konto i Azure AD med hjälp av federation baserat på SAML-protokoll.

    Programmet PolicyStat förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till attributmappningar till din **SAML-Token attribut** konfiguration.  

     Följande skärmbild visar ett exempel på detta.

     ![Attribut](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_attribute.png "attribut")

6. Utför följande steg för att lägga till nödvändiga attributmappning:

    | Attributnamn    |   Attributvärde |
    |------------------- | -------------------- |
    | UID | ExtractMailPrefix([mail]) |
    
    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_04.png)

    ![Konfigurera enkel inloggning](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_addatribute.png)
    
    b. I den **attributnamn** textruta typen **uid**.

    c. I den **attributvärdet** textruta väljer **ExtractMailPrefix()**.    
   
    d. Från den **e** väljer **User.mail**.
    
    e. Klicka på **Ok**

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-policystat-tutorial/tutorial_general_400.png)

8. Logga in på webbplatsen PolicyStat företag som en administratör i en annan webbläsarfönster.

9. Klicka på den **Admin** fliken och klicka sedan på **konfiguration för enkel inloggning** i vänstra navigeringsfönstret.
   
    ![Administratörsmenyn](./media/active-directory-saas-policystat-tutorial/ic808633.png "administratör-menyn")

10. I den **installationsprogrammet** väljer **aktivera enkel inloggning Integration**.
   
    ![Enkel inloggning Configuration](./media/active-directory-saas-policystat-tutorial/ic808634.png "enkel inloggning konfiguration")

11. Klicka på **Konfigurera attribut**, och sedan, i den **Konfigurera attribut** avsnittet, utför följande steg:
   
    ![Enkel inloggning Configuration](./media/active-directory-saas-policystat-tutorial/ic808635.png "enkel inloggning konfiguration")
   
    a. I den **Username-attributet** textruta typen **uid**.

    b. I den **förnamn attributet** textruta typen **Förnamn** för användare **Britta**.

    c. I den **senaste namnattributet** textruta typen **efternamn** för användare **Simon**.

    d. I den **e-attributet** textruta typen **emailaddress** för användare  **BrittaSimon@contoso.com** .

    e. Klicka på **spara ändringar**.

12. Klicka på **din IDP Metadata**, och sedan, i den **din IDP Metadata** avsnittet, utför följande steg:
   
    ![Enkel inloggning Configuration](./media/active-directory-saas-policystat-tutorial/ic808636.png "enkel inloggning konfiguration")
   
    a. Öppna metadatafilen hämtade, kopiera innehållet och klistrar in det i den **din identitet providern Metadata** textruta.

    b. Klicka på **spara ändringar**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-policystat-test-user"></a>Skapa en testanvändare PolicyStat

För att aktivera Azure AD-användare att logga in på PolicyStat etableras de i PolicyStat.  

PolicyStat stöder bara i tid användaretablering. Det innebär du behöver inte lägga till användare manuellt till PolicyStat. Användarna ska få automatiskt läggas till på deras första inloggning via enkel inloggning.

>[!NOTE]
>Du kan använda något annat PolicyStat användarens konto skapas verktyg eller API: er som tillhandahålls av PolicyStat att etablera Azure AD-användarkonton.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till PolicyStat.

![Tilldela användare][200] 

**Om du vill tilldela PolicyStat Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **PolicyStat**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen PolicyStat på åtkomstpanelen du bör få automatiskt loggat in på ditt PolicyStat program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_203.png

