---
title: "Självstudier: Azure Active Directory-integrering med Hightail | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Hightail."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e15206ac-74b0-46e4-9329-892c7d242ec0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: ba55f9b62d274aa3eb91723c62b53f54de0891b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>Självstudier: Azure Active Directory-integrering med Hightail

I kursen får lära du att integrera Hightail med Azure Active Directory (AD Azure).

Integrera Hightail med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Hightail
- Du kan aktivera användarna att automatiskt hämta loggat in på Hightail (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Hightail, behöver du följande:

- En Azure AD-prenumeration
- En Hightail enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Hightail från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-hightail-from-the-gallery"></a>Att lägga till Hightail från galleriet
Du måste lägga till Hightail från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Hightail i Azure AD.

**Utför följande steg för att lägga till Hightail från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Hightail**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_search.png)

5. Välj i resultatpanelen **Hightail**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Hightail baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Hightail motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Hightail upprättas.

I Hightail, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Hightail, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Hightail](#creating-a-hightail-test-user)**  – du har en motsvarighet för Britta Simon i Hightail som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Hightail program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Hightail:**

1. I Azure-portalen på den **Hightail** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_samlbase.png)

3. På den **Hightail domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_url.png)

     I den **Reply URL** textruta ange Webbadressen som:`https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse`

    > [!NOTE] 
    > Föregående värde är inte verkliga värde. Du uppdaterar värdet med faktiska Reply-URL, vilket beskrivs senare i självstudierna.
 
4. På den **Hightail domän och URL: er** om du vill konfigurera programmet i **SP initierade läge**, utför följande steg:
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_url1.png)

    a. Klicka på den **visa avancerade inställningar för URL: en**.

    b. I den **logga URL** textruta ange Webbadressen som:`https://www.hightail.com/loginSSO`

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_certificate.png) 

5. Hightail program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den **”Atrribute”** för programmet. Följande skärmbild visar ett exempel för det här. 

    ![Konfigurera enkel inloggning](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_attribute.png) 

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden och utför följande steg:
    
    | Attributets namn | Attributvärdet |
    | ------------------- | -------------------- |
    | Förnamn | User.givenName |
    | Efternamn | User.surname |
    | E-post | User.Mail |    
    | Användaridentiteten | User.Mail |
    
    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-hightail-tutorial/tutorial_officespace_04.png)

    ![Konfigurera enkel inloggning](./media/active-directory-saas-hightail-tutorial/tutorial_officespace_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. Lämna den **Namespace** tomt.
    
    e. Klicka på **OK**.

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-hightail-tutorial/tutorial_general_400.png)

8. På den **Hightail Configuration** klickar du på **konfigurera Hightail** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_configure.png) 

    >[!NOTE] 
    >Innan du konfigurerar den enkel inloggning på Hightail app kan du lista för tillåten din e-postdomän med Hightail team så att alla användare som använder den här domänen kan använda enkel inloggning funktioner.


9. För att få SSO konfigurerats för ditt program måste logga in till din Hightail-klient som administratör.
   
    a. Klicka på menyn högst upp i **konto** fliken och markera **konfigurera SAML**.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_001.png) 

    b. Markera kryssrutan för **aktivera SAML-autentisering**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_002.png) 

    c. Öppna din Base64-kodade certifikatet i anteckningar som hämtas från Azure-portalen, kopiera innehållet i den till Urklipp och klistra in den till den **SAML Tokensigneringscertifikatet** textruta.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_003.png) 

    d. I den **SAML Authority (identitetsleverantör)** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** kopieras från Azure-portalen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_004.png)

    e. Om du vill konfigurera programmet i **IDP initierade läge** Välj **”identitetsprovider (IdP) initierade logga in”**. Om **SP initierade läge** Välj **”ServiceProvider (SP) initierade logga in”**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_006.png)

    f. Kopiera URL för SAML-konsumenten för din instans och klistra in den i **Reply URL** TextBox-kontroll i **Hightail domän och URL: er** avsnitt på Azure-portalen.
    
    g. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-hightail-test-user"></a>Skapa en testanvändare Hightail

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Hightail. 

Det finns ingen åtgärd objekt i det här avsnittet. Hightail stöder just-in-time-användaretablering baserat på de anpassa anspråk. Om du har konfigurerat anpassade anspråk som visas i avsnittet  **[konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  ovan, skapas automatiskt en användare i program som det inte finns. 

>[!NOTE]
>Om du behöver skapa en användare manuellt, måste du kontakta den [Hightail supportteamet](mailto:support@hightail.com). 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Hightail.

![Tilldela användare][200] 

**Om du vill tilldela Hightail Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Hightail**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Hightail på åtkomstpanelen du bör få automatiskt loggat in på ditt Hightail program.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_203.png

