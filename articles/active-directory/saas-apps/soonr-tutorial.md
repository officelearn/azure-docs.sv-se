---
title: 'Självstudier: Azure Active Directory-integrering med Soonr arbetsplats | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Soonr arbetsplats.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b75f5f00-ea8b-4850-ae2e-134e5d678d97
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: jeedes
ms.openlocfilehash: bdcce907a5c6c20263edb969f86036d158cc5453
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213200"
---
# <a name="tutorial-azure-active-directory-integration-with-soonr-workplace"></a>Självstudier: Azure Active Directory-integrering med Soonr arbetsplats

I kursen får lära du att integrera Soonr arbetsplats med Azure Active Directory (AD Azure).

Integrera Soonr arbetsplats med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till arbetsplats Soonr
- Du kan aktivera användarna att automatiskt hämta inloggade Soonr arbetsyta (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Soonr arbetsplats, behöver du följande:

- En Azure AD-prenumeration
- En Soonr arbetsplatsen enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Soonr arbetsplats från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-soonr-workplace-from-the-gallery"></a>Att lägga till Soonr arbetsplats från galleriet
Du måste lägga till Soonr arbetsplats från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Soonr arbetsplats i Azure AD.

**Utför följande steg för att lägga till Soonr arbetsplats från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Soonr arbetsplats**.

    ![Skapa en testanvändare i Azure AD](./media/soonr-tutorial/tutorial_soonr_search.png)

5. Välj i resultatpanelen **Soonr arbetsplats**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/soonr-tutorial/tutorial_soonr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
Du konfigurera och testa Azure AD enkel inloggning med Soonr arbetsplats baserat på en testanvändare som kallas ”Britta Simon” i det här avsnittet.

Azure AD måste du känna till motsvarande användaren i Soonr arbetsplats till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Soonr arbetsplatsen upprättas.

I Soonr arbetsplats, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Soonr arbetsplats, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Soonr arbetsplats](#creating-a-soonr-workplace-test-user)**  – du har en motsvarighet för Britta Simon Soonr arbetsplatsen som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Soonr arbetsplats.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Soonr arbetsplats:**

1. I Azure-portalen på den **Soonr arbetsplats** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/soonr-tutorial/tutorial_soonr_samlbase.png)

3. På den **Soonr arbetsplats domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/soonr-tutorial/tutorial_soonr_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<servername>.soonr.com/singlesignon/saml/metadata`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<servername>.soonr.com/singlesignon/saml/SSO`

4. På den **Soonr arbetsplats domän och URL: er** om du vill konfigurera programmet i **SP initierade läge**, utför följande steg:
    
    ![Konfigurera enkel inloggning](./media/soonr-tutorial/tutorial_soonr_url1.png)

    a. Klicka på den **visa avancerade inställningar för URL: en**.

    b. I den **logga URL** textruta Skriv en URL med följande mönster: `https://<servername>.soonr.com/singlesignon/saml/SSO`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren inloggning Webbadressen och svar. Kontakta [Soonr arbetsplats supportteamet](https://awp.autotask.net/help/) att hämta dessa värden.
 
5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/soonr-tutorial/tutorial_soonr_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/soonr-tutorial/tutorial_general_400.png)

7. På den **Soonr arbetsplats Configuration** klickar du på **konfigurera Soonr arbetsplats** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/soonr-tutorial/tutorial_soonr_configure.png) 

8. Konfigurera enkel inloggning på **Soonr arbetsplats** sida, måste du skicka den hämtade **XML-Metadata för**, **Sign-Out URL, SAML enhets-ID och SAML inloggning tjänst-URL för enkel** att [Soonr arbetsplats supportteamet](https://awp.autotask.net/help/). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

    >[!Note]
    >Om du behöver hjälp med att konfigurera Autotask arbetsplats finns [den här sidan](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) att få hjälp med ditt arbetsplatskonto.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/soonr-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/soonr-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/soonr-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/soonr-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-soonr-workplace-test-user"></a>Skapa en testanvändare Soonr arbetsplats

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon Soonr arbetsplatsen. Arbeta med [Soonr arbetsplats supportteamet](https://awp.autotask.net/help/) att skapa en användare i plattformen. Du kan höja supportärende med Soonr från <a href="https://na01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fsoonr.com%2FAWPHelp%2FContent%2F0_HOME%2FSupport_for_End_Clients.htm&data=01%7C01%7Cv-saikra%40microsoft.com%7Ccbb4367ab09b4dacaac408d3eebe3f42%7C72f988bf86f141af91ab2d7cd011db47%7C1&sdata=FB92qtE6m%2Fd8yox7AnL2f1h%2FGXwSkma9x9H8Pz0955M%3D&reserved=0/">här</a>.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till arbetsplats Soonr.

![Tilldela användare][200] 

**Om du vill tilldela Soonr arbetsplats Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Soonr arbetsplats**.

    ![Konfigurera enkel inloggning](./media/soonr-tutorial/tutorial_soonr_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.  

När du klickar på panelen Soonr arbetsplats på åtkomstpanelen du ska hämta automatiskt loggat in på ditt Soonr arbetsplats program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/soonr-tutorial/tutorial_general_01.png
[2]: ./media/soonr-tutorial/tutorial_general_02.png
[3]: ./media/soonr-tutorial/tutorial_general_03.png
[4]: ./media/soonr-tutorial/tutorial_general_04.png

[100]: ./media/soonr-tutorial/tutorial_general_100.png

[200]: ./media/soonr-tutorial/tutorial_general_200.png
[201]: ./media/soonr-tutorial/tutorial_general_201.png
[202]: ./media/soonr-tutorial/tutorial_general_202.png
[203]: ./media/soonr-tutorial/tutorial_general_203.png

