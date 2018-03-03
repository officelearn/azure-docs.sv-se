---
title: "Självstudier: Azure Active Directory-integrering med Slack | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Slack."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: jeedes
ms.openlocfilehash: cd0cecde7f98e73911e7dec734cffeeee6f09a72
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Självstudier: Azure Active Directory-integrering med Slack

I kursen får lära du att integrera Slack med Azure Active Directory (AD Azure).

Integrera Slack med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Slack
- Du kan aktivera användarna att automatiskt hämta loggat in på Slack (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Slack, behöver du följande:

- En Azure AD-prenumeration
- En Slack enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Slack från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-slack-from-the-gallery"></a>Att lägga till Slack från galleriet
Du måste lägga till Slack från galleriet i listan över hanterade SaaS-appar för att konfigurera Slack till Azure AD-integrering.

**Utför följande steg för att lägga till Slack från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Slack**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-slack-tutorial/tutorial_slack_search.png)

5. Välj i resultatpanelen **Slack**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-slack-tutorial/tutorial_slack_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Slack baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Slack motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Slack upprättas.

I Slack, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Slack, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en Slack testanvändare](#creating-a-slack-test-user)**  – har en motsvarighet för Britta Simon Slack som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Slack.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Slack:**

1. I Azure-portalen på den **Slack** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-slack-tutorial/tutorial_slack_samlbase.png)

3. På den **Slack domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-slack-tutorial/tutorial_slack_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<companyname>.slack.com`

    b. I den **identifierare** textruta anger du URL: `https://slack.com`

    > [!NOTE] 
    > Värdet är inte verkliga. Du måste uppdatera värdet med faktiska logga på URL: en. Kontakta [Slack supportteamet](https://slack.com/help/contact) värdet hämtas
     
4. Slack program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-slack-tutorial/tutorial_slack_attribute.png)

    > [!NOTE] 
    > Om användaren har **e-postadress** tilldelats med hjälp av Office365 sedan bara det fylls i annat fall den **e-postadress** anspråk visas inte i SAML-Token.

5. I den **användarattribut** avsnitt på den **enkel inloggning** markerar **user.mail** som **användar-ID** och för varje rad som visas i i tabellen nedan, utför följande steg:
    
    | Attributnamn | Attributvärde |
    | --- | --- |
    | first_name | user.givenname |
    | last_name | User.surname |
    | User.Email | User.Mail |  
    | User.Username | user.userprincipalname |

    a. Klicka på **attributet** att öppna **Redigera attribut** dialogrutan rutan och utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-slack-tutorial/tutorial_slack_attribute1.png)

    a. I den **namn** textruta ange attributets namn visas för den raden.
    
    b. Från den **värdet** väljer du det attributvärde som visas för den raden.

    c. Lämna den **Namespace** tomt.
    
    d. Klicka på **OK**

6. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-slack-tutorial/tutorial_slack_certificate.png)

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

8. På den **Slack Configuration** klickar du på **konfigurera Slack** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-slack-tutorial/tutorial_slack_configure.png) 

9.  I en annan webbläsarfönster loggar du in på webbplatsen Slack företag som administratör.

10.  Gå till **Microsoft Azure AD** och gå till **inställningar för Team**.

     ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

11.  I den **inställningar för Team** klickar du på den **autentisering** fliken och klicka sedan på **ändra inställningar för**.

     ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

12. På den **SAML autentiseringsinställningar** dialogrutan, utför följande steg:

    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a.  I den **SAML 2.0 slutpunkt (HTTP)** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.

    b.  I den **identitet providern utfärdaren** textruta klistra in värdet för **SAML enhets-ID**, som du har kopierat från Azure-portalen.

    c.  Öppna filen hämtat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **certifikat med offentlig** textruta.

    d. Konfigurera tre inställningarna ovan som passar din Slack-teamet. Mer information om inställningarna hittar den **Slacks guiden för konfiguration av SSO** här. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Klicka på **spara konfigurationen**.
     
    <!-- Deselect **Allow users to change their email address**.

    e.  Select **Allow users to choose their own username**.

    f.  As **Authentication for your team must be used by**, select **It’s optional**. -->

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-slack-test-user"></a>Skapa en Slack testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Slack. Slack stöder just-in-time-etablering, vilket är aktiverat som standard.

Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt Slack om den inte finns.

> [!NOTE]
> Om du behöver skapa en användare manuellt, måste du kontakta [Slack supportteamet](https://slack.com/help/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Slack.

![Tilldela användare][200] 

**Om du vill tilldela Slack Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Slack**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-slack-tutorial/tutorial_slack_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Slack åtkomst på panelen du ska hämta automatiskt loggat in på ditt Slack program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-slack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png

