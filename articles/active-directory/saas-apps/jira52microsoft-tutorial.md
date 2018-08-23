---
title: 'Självstudier: Azure Active Directory-integration med JIRA SAML SSO av Microsoft (V5.2) | Microsoft Docs'
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
ms.openlocfilehash: d7f53efd4b473f36aa03628da4992d1c4c2fb04b
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42058226"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Självstudier: Azure Active Directory-integration med JIRA SAML SSO av Microsoft (V5.2)

I den här självstudien får du lära dig hur du integrerar JIRA SAML SSO av Microsoft (V5.2) med Azure Active Directory (AD Azure).

Integrera JIRA SAML SSO av Microsoft (V5.2) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till JIRA SAML SSO av Microsoft (V5.2).
- Du kan aktivera användarna att automatiskt få loggat in på JIRA SAML SSO av Microsoft (V5.2) (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="description"></a>Beskrivning

Använd ditt Microsoft Azure Active Directory-konto med Atlassians JIRA-server för att aktivera enkel inloggning. På så sätt alla organisationens användare kan använda autentiseringsuppgifter för Azure AD logga in i JIRA-programmet. Det här plugin-programmet använder SAML 2.0 för federation.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med JIRA SAML SSO av Microsoft (V5.2), behöver du följande objekt:

- En Azure AD-prenumeration
- JIRA Core och 5.2 programvara ska installeras och konfigureras på Windows 64-bitars version
- JIRA-servern är HTTPS aktiverat
- Observera versionerna som stöds för JIRA-pluginprogrammet nämns i under avsnitt.
- JIRA-servern kan nås på internet särskilt till Azure AD inloggningssidan för autentisering och ska kunna ta emot token från Azure AD
- Administratörsautentiseringsuppgifter ställs in i JIRA
- WebSudo har inaktiverats i JIRA
- Testa användaren som skapades i JIRA-serverprogram

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte använder en produktionsmiljön i JIRA. Testa integrationen först i utvecklings- eller mellanlagringsmiljön för programmet och sedan använda produktionsmiljön.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

**Versioner som stöds:**

*   JIRA Core- och programvara: 5.2
*   JIRA stöder också 6.0 och 7.8. Mer information klickar du på [JIRA SAML SSO av Microsoft](jiramicrosoft-tutorial.md)

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till JIRA SAML SSO av Microsoft (V5.2) från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Att lägga till JIRA SAML SSO av Microsoft (V5.2) från galleriet
Om du vill konfigurera integreringen av JIRA SAML SSO av Microsoft (V5.2) till Azure AD, som du behöver lägga till JIRA SAML SSO av Microsoft (V5.2) från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till JIRA SAML SSO av Microsoft (V5.2) från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **JIRA SAML SSO av Microsoft (V5.2)** väljer **JIRA SAML SSO av Microsoft (V5.2)** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![JIRA SAML SSO av Microsoft (V5.2) i resultatlistan](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med JIRA SAML SSO av Microsoft (V5.2) baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i JIRA SAML SSO av Microsoft (V5.2) till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i JIRA SAML SSO av Microsoft (V5.2) upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med JIRA SAML SSO av Microsoft (V5.2), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa ett JIRA SAML SSO av Microsoft (V5.2) testanvändare](#create-a-jira-saml-sso-by-microsoft-v52-test-user)**  – du har en motsvarighet för Britta Simon i JIRA SAML SSO genom den Microsoft (V5.2) som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din JIRA SAML SSO av Microsoft (V5.2) program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med JIRA SAML SSO av Microsoft (V5.2):**

1. I Azure-portalen på den **JIRA SAML SSO av Microsoft (V5.2)** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_samlbase.png)

3. På den **JIRA SAML SSO genom Microsoft-Domain och URL: er** avsnittet, utför följande steg:

    ![JIRA SAML SSO genom Microsoft-Domain och URL: er enkel inloggning för information](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<domain:port>/plugins/servlet/saml/auth`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<domain:port>/`

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och inloggnings-URL. Porten är valfritt, om det är en namngiven URL. Dessa värden tas emot under konfigurationen av Jira-plugin-programmet, som beskrivs senare i självstudien.

4. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/tutorial_metadataurl.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/tutorial_general_400.png)

6. I ett annat webbläsarfönster logga du in på din JIRA-instans som en administratör.

7. Hovra över kugghjulet och klicka på den **tillägg**.

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/addon1.png)

8. Tillägg fliken avsnittet, klickar du på **Hantera tillägg**.

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/addon7.png)

9. Hämta plugin-programmet från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56521). Överför manuellt från Microsoft med hjälp av plugin-programmet **överföra tillägg** menyn. Täcks av hämtningen av plugin-programmet [Microsofts serviceavtal](https://www.microsoft.com/servicesagreement/).

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/addon12.png)

10. När plugin-programmet har installerats, visas det i **användarinstallerade** tillägg avsnittet. Klicka på **konfigurera** att konfigurera nya plugin-programmet.

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/addon13.png)

11. Utför följande steg på konfigurationssidan:

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Kontrollera att det finns bara ett certifikat mappas mot appen så att det finns inget fel vid matchning av metadata. Om det finns flera certifikat vid lösa metadata som får administratören ett fel.

    a. I **Metadata_url** textrutan klistra in **Appfederationsmetadata** värde som du har kopierat från Azure-portalen och klicka på den **lösa** knappen. Den läser metadata-URL för IDP: N och fylls informationen för fält.

    b. Kopiera den **identifierare, svars-URL och URL: en inloggning** standardvärden och klistra in dem i **identifierare, svars-URL och URL: en inloggning** textrutor i respektive **JIRA SAML SSO av Microsoft (V5.2)-domän och URL: er**  avsnittet på Azure-portalen.

    c. I **knappen inloggningsnamn** skriver du namnet på knappen som organisationen vill att användarna kan se på inloggningsskärmen.

    d. I **SAML-ID användarplatser** väljer du antingen **användar-ID är i NameIdentifier-elementet i instruktionen ämne** eller **användar-ID är i ett attributelement**.  Detta ID måste vara JIRA användar-id. Om det användar-id inte matchas sedan kan inte användare att logga in.

    > [!Note]
    > Standardplatsen för SAML användar-ID är namnidentifierare. Du kan ändra det till ett attributalternativ för och ange lämpliga attributets namn.

    e. Om du väljer **användar-ID är i ett attributelement** alternativet i **attributnamnet** textrutan skriver du namnet på attributet som där användar-Id förväntas. 

    f. Om du använder federerad domän (till exempel AD FS osv) med Azure AD, klicka sedan på den **aktivera identifiering av startsfär** och konfigurera den **domännamn**.

    g. I **domännamn** anger du domännamnet här vid inloggningen ADFS-baserad.

    h. Kontrollera **aktivera enkelinloggning ut** om du vill logga ut från Azure AD när en användare loggar från JIRA. 

    i. Klicka på **spara** för att spara inställningarna.

    > [!NOTE]
    > Mer information om installation och felsökning finns [administratörshandboken för MS JIRA SSO Connector](../ms-confluence-jira-plugin-adminguide.md) och det finns också [vanliga frågor och svar](../ms-confluence-jira-plugin-faq.md) på förhand

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/jira52microsoft-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/jira52microsoft-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/jira52microsoft-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/jira52microsoft-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-jira-saml-sso-by-microsoft-v52-test-user"></a>Skapa ett JIRA SAML SSO av Microsoft (V5.2) testanvändare

Om du vill aktivera Azure AD-användare att logga in på JIRA den lokala servern, måste de etableras i JIRA den lokala servern.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in till JIRA den lokala servern som administratör.

2. Hovra över kugghjulet och klicka på den **Användarhantering**.

    ![Lägg till medarbetare](./media/jira52microsoft-tutorial/user1.png)

3. Du omdirigeras till administratörsåtkomst där du kan ange **lösenord** och klicka på **Bekräfta** knappen.

    ![Lägg till medarbetare](./media/jira52microsoft-tutorial/user2.png)

4. Under **Användarhantering** fliken avsnittet **skapa användare**.

    ![Lägg till medarbetare](./media/jira52microsoft-tutorial/user3.png) 

5. På den **”Skapa ny användare”** dialogrutan utför följande steg:

    ![Lägg till medarbetare](./media/jira52microsoft-tutorial/user4.png)

    a. I den **e-postadress** textrutan typ e-postadressen för användaren som Brittasimon@contoso.com.

    b. I den **fullständigt namn** textrutan fullständiga namn för användaren som Britta Simon.

    c. I den **användarnamn** textrutan typ e-postmeddelandet av användare som Brittasimon@contoso.com.

    d. I den **lösenord** textrutan skriver du lösenordet för användaren.

    e. Klicka på **skapa användare**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till JIRA SAML SSO av Microsoft (V5.2).

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon JIRA SAML SSO av Microsoft (V5.2), utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **JIRA SAML SSO av Microsoft (V5.2)**.

    ![JIRA SAML SSO av Microsoft (V5.2) länk i listan med program](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_app.png)

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på JIRA SAML SSO av Microsoft (V5.2) panel i åtkomstpanelen du bör få automatiskt loggat in på ditt JIRA SAML SSO av Microsoft (V5.2) program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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