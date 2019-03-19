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
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3de8422efdbb7802e0532503e2733518e8050f6d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842540"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Självstudier: Azure Active Directory-integrering med Riskware

I den här självstudien får du lära dig hur du integrerar Riskware med Azure Active Directory (AD Azure).

Integrera Riskware med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Riskware.
- Du kan aktivera användarna att automatiskt få loggat in på Riskware (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Riskware, behöver du följande objekt:

- En Azure AD-prenumeration
- En Riskware enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Riskware från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-riskware-from-the-gallery"></a>Att lägga till Riskware från galleriet
För att konfigurera integrering av Riskware i Azure AD, som du behöver lägga till Riskware från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Riskware från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Riskware**väljer **Riskware** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Riskware i resultatlistan](./media/riskware-tutorial/tutorial_riskware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Riskware baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Riskware är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Riskware upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Riskware, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Riskware](#create-a-riskware-test-user)**  – du har en motsvarighet för Britta Simon i Riskware som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Riskware program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Riskware:**

1. I Azure-portalen på den **Riskware** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/riskware-tutorial/tutorial_riskware_samlbase.png)

1. På den **Riskware domän och URL: er** avsnittet, utför följande steg:

    ![Riskware domän och URL: er med enkel inloggning för information](./media/riskware-tutorial/tutorial_riskware_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster:

    | Miljö| URL-mönster|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. I den **identifierare (entitets-ID)** textrutan anger du ett URL:
    
    | Miljö| URL-mönster|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Riskware klienten supportteamet](mailto:support@pansoftware.com.au) att hämta värdet.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/riskware-tutorial/tutorial_riskware_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/riskware-tutorial/tutorial_general_400.png)

1. På den **Riskware Configuration** klickar du på **konfigurera Riskware** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Riskware konfiguration](./media/riskware-tutorial/tutorial_riskware_configure.png)

1. I ett annat webbläsarfönster, loggar du in din Riskware företagets webbplats som administratör.

1. Klicka på upp till höger, **Underhåll** att öppna underhållssidan.

    ![Riskware konfigurationer Underhåll](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Underhåll på sidan klickar du på **autentisering**.

    ![Riskware Configuration authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. I **Autentiseringskonfiguration** utför följande steg:

    ![Riskware Configuration authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Välj **typ** som **SAML** för autentisering.

    b. I den **kod** textrutan skriver in din kod som AZURE_UAT.

    c. I den **beskrivning** textrutan skriver du en beskrivning som AZURE-konfiguration för enkel inloggning.

    d. I **enkel inloggning på sidan** textrutan klistra in den **SAML enkel inloggning för tjänst-URL** värde, som du har kopierat från Azure-portalen.

    e. I **logga ut sidan** textrutan klistra in den **URL: en för utloggning** värde, som du har kopierat från Azure-portalen.

    f. I den **Post formulärfält** textrutan skriver fältnamnet finns i inlägget svar som innehåller SAML som SAMLResponse

    g. I den **XML-taggnamnet för Identity** textrutan type-attributet som innehåller den unika identifieraren i SAML-svar som NameID.

    h. Öppna den hämtade **Xml-Metadata för** kopiera certifikatet från metadatafilen från Azure-portalen i anteckningar och klistra in den i den **certifikat** textrutan

    i. I **konsument URL** textrutan klistra in värdet för **svars-URL**, som du får från supporten.

    j. I **utfärdare** textrutan klistra in värdet för **identifierare**, som du får från supporten.

    > [!Note]
    > Kontakta [Riskware klienten supportteamet](mailto:support@pansoftware.com.au) att hämta dessa värden

    k. Välj **användning efter** kryssrutan.

    l. Välj **Använd SAML-begäran** kryssrutan.

    m. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/riskware-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/riskware-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/riskware-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/riskware-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-riskware-test-user"></a>Skapa en Riskware testanvändare

Om du vill aktivera Azure AD-användare att logga in på Riskware, måste de etableras i Riskware. I Riskware är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på Riskware som en administratör.

1. Klicka på upp till höger, **Underhåll** att öppna underhållssidan. 

    ![Riskware bibehåller](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Underhåll på sidan klickar du på **personer**.

    ![Riskware Configuration personer](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Välj **information** fliken och utför följande steg:

    ![Riskware konfigurationsinformation](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Välj **typ** som anställd.

    b. I **Förnamn** textrutan Ange först namnet på användaren som **Britta**.

    c. I **efternamn** textrutan Ange efternamn för användaren som **Simon**.

1. På den **Security** fliken, utför följande steg:

    ![Riskware Configuration säkerhet](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. Under **autentisering** väljer den **autentisering** läge, som du har konfigurera som AZURE-konfiguration för enkel inloggning.

    b. Under **inloggningsuppgifter** avsnittet i den **användar-ID** textrutan Ange e-postadress för användaren som **brittasimon\@contoso.com**.

    c. I den **lösenord** textrutan anger du lösenordet för användaren.

1. På den **organisation** fliken, utför följande steg:

    ![Riskware Configuration org](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Välj alternativet som **Level1** organisation.

    b. Under **persons primära arbetsplats** avsnittet i den **plats** textrutan skriver din plats.

    c. Under **medarbetare** väljer **anställningsstatus** som Vardaglig.

1. Klicka på **Spara**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Riskware.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon Riskware, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Riskware**.

    ![Länken Riskware i listan med program](./media/riskware-tutorial/tutorial_riskware_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Riskware i åtkomstpanelen du bör få automatiskt loggat in på ditt Riskware program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

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

