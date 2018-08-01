---
title: 'Självstudier: Azure Active Directory-integrering med Jamf Pro | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2018
ms.author: jeedes
ms.openlocfilehash: b003f29db699d89f0d3cec76ee3562ffad08b40f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346342"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Självstudier: Azure Active Directory-integrering med Jamf Pro

I den här självstudien får lära du att integrera Jamf Pro med Azure Active Directory (AD Azure).

Integrera Jamf Pro med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Jamf Pro.
- Du kan aktivera användarna att automatiskt få loggat in på Jamf Pro (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Jamf Pro, behöver du följande objekt:

- En Azure AD-prenumeration
- En Jamf Pro enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Jamf Pro från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-jamf-pro-from-the-gallery"></a>Att lägga till Jamf Pro från galleriet
Om du vill konfigurera integreringen av Jamf Pro till Azure AD, som du behöver lägga till Jamf Pro från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Jamf Pro från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Jamf Pro**väljer **Jamf Pro** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Jamf Pro i resultatlistan](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Jamf Pro utifrån en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Jamf Pro är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Jamf Pro upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Jamf Pro, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare i Jamf Pro](#create-a-jamf-pro-test-user)**  – du har en motsvarighet för Britta Simon i Jamf Pro som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program i Jamf Pro.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Jamf Pro:**

1. I Azure-portalen på den **Jamf Pro** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_samlbase.png)

3. På den **Jamf Pro domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Jamf Pro domän och URL: er med enkel inloggning för information](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url.png)

    a. I den **identifierare (entitets-ID)** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.jamfcloud.com/saml/SSO`

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Jamf Pro domän och URL: er med enkel inloggning för information](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url1.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.jamfcloud.com`
     
    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och inloggnings-URL. Du får det faktiska ID-värdet från **enkel inloggning** avsnitt i Jamf Pro-portalen, som beskrivs senare i självstudien. Du kan extrahera den faktiska **underdomän** från ID-värde och använda det **underdomän** information i inloggnings-URL och svars-URL.

5. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.

    ![Länk för hämtning av certifikat](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/jamfprosamlconnector-tutorial/tutorial_general_400.png)
    
7. Logga in på webbplatsen för företagets Jamf Pro som en administratör i ett annat webbläsarfönster.

8. Klicka på den **inställningsikonen** från det övre högra hörnet på sidan.

    ![Jamf Pro-konfiguration](./media/jamfprosamlconnector-tutorial/configure1.png)

9. Klicka på **enkel inloggning**.

    ![Jamf Pro-konfiguration](./media/jamfprosamlconnector-tutorial/configure2.png)

10. På den **enkel inloggning** sidan utför följande steg:

    ![Jamf Pro enda](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Välj **Jamf Pro Server** att aktivera enkel inloggning för åtkomst.

    b. Genom att välja **Tillåt förbikoppling för alla användare** användare kommer inte att omdirigeras till inloggningssidan identitetsleverantör för autentisering, men kan logga in till Jamf Pro direkt i stället. När en användare försöker få åtkomst till Jamf Pro via identitetsprovidern, görs IdP-initierad SSO-autentisering och auktorisering.

    c. Välj den **NameID** för **ANVÄNDARMAPPNING: SAML**. Som standard är inställningen **NameID** men du kan definiera ett anpassat attribut.

    d. Välj **e-post** för **ANVÄNDARMAPPNING: JAMF PRO**. Jamf Pro mappar SAML-attribut som skickas av IDP: N på följande sätt: genom användare och grupper. När en användare försöker ansluta till Jamf Pro, som standard Jamf Pro hämtar information om användaren från identitetsprovidern och jämför den med Jamf Pro användarkonton. Om inkommande användarkontot inte finns i Jamf Pro, sedan inträffar grupp namnmatchning.

    e. Klistra in värdet `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` i den **GRUPPNAMN för ATTRIBUTET** textrutan.
 
11. På samma sida-rulla ned till och **IDENTITETSPROVIDERN** under den **enkel inloggning** avsnittet och utför följande steg:

    ![Jamf Pro-konfiguration](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Välj **andra** som ett alternativ från den **IDENTITETSPROVIDER** listrutan.

    b. I den **andra PROVIDER** textrutan Ange **Azure AD**.

    c. Välj **Metadata-URL** som ett alternativ från den **IDENTITETSKÄLLA PROVIDERN METADATA** listrutan och klistra in i textrutan följande den **Appfederationsmetadata** värde som du har kopierat från Azure-portalen.

    d. Kopiera den **entitets-ID** värde och klistra in den i den **identifierare (entitets-ID)** -textrutan i **Jamf Pro domän och URL: er** avsnittet på Azure-portalen.

    >[!NOTE]
    > Här är suddiga värdet underdomänsdelen. Använd det här värdet för att slutföra inloggnings-URL och svars-URL i den **Jamf Pro domän och URL: er** avsnittet på Azure-portalen.

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/jamfprosamlconnector-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/jamfprosamlconnector-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/jamfprosamlconnector-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/jamfprosamlconnector-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-jamf-pro-test-user"></a>Skapa en testanvändare i Jamf Pro

Om du vill aktivera Azure AD-användare att logga in till Jamf Pro, måste de etableras i Jamf Pro. När det gäller Jamf Pro är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen för företagets Jamf Pro som en administratör.

2. Klicka på den **inställningsikonen** från det övre högra hörnet på sidan.

    ![Lägg till medarbetare](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Klicka på **Jamf Pro-användarkonton och grupper**.

    ![Lägg till medarbetare](./media/jamfprosamlconnector-tutorial/user1.png)

4. Klicka på **Ny**.

    ![Lägg till medarbetare](./media/jamfprosamlconnector-tutorial/user2.png)

5. Välj **skapa standardkonto**.

    ![Lägg till medarbetare](./media/jamfprosamlconnector-tutorial/user3.png)

6. På den **nytt konto** dailog, utför följande steg:

    ![Lägg till medarbetare](./media/jamfprosamlconnector-tutorial/user4.png)

    a. I den **användarnamn** textrutan skriver du det fullständiga namnet på BrittaSimon.

    b. Välj lämpligt alternativ enligt din organisation för **ÅTKOMSTNIVÅ**, **PRIVILEGIET värdet**, och för **ÅTKOMSTSTATUS**.
    
    c. I den **fullständigt namn** textrutan skriver du det fullständiga namnet för Britta Simon.

    d. I den **e-postadress** textrutan skriver du e-postadressen för Britta Simon konto.

    e. I den **lösenord** textrutan skriver du lösenordet för användaren.

    f. I den **BEKRÄFTA lösenord** textrutan skriver du lösenordet för användaren.

    g. Klicka på **Spara**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Jamf Pro.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Jamf Pro, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Jamf Pro**.

    ![Jamf Pro-länk i listan med program](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Jamf Pro i åtkomstpanelen du bör få automatiskt loggat in på ditt program i Jamf Pro.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jamfprosamlconnector-tutorial/tutorial_general_01.png
[2]: ./media/jamfprosamlconnector-tutorial/tutorial_general_02.png
[3]: ./media/jamfprosamlconnector-tutorial/tutorial_general_03.png
[4]: ./media/jamfprosamlconnector-tutorial/tutorial_general_04.png

[100]: ./media/jamfprosamlconnector-tutorial/tutorial_general_100.png

[200]: ./media/jamfprosamlconnector-tutorial/tutorial_general_200.png
[201]: ./media/jamfprosamlconnector-tutorial/tutorial_general_201.png
[202]: ./media/jamfprosamlconnector-tutorial/tutorial_general_202.png
[203]: ./media/jamfprosamlconnector-tutorial/tutorial_general_203.png

