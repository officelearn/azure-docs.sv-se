---
title: 'Självstudier: Azure Active Directory-integrering med Organisationsschema nu | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Organisationsschema nu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: jeedes
ms.openlocfilehash: 0320ccdf2e7c7e032e153fa46605d98637246f31
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Självstudier: Azure Active Directory-integrering med Organisationsschema nu

I kursen får lära du att integrera Organisationsschema nu med Azure Active Directory (AD Azure).

Integrera Organisationsschema nu med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till organisationsschema nu.
- Du kan aktivera användarna att automatiskt hämta loggat in på organisationsschema nu (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Organisationsschema nu behöver du följande:

- En Azure AD-prenumeration
- Ett organisationsschema nu enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till organisationsschema nu från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-orgchart-now-from-the-gallery"></a>Att lägga till organisationsschema nu från galleriet
Du måste lägga till organisationsschema nu från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av organisationsschema nu i Azure AD.

**Utför följande steg för att lägga till organisationsschema nu från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Organisationsschema nu**väljer **Organisationsschema nu** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Organisationsschema nu i resultatlistan](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Du konfigurera och testa Azure AD enkel inloggning med Organisationsschema nu baserat på en testanvändare som kallas ”Britta Simon” i det här avsnittet.

Azure AD måste du känna till användaren i Organisationsschema nu motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Organisationsschema nu upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Organisationsschema nu måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Organisationsschema nu](#create-an-orgchart-now-test-user)**  – du har en motsvarighet för Britta Simon Organisationsschema nu som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Organisationsschema nu.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Organisationsschema nu:**

1. I Azure-portalen på den **Organisationsschema nu** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_samlbase.png)

3. På den **Organisationsschema nu domän och URL: er** om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och Organisationsschema nu domän med enkel inloggning information](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_url.png)

    I den **identifierare** textruta, ange ett URL-Adressen: `https://sso2.orgchartnow.com`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Organisationsschema nu domän med enkel inloggning information](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`
     
    > [!NOTE]
    > `<YourEntityID>` SAML-enhets-ID som kopieras från avsnittet Snabbreferens beskrivs senare i självstudiekursen.

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_400.png)
    
7. På den **Organisationsschema nu Configuration** klickar du på **konfigurera Organisationsschema nu** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID** från den **Snabbreferens avsnittet** och använda den för att slutföra **inloggnings-URL** i **Organisationsschema nu domän och URL: er avsnittet**.

    ![Organisationsschema nu konfiguration](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_configure.png) 

8. Konfigurera enkel inloggning på **Organisationsschema nu** sida, måste du skicka den hämtade **XML-Metadata för** till [Organisationsschema nu supportteamet](mailto:ocnsupport@officeworksoftware.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-orgchart-now-test-user"></a>Skapa en testanvändare Organisationsschema nu

Om du vill aktivera Azure AD-användare kan logga in på organisationsschema nu etableras de i Organisationsschema nu. 

1. Organisationsschema nu stöder just-in-time-etablering, vilket är aktiverat som standard. En ny användare skapas under ett försök att komma åt Organisationsschema nu om det inte finns. Just-in-time-funktionen för användaretablering bara skapar en **skrivskyddad** användare när en SSO-begäran kommer från en känd IDP och e-post i SAML-kontroll kan inte hittas i användarlistan. För den här automatiskt etablera funktionen måste du skapa en åtkomstgrupp med titeln **allmänna** Organisationsschema nu. Följ de nedanstående steg för att skapa en åtkomstgrupp:

    a. Gå till den **hantera grupper** alternativet när du klickar på den **redskap** i det övre högra hörnet i Användargränssnittet.

    ![Organisationsschema nu grupper](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_manage.png)  

    b. Välj den **Lägg till** ikon och namn på gruppen **allmänna** Klicka **OK**. 

    ![Organisationsschema nu lägga till](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Välj mapparna du vill att de allmänna eller skrivskyddad användarna för att kunna komma åt:

    ![Organisationsschema nu mappar](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Lås** mappar så att endast administrativa användare kan ändra dem. Tryck på **OK**.

    ![Organisationsschema nu Lås](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Att skapa **Admin** användare och **läsning och skrivning** användare, måste du manuellt skapa en användare för att få åtkomst till sina behörighetsnivå via enkel inloggning. Utför följande steg om du vill konfigurera ett användarkonto:

    a. Logga in på organisationsschema nu som en administratör.

    b.  Klicka på **inställningar** hörn på upp till höger och gå sedan till **hantera användare**.

    ![Organisationsschema nu inställningar](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Klicka på **Lägg till** och utför följande steg:

    ![Organisationsschema nu hantera](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * I den **användar-ID** textruta ange användar-ID som **brittasimon@contoso.com**.

    * I **e-postadress** text Ange e-postadressen för användaren som **brittasimon@contoso.com**.

    * Klicka på **Lägg till**.
    
### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till organisationsschema nu.

![Tilldela rollen][200] 

**Om du vill tilldela Organisationsschema nu Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Organisationsschema nu**.

    ![Organisationsschema nu länken i listan med program](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Organisationsschema nu på åtkomstpanelen du bör få automatiskt loggat in i tillämpningsprogrammet Organisationsschema nu.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_203.png

