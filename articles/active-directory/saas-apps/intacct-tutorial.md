---
title: 'Självstudier: Azure Active Directory-integrering med Intacct | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Intacct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 1fb8a1ac4474e91671710ce1f84980f296ecd8df
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224274"
---
# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>Självstudier: Azure Active Directory-integrering med Intacct

I kursen får lära du att integrera Intacct med Azure Active Directory (AD Azure).

Integrera Intacct med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Intacct
- Du kan aktivera användarna att automatiskt hämta loggat in på Intacct (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Intacct, behöver du följande:

- En Azure AD-prenumeration
- En Intacct enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Intacct från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-intacct-from-the-gallery"></a>Att lägga till Intacct från galleriet
Du måste lägga till Intacct från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Intacct i Azure AD.

**Utför följande steg för att lägga till Intacct från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Intacct**.

    ![Skapa en testanvändare i Azure AD](./media/intacct-tutorial/tutorial_intacct_search.png)

5. Välj i resultatpanelen **Intacct**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/intacct-tutorial/tutorial_intacct_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Intacct baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Intacct motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Intacct upprättas.

I Intacct, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Intacct, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Intacct](#creating-an-intacct-test-user)**  – du har en motsvarighet för Britta Simon i Intacct som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Intacct program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Intacct:**

1. I Azure-portalen på den **Intacct** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/intacct-tutorial/tutorial_intacct_samlbase.png)

3. På den **Intacct domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/intacct-tutorial/tutorial_intacct_url.png)

    I den **Reply URL** textruta Skriv en URL med följande mönster:
    | |
    |--|
    | `https://<companyname>.intacct.com/ia/acct/sso_response.phtml`|
    | `https://www.intacct.com/ia/acct/sso_response.phtml` |

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska Reply-URL. Kontakta [Intacct supportteamet](https://us.intacct.com/support) att hämta det här värdet.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/intacct-tutorial/tutorial_intacct_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/intacct-tutorial/tutorial_general_400.png)

6. På den **Intacct Configuration** klickar du på **konfigurera Intacct** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/intacct-tutorial/tutorial_intacct_configure.png) 

7. I en annan webbläsarfönster loggar du in på ditt Intacct företagets webbplats som administratör.

8. Klicka på den **företagets** fliken och klicka sedan på **företagets information**.

    ![Företagets](./media/intacct-tutorial/ic790037.png "företag")

9. Klicka på den **säkerhet** fliken och klicka sedan på **redigera**.

    ![Säkerhet](./media/intacct-tutorial/ic790038.png "säkerhet")

10. I den **enkel inloggning (SSO)** avsnittet, utför följande steg:

    ![Enkel inloggning](./media/intacct-tutorial/ic790039.png "enkel inloggning")

    a. Välj **aktivera enkel inloggning på**.

    b. Som **identitet providertyp**väljer **SAML 2.0**.

    c. I **utfärdar-URL** textruta klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen.
   
    d. I **Inloggningswebbadressen** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    e. Öppna din **Base64-** kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **certifikat** rutan.
   
    f. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/intacct-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/intacct-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/intacct-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/intacct-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-intacct-test-user"></a>Skapa en testanvändare Intacct

Om du vill konfigurera Azure AD-användare så att de kan logga in på Intacct, måste de etableras i Intacct. Intacct är etablering en manuell aktivitet.

**Utför följande steg för att etablera användarkonton:**

1. Logga in på ditt **Intacct** klient.

2. Klicka på den **företagets** fliken och klicka sedan på **användare**.

    ![Användare](./media/intacct-tutorial/ic790041.png "användare")
3. Klicka på den **Lägg till** fliken.

    ![Lägg till](./media/intacct-tutorial/ic790042.png "Lägg till")
4. I den **användarinformation** avsnittet, utför följande steg:

    ![Användarinformation](./media/intacct-tutorial/ic790043.png "användarinformation")

    a. Ange den **användar-ID**, **efternamn**, **Förnamn**, **e-postadress**, **rubrik**, och **Phone** för ett Azure AD-konto som du vill etablera i den **användarinformation** avsnitt.

    b. Välj den **administratörsrättigheter** för ett Azure AD-konto som du vill etablera.
   
    c. Klicka på **Spara**. Kontoinnehavaren Azure AD tar emot ett e-postmeddelande och följer en länk för att bekräfta sina konton innan den aktiveras.

>[!NOTE]
>Du kan använda andra Intacct användare skapa verktyg och API: er som tillhandahålls av Intacct för att etablera Azure AD-användarkonton.
        
### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Intacct.

![Tilldela användare][200] 

**Om du vill tilldela Intacct Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Intacct**.

    ![Konfigurera enkel inloggning](./media/intacct-tutorial/tutorial_intacct_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testa du Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Intacct på åtkomstpanelen bör du vara automatiskt inloggad i tillämpningsprogrammet Intacct.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/intacct-tutorial/tutorial_general_01.png
[2]: ./media/intacct-tutorial/tutorial_general_02.png
[3]: ./media/intacct-tutorial/tutorial_general_03.png
[4]: ./media/intacct-tutorial/tutorial_general_04.png

[100]: ./media/intacct-tutorial/tutorial_general_100.png

[200]: ./media/intacct-tutorial/tutorial_general_200.png
[201]: ./media/intacct-tutorial/tutorial_general_201.png
[202]: ./media/intacct-tutorial/tutorial_general_202.png
[203]: ./media/intacct-tutorial/tutorial_general_203.png

