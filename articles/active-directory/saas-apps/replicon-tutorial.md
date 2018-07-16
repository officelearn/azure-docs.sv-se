---
title: 'Självstudier: Azure Active Directory-integration med Replicon | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Replicon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 10f4749524c4d4b35b83e5637d35f4ef9a8f9ab6
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041338"
---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>Självstudier: Azure Active Directory-integration med Replicon

I den här självstudien får du lära dig hur du integrerar Replicon med Azure Active Directory (AD Azure).

Integrera Replicon med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Replicon.
- Du kan aktivera användarna att automatiskt få loggat in på Replicon (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Replicon, behöver du följande objekt:

- En Azure AD-prenumeration
- En Replicon enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Replicon från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-replicon-from-the-gallery"></a>Att lägga till Replicon från galleriet
För att konfigurera integrering av Replicon i Azure AD, som du behöver lägga till Replicon från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Replicon från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Replicon**väljer **Replicon** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Replicon i resultatlistan](./media/replicon-tutorial/tutorial_replicon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Replicon baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Replicon är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Replicon upprättas.

I Replicon, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Replicon, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Replicon](#create-a-replicon-test-user)**  – du har en motsvarighet för Britta Simon i Replicon som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Replicon program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Replicon:**

1. I Azure-portalen på den **Replicon** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/replicon-tutorial/tutorial_replicon_samlbase.png)

3. På den **Replicon domän och URL: er** avsnittet, utför följande steg:

    ![Replicon domän och URL: er med enkel inloggning för information](./media/replicon-tutorial/tutorial_replicon_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://na2.replicon.com/<companyname>/saml2/sp-sso/post`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://global.replicon.com/<companyname>`

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://global.replicon.com/!/saml2/<companyname>/sso/post`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL, identifierare och svars-URL. Kontakta [Replicon klienten supportteamet](https://www.replicon.com/customerzone/contact-support) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/replicon-tutorial/tutorial_replicon_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/replicon-tutorial/tutorial_general_400.png)

6. Logga in på webbplatsen Replicon företag som en administratör i ett annat webbläsarfönster.

7. Utför följande steg för att konfigurera SAML 2.0:

    ![Aktivera SAML-autentisering](./media/replicon-tutorial/ic777805.png "aktivera SAML-autentisering")

    a. Att visa den **EnableSAML Authentication2** dialogrutan lägger du till följande till din URL efter företagets nyckeln: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Nedan visas schemat för den fullständiga URL: en: `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Klicka på den **+** att expandera den **v20Configuration** avsnittet.

   c. Klicka på den **+** att expandera den **metaDataConfiguration** avsnittet.

   d. Klicka på **Välj fil**, för att välja din identitet providern XML-fil och klicka på **skicka**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/replicon-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/replicon-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/replicon-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/replicon-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-replicon-test-user"></a>Skapa en Replicon testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Replicon. Replicon stöder automatisk användaretablering, vilket är som standard aktiverat. Du hittar mer information om [här](replicon-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du vill skapa användare manuellt kan du utföra följande steg:**

1. Logga in på webbplatsen Replicon företag som en administratör i ett webbläsarfönster.

2. Gå till **Administration \> användare**.

    ![Användare](./media/replicon-tutorial/ic777806.png "användare")

3. Klicka på **+ Lägg till användare**.

    ![Lägg till användare](./media/replicon-tutorial/ic777807.png "lägga till användare")

4. I den **användarprofil** avsnittet, utför följande steg:

    ![Användarprofil](./media/replicon-tutorial/ic777808.png "användarprofil")

    a. I den **inloggningsnamn** textrutan Ange Azure AD-e-postadress för Azure AD-användare som du vill etablera som **BrittaSimon@contoso.com**.

    b. Som **autentiseringstyp**väljer **SSO**.

    c. I den **avdelning** textrutan skriver användarens avdelning.

    d. Som **typ av anställd**väljer **administratör**.

    e. Klicka på **spara användarprofil**.

>[!NOTE]
>Du kan använda alla andra Replicon användare konto verktyg för att skapa eller API: er som tillhandahålls av Replicon att etablera användarkonton i Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Replicon.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon Replicon, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Replicon**.

    ![Länken Replicon i listan med program](./media/replicon-tutorial/tutorial_replicon_app.png)

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Replicon i åtkomstpanelen du bör få automatiskt loggat in på ditt Replicon program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](replicon-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/replicon-tutorial/tutorial_general_01.png
[2]: ./media/replicon-tutorial/tutorial_general_02.png
[3]: ./media/replicon-tutorial/tutorial_general_03.png
[4]: ./media/replicon-tutorial/tutorial_general_04.png

[100]: ./media/replicon-tutorial/tutorial_general_100.png

[200]: ./media/replicon-tutorial/tutorial_general_200.png
[201]: ./media/replicon-tutorial/tutorial_general_201.png
[202]: ./media/replicon-tutorial/tutorial_general_202.png
[203]: ./media/replicon-tutorial/tutorial_general_203.png
