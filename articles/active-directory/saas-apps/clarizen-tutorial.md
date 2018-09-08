---
title: 'Självstudier: Azure Active Directory-integration med Clarizen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Clarizen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: jeedes
ms.openlocfilehash: 855f147b0622ecc0831f2bc464e83d245af9e574
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158679"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Självstudier: Azure Active Directory-integration med Clarizen

I de här självstudierna lär du dig hur du integrerar Azure Active Directory (Azure AD) med Clarizen. Den här integreringen ger följande fördelar:

- Du kan styra, i Azure AD som har åtkomst till Clarizen.
- Du kan aktivera användarna att logga in automatiskt till Clarizen (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats, Azure-portalen.

Scenariot i den här kursen består av två huvudsakliga uppgifter:

1. Lägg till Clarizen från galleriet.
1. Konfigurera och testa Azure AD enkel inloggning.

Om du vill ha mer information om programvara som en tjänst (SaaS) app-integrering med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar
Om du vill konfigurera Azure AD-integrering med Clarizen, behöver du följande objekt:

- En Azure AD-prenumeration
- En Clarizen-prenumeration som är aktiverad för enkel inloggning

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Testa Azure AD enkel inloggning i en testmiljö. Använd inte din produktionsmiljö såvida inte detta är nödvändigt.
- Om du inte har en testmiljö för Azure AD, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-clarizen-from-the-gallery"></a>Lägg till Clarizen från galleriet
Lägg till Clarizen från galleriet i din lista över hanterade SaaS-appar för att konfigurera integrering av Clarizen i Azure AD.

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan klickar du på den **Azure Active Directory** ikon.

    ![Azure Active Directory-ikon][1]

1. Klicka på **företagsprogram**. Klicka sedan på **alla program**.

    ![Klicka på ”program” och ”alla program”][2]

1. Klicka på den **Lägg till** längst upp i dialogrutan.

    ![Knappen ”Lägg till”][3]

1. I sökrutan skriver **Clarizen**.

    ![Att skriva ”Clarizen” i sökrutan](./media/clarizen-tutorial/tutorial_clarizen_000.png)

1. I resultatfönstret väljer **Clarizen**, och klicka sedan på **Lägg till** att lägga till programmet.

    ![Att välja Clarizen i resultatfönstret](./media/clarizen-tutorial/tutorial_clarizen_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I följande avsnitt, konfigurera och testa Azure AD enkel inloggning med Clarizen baserat på testanvändare Britta Simon.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Clarizen är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Clarizen upprättas. Du skapar den här länken relation genom att tilldela värdet för **användarnamn** i Azure AD som värde för **användarnamn** i Clarizen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Clarizen, utför du följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  vill tillåta att användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Clarizen](#create-a-clarizen-test-user)**  har en motsvarighet för Britta Simon i Clarizen som är länkad till en Azure AD-representation av henne.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning
Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Clarizen program.

1. I Azure-portalen på den **Clarizen** program integration-sidan klickar du på **enkel inloggning**.

    ![Klicka på ”enkel inloggning”][4]

1. I den **enkel inloggning** dialogrutan för **läge**väljer **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Att välja ”SAML-baserad inloggning”](./media/clarizen-tutorial/tutorial_clarizen_01.png)

1. I den **Clarizen domän och URL: er** avsnittet, utför följande steg:

    ![Rutorna för identifierare och svars-URL](./media/clarizen-tutorial/tutorial_clarizen_02.png)

    a. I den **identifierare** skriver värdet som: **Clarizen**

    b. I den **svars-URL** skriver en URL med hjälp av följande mönster: **https://<company name>.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx**

    > [!NOTE]
    > Detta är inte de verkliga värden. Du måste använda det faktiska ID: t och svars-URL. Här rekommenderar vi att du använder det unika värdet av en sträng som identifierare. För att få de faktiska värdena kan kontakta den [Clarizen supportteamet](https://success.clarizen.com/hc/en-us/requests/new).

1. På den **SAML-signeringscertifikat** klickar du på **Skapa nytt certifikat**.

    ![Klicka på ”Skapa nytt certifikat”](./media/clarizen-tutorial/tutorial_clarizen_03.png)    

1. I den **Skapa nytt certifikat** dialogrutan rutan klickar du på kalenderikonen och väljer ett förfallodatum. Klicka sedan på **Spara**.

    ![Att välja och spara ett förfallodatum](./media/clarizen-tutorial/tutorial_general_300.png)

1. I den **SAML-signeringscertifikat** väljer **gör nytt certifikat aktivt**, och klicka sedan på **spara**.

    ![Markera kryssrutan för att göra det nya certifikatet active](./media/clarizen-tutorial/tutorial_clarizen_04.png)

1. I den **förnyelsecertifikatet** dialogrutan klickar du på **OK**.

    ![Klicka på ”OK” för att bekräfta att du vill aktivera certifikatet](./media/clarizen-tutorial/tutorial_general_400.png)

1. I den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Klicka på ”certifikat (Base64)” att starta nedladdningen](./media/clarizen-tutorial/tutorial_clarizen_05.png)

1. I den **Clarizen Configuration** klickar du på **konfigurera Clarizen** att öppna den **konfigurera inloggning** fönster.

    ![Klicka på ”Konfigurera Clarizen”](./media/clarizen-tutorial/tutorial_clarizen_06.png)

    ![”Konfigurera inloggning” fönster, inklusive filer och URL: er](./media/clarizen-tutorial/tutorial_clarizen_07.png)

1. I ett annat webbläsarfönster, loggar du in din Clarizen företagets webbplats som administratör.

1. Klicka på ditt användarnamn och klicka sedan på **inställningar**.

    ![Klicka på ”inställningar” under ditt användarnamn](./media/clarizen-tutorial/tutorial_clarizen_001.png "inställningar")

1. Klicka på den **globala inställningar** fliken. Sedan bredvid **federerad**, klickar du på **redigera**.

    ![Fliken ”globala inställningar”](./media/clarizen-tutorial/tutorial_clarizen_002.png "globala inställningar")

1. I den **federerad** dialogrutan utför följande steg:

    ![”Federerad autentisering” dialogrutan](./media/clarizen-tutorial/tutorial_clarizen_003.png "federerad autentisering")

    a. Välj **aktivera federerad autentisering**.

    b. Klicka på **överför** att ladda upp din hämtade certifikatet.

    c. I den **inloggning URL** anger värdet för **SAML enkel inloggning för tjänst-URL** från konfigurationsfönstret för Azure AD-program.

    d. I den **URL: en för utloggning** anger värdet för **URL: en för utloggning** från konfigurationsfönstret för Azure AD-program.

    e. Välj **använder POST**.

    f. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
Skapa en testanvändare som kallas Britta Simon i Azure-portalen.

![Namn och e-postadress för den Azure AD-testanvändaren][100]

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** ikon.

    ![Azure Active Directory-ikon](./media/clarizen-tutorial/create_aaduser_01.png)

1. Klicka på **användare och grupper**, och klicka sedan på **alla användare** att visa en lista över användare.

    ![Klicka på ”användare och grupper” och ”alla användare”](./media/clarizen-tutorial/create_aaduser_02.png)

1. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.

    ![Knappen ”Lägg till”](./media/clarizen-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![”Användare”-dialogruta med namn, e-postadress och lösenord som fyllts](./media/clarizen-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver du e-postadressen för Britta Simon-kontot.

    c. Välj **visa lösenord** och anteckna värdet för **lösenord**.

    d. Klicka på **Skapa**.

### <a name="create-a-clarizen-test-user"></a>Skapa en Clarizen testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Clarizen.

**Om du vill skapa användare manuellt gör du följande:**

Om du vill aktivera Azure AD-användare att logga in på Clarizen, måste du etablera användarkonton. När det gäller Clarizen är etablering en manuell aktivitet.

1. Logga in på webbplatsen Clarizen företag som administratör.

2. Klicka på **personer**.

    ![Klicka på ”personer”](./media/clarizen-tutorial/create_aaduser_001.png "personer")

3. Klicka på **Bjud in användare**.

    ![Knappen ”Bjud in användare”](./media/clarizen-tutorial/create_aaduser_002.png "bjuda in användare")

1. I den **bjuda in människor** dialogrutan utför följande steg:

    ![”” Dialogrutan](./media/clarizen-tutorial/create_aaduser_003.png "bjuda in människor")

    a. I den **e-post** skriver du e-postadressen för Britta Simon-kontot.

    b. Klicka på **bjuda in**.

    > [!NOTE]
    > Azure Active Directory-kontoinnehavare kommer ett e-postmeddelande och följ en länk för att bekräfta sina konton innan den blir aktiv.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare
Aktivera Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst Clarizen.

![Tilldelade användare][200]

1. I Azure portal, öppna program visa, bläddra till vyn directory klickar du på **företagsprogram**, och klicka sedan på **alla program**.

    ![Klicka på ”program” och ”alla program”][201]

1. I listan med program väljer **Clarizen**.

    ![Välj Clarizen på listan](./media/clarizen-tutorial/tutorial_clarizen_50.png)

1. I den vänstra rutan klickar du på **användare och grupper**.

    ![Klicka på ”användare och grupper”][202]

1. Klicka på knappen **Lägg till**. I den **Lägg till tilldelning** dialogrutan **användare och grupper**.

    ![Knappen ”Lägg till” och dialogrutan ”Lägg till tilldelning”][203]

1. I den **användare och grupper** dialogrutan **Britta Simon** i listan över användare.

1. I den **användare och grupper** dialogrutan klickar du på den **Välj** knappen.

1. I den **Lägg till tilldelning** dialogrutan klickar du på den **tilldela** knappen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning
Testa Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Clarizen i åtkomstpanelen, bör det vara loggas in automatiskt till Clarizen programmet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/clarizen-tutorial/tutorial_general_01.png
[2]: ./media/clarizen-tutorial/tutorial_general_02.png
[3]: ./media/clarizen-tutorial/tutorial_general_03.png
[4]: ./media/clarizen-tutorial/tutorial_general_04.png

[100]: ./media/clarizen-tutorial/tutorial_general_100.png

[200]: ./media/clarizen-tutorial/tutorial_general_200.png
[201]: ./media/clarizen-tutorial/tutorial_general_201.png
[202]: ./media/clarizen-tutorial/tutorial_general_202.png
[203]: ./media/clarizen-tutorial/tutorial_general_203.png