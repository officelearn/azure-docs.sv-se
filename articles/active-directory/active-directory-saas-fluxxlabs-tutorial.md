---
title: 'Självstudier: Azure Active Directory-integrering med Fluxx Labs | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Fluxx övningar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: jeedes
ms.openlocfilehash: 0bba820c14c5eddc6db99923e3fb1de58c110f4c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Självstudier: Azure Active Directory-integrering med Fluxx Labs

I kursen får lära du att integrera Fluxx Labs med Azure Active Directory (AD Azure).

Integrera Fluxx Labs med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Fluxx övningar.
- Du kan aktivera användarna att automatiskt hämta loggat in på Fluxx Labs (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Fluxx Labs, behöver du följande:

- En Azure AD-prenumeration
- En Fluxx Labs enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Fluxx Labs från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-fluxx-labs-from-the-gallery"></a>Att lägga till Fluxx Labs från galleriet
Du måste lägga till Fluxx Labs från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Fluxx Labs i Azure AD.

**Utför följande steg för att lägga till Fluxx Labs från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Fluxx Labs**väljer **Fluxx Labs** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Fluxx Labs i resultatlistan](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Fluxx Labs baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Fluxx Labs motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Fluxx Labs upprättas.

I Fluxx Labs, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Fluxx Labs, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Fluxx Labs](#create-a-fluxx-labs-test-user)**  – du har en motsvarighet för Britta Simon i Fluxx Labs som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Fluxx övningar.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Fluxx Labs:**

1. I Azure-portalen på den **Fluxx Labs** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_samlbase.png)

3. På den **Fluxx Labs domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och Fluxx Labs domän med enkel inloggning information](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster:

    | Miljö | URL-mönster|
    |-------------|------------|
    | Produktion | `https://<subdomain>.fluxx.io` |
    | Förproduktion | `https://<subdomain>.preprod.fluxxlabs.com`|
        
    b. I den **Reply URL** textruta Skriv en URL med följande mönster:

    | Miljö | URL-mönster|
    |-------------|------------|
    | Produktion | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Förproduktion | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|
        
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare och Reply-URL. Kontakta [Fluxx Labs supportteam](mailto:travis@fluxxlabs.com) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_400.png)

6. På den **Fluxx Labs Configuration** klickar du på **konfigurera Fluxx Labs** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Fluxx Labs konfiguration](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_configure.png)

7. I en annan webbläsarfönster inloggning till webbplatsen Fluxx Labs företag som administratör.

8. Välj **Admin** under den **inställningar** avsnitt.

    ![Fluxx Labs konfiguration](./media/active-directory-saas-fluxxlabs-tutorial/config1.png)

9. Markera Admin-panelen **plugins** > **integreringar** och välj sedan **SAML SSO-(Disabled)**

    ![Fluxx Labs konfiguration](./media/active-directory-saas-fluxxlabs-tutorial/config2.png)
    
10. Utför följande steg i avsnittet attribut:
    
    ![Fluxx Labs konfiguration](./media/active-directory-saas-fluxxlabs-tutorial/config3.png)

    a. Välj den **SAML SSO** kryssrutan.

    b. I den **begära sökvägen** textruta typen **/auth/saml**.

    c. I den **motringning sökvägen** textruta typen **/auth/saml/callback**.

    d. I den **Assertion konsumenten Service Url(Single Sign-On URL)** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.

    e. I den **målgruppen (SP enhets-ID)** textruta klistra in värdet för **SAML enhets-ID**, som du har kopierat från Azure-portalen.

    f. Öppna din Base64-kodade certifikatet i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **providern identitetscertifikat** textruta.

    g. I **namnidentifierare Format** textruta anger du värdet `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    h. Klicka på **Spara**.

    > [!NOTE]
    > När innehållet sparas, fältet visas tomt för säkerhet, men värdet har sparats i konfigurationen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
  
### <a name="create-a-fluxx-labs-test-user"></a>Skapa en testanvändare Fluxx Labs

Om du vill aktivera Azure AD-användare kan logga in på Fluxx Labs, måste de etableras i Fluxx övningar. När det gäller Fluxx Labs är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på webbplatsen Fluxx Labs företag som administratör.

2. Klicka på den nedan visas **ikonen**.

    ![Fluxx Labs konfiguration](./media/active-directory-saas-fluxxlabs-tutorial/config6.png)

3. På instrumentpanelen, klickar du på den nedan visas ikonen för att öppna den **nya personer** kort.

    ![Fluxx Labs konfiguration](./media/active-directory-saas-fluxxlabs-tutorial/config4.png)

4. På den **nya personer** avsnittet, utför följande steg:
    
    ![Fluxx Labs konfiguration](./media/active-directory-saas-fluxxlabs-tutorial/config5.png)

    a. Fluxx labben använda e-post som den unika identifieraren för SSO-inloggning. Fyll i den **SSO UID** med användarens e-postadress som matchar den e-postadress som de använder som inloggningen med enkel inloggning.

    b. Klicka på **Spara**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Fluxx övningar.

![Tilldela rollen][200] 

**Om du vill tilldela Fluxx Labs Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Fluxx Labs**.

    ![Länken Fluxx Labs i listan med program](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Fluxx Labs på åtkomstpanelen du ska hämta automatiskt loggat in på ditt Fluxx Labs-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_203.png
