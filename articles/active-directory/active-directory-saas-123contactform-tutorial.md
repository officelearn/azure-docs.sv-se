---
title: "Självstudier: Azure Active Directory-integrering med 123ContactForm | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och 123ContactForm."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 3a99f0841c3e0d973168991f5dbee40e54c1d054
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-123contactform"></a>Självstudier: Azure Active Directory-integrering med 123ContactForm

I kursen får lära du att integrera 123ContactForm med Azure Active Directory (AD Azure).

Integrera 123ContactForm med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till 123ContactForm
- Du kan aktivera användarna att automatiskt hämta loggat in på 123ContactForm (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med 123ContactForm, behöver du följande:

- En Azure AD-prenumeration
- En 123ContactForm enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till 123ContactForm från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-123contactform-from-the-gallery"></a>Att lägga till 123ContactForm från galleriet
Du måste lägga till 123ContactForm från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av 123ContactForm i Azure AD.

**Utför följande steg för att lägga till 123ContactForm från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **123ContactForm**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_search.png)

5. Välj i resultatpanelen **123ContactForm**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med 123ContactForm baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i 123ContactForm motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i 123ContactForm upprättas.

I 123ContactForm, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med 123ContactForm, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare 123ContactForm](#creating-a-123contactform-test-user)**  – du har en motsvarighet för Britta Simon i 123ContactForm som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt 123ContactForm program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med 123ContactForm:**

1. I Azure-portalen på den **123ContactForm** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_samlbase.png)

3. På den **123ContactForm domän och URL: er** om du vill konfigurera programmet i **IDP initierade läge**, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-123contactform-tutorial/url1.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster:`https://www.123contactform.com/saml/azure_ad/<tenant_id>/metadata`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster:`https://www.123contactform.com/saml/azure_ad/<tenant_id>/acs`

4. Om du vill konfigurera programmet i **SP initierade läge**, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-123contactform-tutorial/url2.png)

    a. Klicka på den **visa avancerade inställningar för URL: en** alternativet

    b. I den **logga URL** textruta Skriv en URL som:`https://www.123contactform.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Du behöver uppdatera dessa värde från faktiska URL: er och identifierare som beskrivs senare i självstudierna.
    
5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-123contactform-tutorial/tutorial_general_400.png)

7. Konfigurera enkel inloggning på **123ContactForm** sida, gå till [https://www.123contactform.com/form-2709121/](https://www.123contactform.com/form-2709121/) och utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-123contactform-tutorial/submit.png) 

    a. I den **e-post** textruta Skriv e-postadressen för användaren engångsfaktorautentisering **BrittaSimon@Contoso.com**.

    b. Klicka på **överför** och bläddra i XML-Metadata för filen som du har hämtat från Azure-portalen.

    c. Klicka på **skicka formuläret**.

8. På den **konfigurera Appinställningar i Microsoft Azure AD enkel inloggning -** utför följande steg:
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-123contactform-tutorial/url3.png)

    a. Om du vill konfigurera programmet i **IDP initierade läge**, kopiera den **IDENTIFIERARE** värde för din instans och klistra in den i **identifierare** TextBox-kontroll i **123ContactForm domän och URL: er** avsnitt på Azure-portalen.
    
    b. Om du vill konfigurera programmet i **IDP initierade läge**, kopiera den **REPLY URL** värde för din instans och klistra in den i **Reply URL** TextBox-kontroll i **123ContactForm domän och URL: er** avsnitt på Azure-portalen.

    c. Om du vill konfigurera programmet i **SP initierade läge**, kopiera den **SIGN ON URL** värde för din instans och klistra in den i **logga URL** TextBox-kontroll i **123ContactForm domän och URL: er** avsnitt på Azure-portalen.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-123contactform-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-123contactform-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-123contactform-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-123contactform-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-123contactform-test-user"></a>Skapa en testanvändare 123ContactForm

Programmet stöder bara i tid användaretablering och authentication-användare kommer automatiskt att skapas i programmet.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till 123ContactForm.

![Tilldela användare][200] 

**Om du vill tilldela 123ContactForm Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **123ContactForm**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen 123ContactForm på åtkomstpanelen du bör få automatiskt loggat in på ditt 123ContactForm program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_203.png

