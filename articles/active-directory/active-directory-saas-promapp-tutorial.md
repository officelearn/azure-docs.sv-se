---
title: "Självstudier: Azure Active Directory-integrering med Promapp | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Promapp."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: jeedes
ms.openlocfilehash: a9cd70b048d454009d8741f394fed0b6b93fcab7
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2017
---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>Självstudier: Azure Active Directory-integrering med Promapp

I kursen får lära du att integrera Promapp med Azure Active Directory (AD Azure).

Integrera Promapp med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Promapp
- Du kan aktivera användarna att automatiskt hämta loggat in på Promapp (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Promapp, behöver du följande:

- En Azure AD-prenumeration
- En Promapp enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Promapp från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-promapp-from-the-gallery"></a>Att lägga till Promapp från galleriet
Du måste lägga till Promapp från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Promapp i Azure AD.

**Utför följande steg för att lägga till Promapp från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Promapp**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_search.png)

5. Välj i resultatpanelen **Promapp**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Promapp baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Promapp motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Promapp upprättas.

I Promapp, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Promapp, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Promapp](#creating-a-promapp-test-user)**  – du har en motsvarighet för Britta Simon i Promapp som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Promapp program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Promapp:**

1. I Azure-portalen på den **Promapp** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_samlbase.png)

3. På den **Promapp domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster:
    | |
    |--|
    | `https://demo.promapp.com/TENANTNAME`|
    | `https://go.promapp.com/TENANTNAME`|
    | `https://demoau.promapp.com/TENANTNAME`|
    | `https://au.promapp.com/TENANTNAME`|
    | `https://demous.promapp.com/TENANTNAME`|
    | `https://us.promapp.com/TENANTNAME`|
    | `https://dev.promapp.com/TENANTNAME`|
    | `https://test.promapp.com/TENANTNAME`|
    | `https://staging.promapp.com/TENANTNAME`|
    
    b. I den **Reply URL** textruta Skriv en URL med följande mönster:`https://DOMAINNAME.promapp.com/azuread/saml/authenticate.aspx`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://DOMAINNAME.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska inloggnings-URL, identifierare och Reply-URL. Kontakta [Promapp klienten supportteamet](https://www.promapp.com/about-us/contact-us/) att hämta dessa värden.

5. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-promapp-tutorial/tutorial_general_400.png)

7. På den **Promapp Configuration** klickar du på **konfigurera Promapp** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_configure.png) 

8. Inloggning på webbplatsen Promapp företag som administratör. 

9. Klicka på menyn högst upp **Admin**. 
   
    ![Azure AD-Single Sign-On][12]

10. Klicka på **Konfigurera**. 
   
    ![Azure AD-Single Sign-On][13]

11. På den **säkerhet** dialogrutan, utför följande steg:
   
    ![Azure AD-Single Sign-On][14]
    
    a. Klistra in **SAML enkel inloggning Tjänstwebbadress**, som du har kopierat från Azure-portalen i den **SSO inloggnings-URL** textruta.
    
    b. Som **SSO - läget för enkel inloggning**väljer **valfritt**, och klicka sedan på **spara**.

    > [!NOTE]
    > **Valfria** läge är endast för testning. När du är nöjd med konfiguration, Välj **krävs** läge för att tillämpa alla användare att autentisera med Azure AD.

    c. Öppna hämtade certifikatet i anteckningar, kopiera certifikatet innehållet utan att den första raden (---**börjar certifikat**---) och den sista raden (---**END CERTIFICATE**---), klistrar in den i  **SSO-x.509-certifikat** textruta och klicka sedan på **spara**.
        
> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-promapp-test-user"></a>Skapa en testanvändare Promapp

Promapp programmet stöder Just-in-Time-etablering. Det innebär ett konto skapas automatiskt om det behövs vid ett försök att komma åt programmet med hjälp av panelen åtkomst.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Promapp.

![Tilldela användare][200] 

**Om du vill tilldela Promapp Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Promapp**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.

Att testa ditt program i **IDP** initierade-läge när du klickar på panelen Promapp på panelen åtkomst du ska hämta automatiskt loggat in på ditt Promapp program.

Att testa ditt program i **SP** initierade läge, måste du starta autentisering från webbplatsen Promapp. Detta kan göras genom att lämna lösenordsfältet tomt när du loggar in samtidigt **valfritt** läge är aktiverat.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_04.png
[12]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_07.png

[100]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_203.png

