---
title: 'Självstudier: Azure Active Directory-integrering med JIRA SAML SSO av Microsoft (V5.2) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och JIRA SAML SSO av Microsoft (V5.2).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 5df8b3d91f52e4809ef43e3e376004e4274b4400
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112028"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Självstudier: Azure Active Directory-integrering med JIRA SAML SSO av Microsoft (V5.2)

I kursen får lära du att integrera JIRA SAML SSO av Microsoft (V5.2) med Azure Active Directory (AD Azure).

Integrera JIRA SAML SSO av Microsoft (V5.2) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till JIRA SAML SSO av Microsoft (V5.2).
- Du kan aktivera användarna att automatiskt hämta loggat in på JIRA SAML SSO av Microsoft (V5.2) (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="description"></a>Beskrivning

Använda Microsoft Azure Active Directory-konto med Atlassian JIRA server för att aktivera enkel inloggning. Det här sättet alla organisationens användare kan använda Azure AD-autentiseringsuppgifterna för att logga in på programmet JIRA. Det här plugin-programmet använder SAML 2.0 för federation.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med JIRA SAML SSO av Microsoft (V5.2) behöver du följande:

- En Azure AD-prenumeration
- JIRA kärnor och 5.2 programvara ska installeras och konfigureras på Windows 64-bitars version
- JIRA server är HTTPS aktiverat
- Observera versionerna som stöds för JIRA Plugin nämns i under avsnittet.
- JIRA servern kan nås på internet särskilt till Azure AD inloggningssidan för autentisering och ska kunna ta emot token från Azure AD
- Admin-autentiseringsuppgifter anges i JIRA
- WebSudo har inaktiverats i JIRA
- Testanvändare som skapats i serverprogrammet JIRA

> [!NOTE]
> Om du vill testa stegen i den här självstudiekursen, rekommenderas inte med hjälp av en produktionsmiljö för JIRA. Testa integreringen först i utvecklings- eller mellanlagringsmiljön för programmet och sedan använda produktionsmiljön.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

**Versioner som stöds:**

*   JIRA Core- och programvara: 5.2
*   JIRA stöder också 6.0 och 7,8. Mer information klickar du på [JIRA SAML SSO av Microsoft](jiramicrosoft-tutorial.md)

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till JIRA SAML SSO av Microsoft (V5.2) från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Att lägga till JIRA SAML SSO av Microsoft (V5.2) från galleriet
Du måste lägga till JIRA SAML SSO av Microsoft (V5.2) från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av JIRA SAML SSO av Microsoft (V5.2) i Azure AD.

**Utför följande steg för att lägga till JIRA SAML SSO av Microsoft (V5.2) från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]

3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **JIRA SAML SSO av Microsoft (V5.2)** väljer **JIRA SAML SSO av Microsoft (V5.2)** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![JIRA SAML SSO av Microsoft (V5.2) i resultatlistan](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med JIRA SAML SSO av Microsoft (V5.2) baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren JIRA SAML SSO av Microsoft (V5.2) till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren JIRA SAML SSO av Microsoft (V5.2) upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med JIRA SAML SSO av Microsoft (V5.2), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en JIRA SAML SSO av Microsoft (V5.2) testanvändare](#create-a-jira-saml-sso-by-microsoft-v52-test-user)**  – du har en motsvarighet för Britta Simon JIRA SAML SSO av den Microsoft (V5.2) som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din JIRA SAML SSO av Microsoft (V5.2) program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med JIRA SAML SSO av Microsoft (V5.2):**

1. I Azure-portalen på den **JIRA SAML SSO av Microsoft (V5.2)** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning dialogrutan](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_samlbase.png)

3. På den **JIRA SAML SSO av URL: er och Microsoft Domain** avsnittet, utför följande steg:

    ![JIRA SAML SSO av URL: er och Microsoft Domain enkel inloggning information](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<domain:port>/plugins/servlet/saml/auth`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<domain:port>/`

    c. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Porten är valfria om det är en namngiven URL. Dessa värden tas emot under konfigurationen av Jira plugin som beskrivs senare i självstudierna.

4. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **webbadress Federation Metadata** och klistra in den i anteckningar.

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/tutorial_metadataurl.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/tutorial_general_400.png)

6. I en annan webbläsarfönster loggar du in till JIRA-instans som administratör.

7. Hovra över kugge och klicka på den **tillägg**.

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/addon1.png)

8. Klicka under tillägg fliken avsnitt **Hantera tillägg**.

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/addon7.png)

9. Hämta plugin-programmet från [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=56521). Ladda upp plugin-programmet som tillhandahålls av Microsoft genom att använda manuellt **ladda upp tilläggsprogrammet** menyn. Hämtningen av plugin-programmet omfattas [Microsofts serviceavtal](https://www.microsoft.com/en-us/servicesagreement/).

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/addon12.png)

10. När plugin-programmet installeras, visas det i **användaren installerat** tillägg avsnitt. Klicka på **konfigurera** att konfigurera nya plugin-programmet.

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/addon13.png)

11. Utför följande steg på konfigurationssidan:

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Kontrollera att det finns bara ett certifikat mappas mot appen så att det finns inga fel vid matchning av metadata. Om det finns flera certifikat vid lösning av metadata, får administratör ett fel.

    a. I **URL för tjänstmetadata** textruta klistra in **webbadress Federation Metadata** värde som du har kopierat från Azure-portalen och klicka på den **lösa** knappen. Den läser IdP metadata-URL och fyller i informationen för fält.

    b. Kopiera den **identifierare, svars-URL: en och URL: en inloggning** värden och klistra in dem i **identifierare, svars-URL: en och URL: en inloggning** respektive i textrutorna **JIRA SAML SSO av Microsoft (V5.2)-domän och URL: er**  avsnitt på Azure-portalen.

    c. I **knappen inloggningsnamnet** skriver du namnet på knappen organisationen vill att användarna ska se på inloggningsskärmen.

    d. I **SAML användar-ID platser** väljer du antingen **användar-ID är i elementet NameIdentifier i instruktionen ämne** eller **användar-ID är i ett element med attributet**.  Detta ID måste vara JIRA användar-id. Om det användar-id inte matchas sedan kan inte användare att logga in.

    > [!Note]
    > Standardplatsen för SAML användar-ID är namnidentifierare. Du kan ändra det till ett attributalternativ och ange lämpliga attributets namn.

    e. Om du väljer **användar-ID är i ett element med attributet** alternativet i **attributnamn** textrutan anger du namnet på attributet som där användar-Id förväntas. 

    f. Om du använder den federerade domänen (t.ex. AD FS etc.) med Azure AD, klicka på den **aktivera identifiering av startsfär** och konfigurera den **domännamn**.

    g. I **domännamn** anger du domännamnet här vid inloggningen ADFS-baserade.

    h. Kontrollera **aktivera enkel inloggning ut** om du vill logga ut från Azure AD när en användare loggar från JIRA. 

    i. Klicka på **spara** för att spara inställningarna.

    > [!NOTE]
    > Mer information om installation och felsökning finns [MS JIRA SSO Connector Admin guiden](../ms-confluence-jira-plugin-adminguide.md) och det också [vanliga frågor och svar](../ms-confluence-jira-plugin-faq.md) på förhand

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/jira52microsoft-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/jira52microsoft-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/jira52microsoft-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/jira52microsoft-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.

### <a name="create-a-jira-saml-sso-by-microsoft-v52-test-user"></a>Skapa en JIRA SAML SSO av Microsoft (V5.2) testanvändare

Om du vill aktivera Azure AD-användare kan logga in på JIRA lokal server, måste de etableras i JIRA lokal server.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt JIRA lokal server som administratör.

2. Hovra över kugge och klicka på den **Användarhantering**.

    ![Lägga till medarbetare](./media/jira52microsoft-tutorial/user1.png)

3. Du omdirigeras till administratörsåtkomst sidan om du vill ange **lösenord** och på **Bekräfta** knappen.

    ![Lägga till medarbetare](./media/jira52microsoft-tutorial/user2.png)

4. Under **Användarhantering** avsnittet klickar du på **skapa användare**.

    ![Lägga till medarbetare](./media/jira52microsoft-tutorial/user3.png) 

5. På den **”skapa nya användare”** dialogrutan utför följande steg:

    ![Lägga till medarbetare](./media/jira52microsoft-tutorial/user4.png)

    a. I den **e-postadress** textruta typen e-postadressen för användaren som Brittasimon@contoso.com.

    b. I den **fullständiga namn** textruta fullständiga typnamnet för användaren som Britta Simon.

    c. I den **användarnamn** textruta, ange den e-posten för användare som Brittasimon@contoso.com.

    d. I den **lösenord** textruta skriver du lösenordet för användaren.

    e. Klicka på **skapa användare**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till JIRA SAML SSO av Microsoft (V5.2).

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon JIRA SAML SSO av Microsoft (V5.2), utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201]

2. Välj i listan med program **JIRA SAML SSO av Microsoft (V5.2)**.

    ![JIRA SAML SSO av Microsoft (V5.2) länken i listan med program](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_app.png)

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på JIRA SAML SSO av Microsoft (V5.2) panelen på åtkomstpanelen du bör få automatiskt loggat in på ditt JIRA SAML SSO av Microsoft (V5.2) program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/msaadssojira5.2-tutorial/tutorial_general_01.png
[2]: ./media/msaadssojira5.2-tutorial/tutorial_general_02.png
[3]: ./media/msaadssojira5.2-tutorial/tutorial_general_03.png
[4]: ./media/msaadssojira5.2-tutorial/tutorial_general_04.png

[100]: ./media/msaadssojira5.2-tutorial/tutorial_general_100.png

[200]: ./media/msaadssojira5.2-tutorial/tutorial_general_200.png
[201]: ./media/msaadssojira5.2-tutorial/tutorial_general_201.png
[202]: ./media/msaadssojira5.2-tutorial/tutorial_general_202.png
[203]: ./media/msaadssojira5.2-tutorial/tutorial_general_203.png