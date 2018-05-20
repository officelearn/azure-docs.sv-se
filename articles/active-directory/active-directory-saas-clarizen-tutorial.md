---
title: 'Självstudier: Azure Active Directory-integrering med Clarizen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Clarizen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: 8af9e7023142077fdeed0262e5f39921b158a14f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Självstudier: Azure Active Directory-integrering med Clarizen

I kursen får lära du att integrera Azure Active Directory (AD Azure) med Clarizen. Den här integreringen ger följande fördelar:

- Du kan styra, i Azure AD, som har åtkomst till Clarizen.
- Du kan aktivera dina användare loggas in automatiskt till Clarizen (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats, Azure-portalen.

Ett scenario för den här kursen består av två huvudsakliga aktiviteter:

1. Lägg till Clarizen från galleriet.
2. Konfigurera och testa Azure AD enkel inloggning.

Om du vill ha mer information om programvara som en tjänst (SaaS) appintegrering med Azure AD, se [vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar
För att konfigurera Azure AD-integrering med Clarizen, behöver du följande:

- En Azure AD-prenumeration
- En Clarizen-prenumeration som är aktiverad för enkel inloggning

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Testa Azure AD enkel inloggning i en testmiljö. Använd inte i produktionsmiljön, om det inte är nödvändigt.
- Om du inte har en Azure AD-testmiljö, kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-clarizen-from-the-gallery"></a>Lägg till Clarizen från galleriet
För att konfigurera integrering av Clarizen i Azure AD, lägga till Clarizen från galleriet i listan över hanterade SaaS-appar.

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan klickar du på den **Azure Active Directory** ikon.

    ![Azure Active Directory-ikonen][1]

2. Klicka på **företagsprogram**. Klicka på **alla program**.

    ![Klicka på ”företagsprogram” och ”alla program”][2]

3. Klicka på den **Lägg till** längst upp i dialogrutan.

    ![Knappen ”Lägg till”][3]

4. I sökrutan skriver **Clarizen**.

    ![Att skriva ”Clarizen” i sökrutan](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_000.png)

5. I resultatfönstret, Välj **Clarizen**, och klicka sedan på **Lägg till** lägga till programmet.

    ![Att välja Clarizen i resultatfönstret](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I följande avsnitt för att konfigurera och testa Azure AD enkel inloggning med Clarizen baserat på testanvändare Britta Simon.

Azure AD måste du känna till användaren i Clarizen motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Clarizen upprättas. Du skapar den här länken relation genom att tilldela värdet för **användarnamn** i Azure AD som värde för **användarnamn** i Clarizen.

Slutför följande byggblock för att konfigurera och testa Azure AD enkel inloggning med Clarizen:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  att användarna ska kunna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Clarizen](#create-a-clarizen-test-user)**  har en motsvarighet för Britta Simon Clarizen som är kopplad till Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning
Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Clarizen program.

1. I Azure-portalen på den **Clarizen** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Klicka på ”enkel inloggning”][4]

2. I den **enkel inloggning** i dialogrutan för **läge**väljer **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Att välja ”SAML-baserade inloggning”](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_01.png)

3. I den **Clarizen domän och URL: er** avsnittet, utför följande steg:

    ![Kryssrutor för identifierare och svars-URL](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_02.png)

    a. I den **identifierare** Skriv värdet som: **Clarizen**

    b. I den **Reply URL** skriver en URL med hjälp av följande mönster: **https://<company name>.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx**

    > [!NOTE]
    > Detta är inte de verkliga värden. Du måste använda faktiska identifierare och reply URL. Här föreslår vi att du använder ett unikt värde i en sträng som identifierare. För att få de faktiska värdena kan kontakta den [Clarizen supportteamet](https://success.clarizen.com/hc/en-us/requests/new).

4. På den **SAML-signeringscertifikat** klickar du på **Skapa nytt certifikat**.

    ![Klicka på ”Skapa nytt certifikat”](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_03.png)  

5. I den **skapa nya certifikat** dialogrutan rutan, klicka på kalenderikonen och väljer ett förfallodatum. Klicka sedan på **Spara**.

    ![Att välja och spara upphör att gälla](./media/active-directory-saas-clarizen-tutorial/tutorial_general_300.png)

6. I den **SAML-signeringscertifikat** väljer **aktivera nya certifikatet**, och klicka sedan på **spara**.

    ![Om du markerar kryssrutan för att göra det nya certifikatet active](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_04.png)

7. I den **förnyelsecertifikat** dialogrutan klickar du på **OK**.

    ![Klicka på ”OK” för att bekräfta att du vill aktivera certifikatet](./media/active-directory-saas-clarizen-tutorial/tutorial_general_400.png)

8. I den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Klicka på ”intyg (Base64)” för att starta nedladdningen](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_05.png)

9. I den **Clarizen Configuration** klickar du på **konfigurera Clarizen** att öppna den **konfigurera inloggning** fönster.

    ![Klicka på ”Konfigurera Clarizen”](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_06.png)

    ![”Konfigurera inloggning” fönster, inklusive filer och URL: er](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_07.png)

10. I en annan webbläsarfönster loggar du in på ditt Clarizen företagets webbplats som administratör.

11. Klicka på ditt användarnamn och klicka sedan på **inställningar**.

    ![Klicka på ”inställningar” under ditt användarnamn](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_001.png "inställningar")

12. Klicka på den **globala inställningar** fliken. Sedan bredvid **federerad autentisering**, klickar du på **redigera**.

    ![”Globala inställningar”](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_002.png "globala inställningar")

13. I den **federerad autentisering** dialogrutan utför följande steg:

    ![”Extern autentisering” dialogrutan](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_003.png "federerad autentisering")

    a. Välj **aktivera federerad autentisering**.

    b. Klicka på **överför** att överföra din hämtat certifikat.

    c. I den **inloggning URL** anger du värdet för **SAML inloggning tjänst-URL för enkel** från Azure AD-konfigurationsfönstret.

    d. I den **Sign-out URL** anger du värdet för **Sign-Out URL** från Azure AD-konfigurationsfönstret.

    e. Välj **använder POST**.

    f. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Skapa en testanvändare kallas Britta Simon i Azure-portalen.

![Namn och e-postadress för användare i Azure AD][100]

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** ikon.

    ![Azure Active Directory-ikonen](./media/active-directory-saas-clarizen-tutorial/create_aaduser_01.png)

2. Klicka på **användare och grupper**, och klicka sedan på **alla användare** att visa en lista över användare.

    ![Klicka på ”användare och grupper” och ”alla användare”](./media/active-directory-saas-clarizen-tutorial/create_aaduser_02.png)

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.

    ![Knappen ”Lägg till”](./media/active-directory-saas-clarizen-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan ”användare” med namn, e-postadress och lösenord som har fyllt i](./media/active-directory-saas-clarizen-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv e-postadressen för kontot som Britta Simon.

    c. Välj **visa lösenordet** och anteckna värdet för **lösenord**.

    d. Klicka på **Skapa**.

### <a name="create-a-clarizen-test-user"></a>Skapa en testanvändare Clarizen
Om du vill aktivera Azure AD-användare att logga in på Clarizen, måste du etablera användarkonton. När det gäller Clarizen är etablering en manuell aktivitet.

1. Logga in på webbplatsen Clarizen företag som administratör.

2. Klicka på **personer**.

    ![Klicka på ”personer”](./media/active-directory-saas-clarizen-tutorial/create_aaduser_001.png "personer")

3. Klicka på **bjuda in användare**.

    ![”Bjuda in användare” knappen](./media/active-directory-saas-clarizen-tutorial/create_aaduser_002.png "bjuda in användare")

4. I den **bjuda in personer** dialogrutan utför följande steg:

    ![”” Dialogrutan](./media/active-directory-saas-clarizen-tutorial/create_aaduser_003.png "bjuda in användare")

    a. I den **e-post** Skriv e-postadressen för kontot som Britta Simon.

    b. Klicka på **bjuda in**.

    > [!NOTE]
    > Innehavaren för Azure Active Directory-konto ett e-postmeddelande och länken för att bekräfta sina konton innan den aktiveras.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare
Aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja sin åtkomst till Clarizen.

![Tilldelad användare][200]

1. I Azure portal, öppna program visas bläddrar du till vyn directory klickar du på **företagsprogram**, och klicka sedan på **alla program**.

    ![Klicka på ”företagsprogram” och ”alla program”][201]

2. Välj i listan med program **Clarizen**.

    ![Att välja Clarizen i listan](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_50.png)

3. I den vänstra rutan klickar du på **användare och grupper**.

    ![Klicka på ”användare och grupper”][202]

4. Klicka på knappen **Lägg till**. I den **Lägg uppdrag** dialogrutan **användare och grupper**.

    ![Knappen ”Lägg till” och dialogrutan ”Lägg till tilldelning”][203]

5. I den **användare och grupper** dialogrutan **Britta Simon** i listan över användare.

6. I den **användare och grupper** dialogrutan klickar du på den **Välj** knappen.

7. I den **Lägg uppdrag** dialogrutan klickar du på den **tilldela** knappen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning
Testa Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Clarizen på åtkomstpanelen bör du vara automatiskt inloggad i tillämpningsprogrammet Clarizen.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_203.png
