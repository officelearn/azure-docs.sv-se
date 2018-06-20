---
title: 'Självstudier: Azure Active Directory-integrering med Riskware | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Riskware.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 2062ba20b09e8e42fd094fbff628f5e76b5ea00f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225797"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Självstudier: Azure Active Directory-integrering med Riskware

I kursen får lära du att integrera Riskware med Azure Active Directory (AD Azure).

Integrera Riskware med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Riskware.
- Du kan aktivera användarna att automatiskt hämta loggat in på Riskware (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Riskware, behöver du följande:

- En Azure AD-prenumeration
- En Riskware enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Riskware från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-riskware-from-the-gallery"></a>Att lägga till Riskware från galleriet
Du måste lägga till Riskware från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Riskware i Azure AD.

**Utför följande steg för att lägga till Riskware från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Riskware**väljer **Riskware** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Riskware i resultatlistan](./media/riskware-tutorial/tutorial_riskware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Riskware baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Riskware motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Riskware upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Riskware, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Riskware](#create-a-riskware-test-user)**  – du har en motsvarighet för Britta Simon i Riskware som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Riskware program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Riskware:**

1. I Azure-portalen på den **Riskware** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/riskware-tutorial/tutorial_riskware_samlbase.png)

3. På den **Riskware domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och Riskware domän med enkel inloggning information](./media/riskware-tutorial/tutorial_riskware_url.png)

    a. I den **inloggning URL** textruta Skriv en URL med följande mönster:
    | Miljö| URL-mönster|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` | 
    |||

    b. I den **identifierare (enhets-ID)** textruta, ange ett URL-Adressen:
    | Miljö| URL-mönster|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` | 
    |||

    > [!NOTE] 
    > Logga in på URL-värdet är inte verkliga. Uppdatera värdet med det faktiska inloggnings-URL. Kontakta [Riskware klienten supportteamet](mailto:support@pansoftware.com.au) värdet hämtas.

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/riskware-tutorial/tutorial_riskware_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/riskware-tutorial/tutorial_general_400.png)

6. På den **Riskware Configuration** klickar du på **konfigurera Riskware** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out Webbadressen och SAML enkel inloggning Service** från den **Snabbreferens avsnitt.**

    ![Riskware konfiguration](./media/riskware-tutorial/tutorial_riskware_configure.png) 

7. I en annan webbläsarfönster loggar du in på webbplatsen Riskware företag som administratör.

8. Klicka på upp till höger, **Underhåll** att öppna underhållssidan. 

    ![Riskware konfigurationer Underhåll](./media/riskware-tutorial/tutorial_riskware_maintain.png)

9. Klicka på underhållssidan **autentisering**.

    ![Riskware Configuration authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

10. I **Autentiseringskonfiguration** utför följande steg:

    ![Riskware Configuration authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Välj **typen** som **SAML** för autentisering.

    b. I den **kod** textruta Typkod som AZURE_UAT.

    c. I den **beskrivning** textruta Anger beskrivningen som AZURE-konfiguration för enkel inloggning.

    d. I **enkel inloggning på sidan** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.

    e. I **logga ut sidan** textruta klistra in värdet för **Sign-Out URL**, som du har kopierat från Azure-portalen.

    f. I den **fält bokför** textruta skriv fältnamnet finns i Post-svar som innehåller SAML som SamlResponse

    g. I den **identitet för XML-taggnamnet** textruta type-attributet som innehåller den unika identifieraren i SAML-svar som NameID.

    h. Öppna den hämtade **Xml-Metadata för** kopiera certifikatet från metadatafilen från Azure-portalen i anteckningar och klistra in det i den **certifikat** textruta
    
    i. I **konsumenten URL** textruta klistra in värdet för **Reply URL**, som du får från supportgruppen.

    j. I **utfärdaren** textruta klistra in värdet för **identifierare**, som du får från supportgruppen.

    > [!Note]
    > Kontakta [Riskware klienten supportteamet](mailto:support@pansoftware.com.au) att hämta dessa värden

    k. Välj **användning efter** skickar SAML-begäran som en post-parameter.

    l. Välj **Använd SAML begära** skickar SSO tillåter SAML-begäran för SP initieras.

    m. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/riskware-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/riskware-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/riskware-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/riskware-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-riskware-test-user"></a>Skapa en testanvändare Riskware

Om du vill aktivera Azure AD-användare kan logga in på Riskware etableras de i Riskware. I Riskware är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på Riskware som en administratör.

2. Klicka på upp till höger, **Underhåll** att öppna underhållssidan. 

    ![Riskware konfigurationen behåller](./media/riskware-tutorial/tutorial_riskware_maintain.png)

3. Klicka på underhållssidan **personer**.

    ![Konfiguration av Riskware personer](./media/riskware-tutorial/tutorial_riskware_people.png)

4. På den **information** fliken, utför följande steg:
    
    ![Riskware konfigurationsinformation](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Välj **typ** som medarbetare.

    b. I **Förnamn** textruta Ange först namnet på användaren som **Britta**.

    c. I **efternamn** textruta Ange efternamn för användaren som **Simon**.

5. På den **säkerhet** fliken, utför följande steg:    

    ![Riskware Configuration säkerhet](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. Under **autentisering** väljer den **autentisering** läge, som du har inställningar som AZURE-konfiguration för enkel inloggning.

    b. Under **inloggningsuppgifter** avsnitt i den **användar-ID** textruta ange e-postadress för användaren som **brittasimon@contoso.com**.

    c. I den **lösenord** textruta ange lösenord för användaren.

6. På den **organisationen** fliken, utför följande steg:

    ![Riskware Configuration org](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Under **organisationen** väljer du organisationen som **Level1** organisationen.
    
    b. Under **persons primära arbetsplats** avsnitt i den **plats** textruta Skriv din plats.

    c. Under **medarbetare** väljer **anställningsstatus** som Vardaglig.

7. Klicka på **Spara**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Riskware.

![Tilldela rollen][200] 

**Om du vill tilldela Riskware Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Riskware**.

    ![Länken Riskware i listan med program](./media/riskware-tutorial/tutorial_riskware_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Riskware på åtkomstpanelen du bör få automatiskt loggat in på ditt Riskware program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/riskware-tutorial/tutorial_general_01.png
[2]: ./media/riskware-tutorial/tutorial_general_02.png
[3]: ./media/riskware-tutorial/tutorial_general_03.png
[4]: ./media/riskware-tutorial/tutorial_general_04.png

[100]: ./media/riskware-tutorial/tutorial_general_100.png

[200]: ./media/riskware-tutorial/tutorial_general_200.png
[201]: ./media/riskware-tutorial/tutorial_general_201.png
[202]: ./media/riskware-tutorial/tutorial_general_202.png
[203]: ./media/riskware-tutorial/tutorial_general_203.png

