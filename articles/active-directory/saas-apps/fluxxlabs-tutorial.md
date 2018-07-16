---
title: 'Självstudier: Azure Active Directory-integrering med Fluxx Labs | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Fluxx Labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 7bb15323a1f4ead02a24c4ee451de8890a635032
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39053059"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Självstudier: Azure Active Directory-integrering med Fluxx Labs

I den här självstudien får du lära dig hur du integrerar Fluxx Labs med Azure Active Directory (AD Azure).

Integrera Fluxx Labs med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Fluxx Labs.
- Du kan aktivera användarna att automatiskt få loggat in på Fluxx Labs (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Fluxx Labs, behöver du följande objekt:

- En Azure AD-prenumeration
- En Fluxx Labs enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Fluxx Labs från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-fluxx-labs-from-the-gallery"></a>Att lägga till Fluxx Labs från galleriet
För att konfigurera integrering av Fluxx labb i Azure AD, som du behöver lägga till Fluxx Labs från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Fluxx Labs från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Fluxx Labs**väljer **Fluxx Labs** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Fluxx Labs i resultatlistan](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Fluxx Labs baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Fluxx Labs är en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Fluxx Labs upprättas.

I Fluxx Labs, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Fluxx Labs, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Fluxx Labs](#create-a-fluxx-labs-test-user)**  – du har en motsvarighet för Britta Simon i Fluxx Labs som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Fluxx Labs-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Fluxx Labs:**

1. I Azure-portalen på den **Fluxx Labs** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_samlbase.png)

3. På den **Fluxx Labs domän och URL: er** avsnittet, utför följande steg:

    ![Fluxx Labs domän och URL: er med enkel inloggning för information](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_url.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster:

    | Miljö | URL-mönster|
    |-------------|------------|
    | Produktion | `https://<subdomain>.fluxx.io` |
    | Förproduktion | `https://<subdomain>.preprod.fluxxlabs.com`|
        
    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster:

    | Miljö | URL-mönster|
    |-------------|------------|
    | Produktion | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Förproduktion | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare och svars-URL. Kontakta [Fluxx Labs supportteam](mailto:travis@fluxxlabs.com) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/fluxxlabs-tutorial/tutorial_general_400.png)

6. På den **Fluxx Labs Configuration** klickar du på **konfigurera Fluxx Labs** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Fluxx Labs konfiguration](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_configure.png)

7. I ett annat webbläsarfönster inloggning till webbplatsen Fluxx Labs företagets som administratör.

8. Välj **Admin** under den **inställningar** avsnittet.

    ![Fluxx Labs konfiguration](./media/fluxxlabs-tutorial/config1.png)

9. I panelen administratören väljer **plugins** > **integreringar** och välj sedan **SAML SSO-(Disabled)**

    ![Fluxx Labs konfiguration](./media/fluxxlabs-tutorial/config2.png)

10. Utför följande steg i avsnittet attribut:

    ![Fluxx Labs konfiguration](./media/fluxxlabs-tutorial/config3.png)

    a. Välj den **SAML SSO** kryssrutan.

    b. I den **begär sökväg** textrutan typ **/auth/saml**.

    c. I den **återanrop sökväg** textrutan typ **/auth/saml/callback**.

    d. I den **Assertion konsument Service Url(Single Sign-On URL)** textrutan anger du den **svars-URL** värde, som du har angett i Azure-portalen.

    e. I den **målgrupp (SP entitets-ID)** textrutan anger du den **identifierare** värde, som du har angett i Azure-portalen.

    f. I den **SSO mål-URL för identitetsprovider** textrutan klistra in den **SAML enkel inloggning för tjänst-URL** värde, som du har kopierat från Azure-portalen.

    g. Öppna din Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **providern identitetscertifikat** textrutan.

    h. I **namnidentifierare Format** textrutan anger du värdet `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Klicka på **Spara**.

    > [!NOTE]
    > När innehållet sparas, fältet visas tomt för säkerhet, men värdet har sparats i konfigurationen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/fluxxlabs-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/fluxxlabs-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/fluxxlabs-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/fluxxlabs-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-a-fluxx-labs-test-user"></a>Skapa en testanvändare Fluxx Labs

Om du vill aktivera Azure AD-användare att logga in på Fluxx Labs, måste de etableras i Fluxx Labs. När det gäller Fluxx Labs är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen Fluxx Labs företagets som administratör.

2. Klicka på den nedan visas **ikonen**.

    ![Fluxx Labs konfiguration](./media/fluxxlabs-tutorial/config6.png)

3. På instrumentpanelen, klickar du på den nedan visas ikonen för att öppna den **nya personer** kort.

    ![Fluxx Labs konfiguration](./media/fluxxlabs-tutorial/config4.png)

4. På den **nya personer** avsnittet, utför följande steg:

    ![Fluxx Labs konfiguration](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs använda e-post som den unika identifieraren för SSO-inloggningar. Fyll i den **SSO UID** med användarens e-postadress som matchar den e-postadress som de använder som inloggning med enkel inloggning.

    b. Klicka på **Spara**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Fluxx Labs.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon Fluxx Labs, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Fluxx Labs**.

    ![Länken Fluxx Labs i listan med program](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Fluxx Labs i åtkomstpanelen du bör få automatiskt loggat in på ditt Fluxx Labs-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/fluxxlabs-tutorial/tutorial_general_01.png
[2]: ./media/fluxxlabs-tutorial/tutorial_general_02.png
[3]: ./media/fluxxlabs-tutorial/tutorial_general_03.png
[4]: ./media/fluxxlabs-tutorial/tutorial_general_04.png

[100]: ./media/fluxxlabs-tutorial/tutorial_general_100.png

[200]: ./media/fluxxlabs-tutorial/tutorial_general_200.png
[201]: ./media/fluxxlabs-tutorial/tutorial_general_201.png
[202]: ./media/fluxxlabs-tutorial/tutorial_general_202.png
[203]: ./media/fluxxlabs-tutorial/tutorial_general_203.png
