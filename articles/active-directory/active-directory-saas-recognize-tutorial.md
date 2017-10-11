---
title: "Självstudier: Azure Active Directory-integrering med identifiera | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och identifiera."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 97d85183d0307c41a3b879d440d87a6fb0c53190
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Självstudier: Azure Active Directory-integrering med identifiera

I kursen får lära du att integrera identifiera med Azure Active Directory (AD Azure).

Identifiera integrera med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till identifiera
- Du kan aktivera användarna att automatiskt hämta loggat in på identifiera (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med identifiera behöver du följande:

- En Azure AD-prenumeration
- En identifiera enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad här: [utvärderingsversion erbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till identifiera från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-recognize-from-the-gallery"></a>Att lägga till identifiera från galleriet
Du måste lägga till identifiera från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av identifiera i Azure AD.

**Utför följande steg för att lägga till identifiera från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **identifiera**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_search.png)

5. Välj i resultatpanelen **identifiera**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med identifiera baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i identifiera motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i identifiera upprättas.

I identifiera, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med identifiera, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare identifiera](#creating-a-recognize-test-user)**  – har en motsvarighet för Britta Simon identifiera som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i identifiera programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med identifiera:**

1. I Azure-portalen på den **identifiera** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_samlbase.png)

3. På den **URL: er och identifiera domänen** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://recognizeapp.com/<your-domain>/saml/sso`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://recognizeapp.com/<your-domain>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [identifiera klienten supportteamet](mailto:support@recognizeapp.com) få inloggnings-URL och du kan hämta ID-värde genom att öppna URL: en för Service Provider Metadata från avsnittet SSO-inställningarna som beskrivs senare under kursen. . 
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-recognize-tutorial/tutorial_general_400.png)

6. På den **identifiera Configuration** klickar du på **konfigurera identifiera** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_configure.png) 

7. I en annan webbläsarfönster inloggning till identifiera klienten som en administratör.

8. Klicka på det övre högra hörnet **menyn**. Gå till **företagets Admin**.
   
    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)

9. I det vänstra navigeringsfönstret klickar du på **inställningar**.
   
    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)

10. Utför följande steg på **SSO-inställningarna** avsnitt.
   
    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)
    
    a. Som **aktivera enkel inloggning**väljer **på**.

    b. I den **IDP enhets-ID** textruta klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen.
    
    c. I den **mål-url för Sso** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.
    
    d. I den **servicenivåmål mål-url** textruta klistra in värdet för **Sign-Out URL** som du har kopierat från Azure-portalen. 
    
    e. Öppna din hämtade **certifikat (Base64)** fil i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **certifikat** textruta.
    
    f. Klicka på den **Spara inställningar** knappen. 

11. Bredvid den **SSO inställningar** och kopiera Webbadressen under **url för tjänstmetadata providern**.
   
    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)

12. Öppna den **Metadata URL-länk** under en tom webbläsare för att hämta Metadatadokumentet. Kopiera EntityDescriptor value(entityID) från filen och klistra in den i **identifierare** TextBox-kontroll i **URL: er och identifiera domänen avsnittet** på Azure-portalen.
    
    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-recognize-test-user"></a>Skapa en testanvändare identifiera

För att aktivera Azure AD-användare att logga in på identifiera etableras de i identifiera. Identifiera är etablering en manuell aktivitet.

Den här appen stöder inte SCIM etablering men har en annan användare sync som etablerar användare. 

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på webbplatsen identifiera företag som administratör.

2. Klicka på det övre högra hörnet **menyn**. Gå till **företagets Admin**.

3. I det vänstra navigeringsfönstret klickar du på **inställningar**.

4. Utför följande steg på **användaren Sync** avsnitt.
   
   ![Ny användare](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "ny användare")
   
   a. Som **synkronisering aktiverat**väljer **på**.
   
   b. Som **Välj sync providern**väljer **Microsoft / Office 365**.
   
   c. Klicka på **köra användaren synkronisering**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till identifiera.

![Tilldela användare][200] 

**Om du vill tilldela identifiera Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **identifiera**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen identifiera på åtkomstpanelen du bör få automatiskt inloggade att identifiera programmet. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png

