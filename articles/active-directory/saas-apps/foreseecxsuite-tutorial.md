---
title: 'Självstudier: Azure Active Directory-integrering med förutser CX Suite | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och förutser CX Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5f4b7830-6186-4d17-b77b-504d4192bfde
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: jeedes
ms.openlocfilehash: b288bcbe14050c0f764f348d5e20186570e32866
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442208"
---
# <a name="tutorial-azure-active-directory-integration-with-foresee-cx-suite"></a>Självstudier: Azure Active Directory-integrering med förutser CX Suite

I den här självstudien får du lära dig hur du integrerar förutser CX Suite med Azure Active Directory (AD Azure).

Integrera förutser CX Suite med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till förutser CX Suite.
- Du kan aktivera användarna att automatiskt få loggat in på förutser CX Suite (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med förutser CX Suite, behöver du följande objekt:

- En Azure AD-prenumeration
- En förutser CX Suite enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till förutser CX Suite från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-foresee-cx-suite-from-the-gallery"></a>Att lägga till förutser CX Suite från galleriet
Om du vill konfigurera integreringen av förutser CX Suite till Azure AD, som du behöver lägga till förutser CX Suite från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till förutser CX Suite från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **förutser CX Suite**väljer **förutser CX Suite** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Förutse CX Suite i resultatlistan](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med förutser CX Suite baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i förutser CX Suite är en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i förutser CX Suite upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med förutser CX Suite, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare förutser CX Suite](#create-a-foresee-cx-suite-test-user)**  – du har en motsvarighet för Britta Simon i förutser CX Suite som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt förutser CX Suite-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med förutser CX Suite:**

1. I Azure-portalen på den **förutser CX Suite** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_samlbase.png)

1. På den **förutser CX Suite-domän och URL: er** om du har **tjänstleverantör metadatafil**, utför följande steg:

    ![Förutse CX Suite domän och URL: er enkel inloggning för information](./media/foreseecxsuite-tutorial/upload.png)

    a. Klicka på **ladda upp metadatafilen**.

    ![Förutse CX Suite domän och URL: er enkel inloggning för information](./media/foreseecxsuite-tutorial/tutorial_foreseen_uploadconfig.png)

    b. Klicka på **mappen logotyp** att välja metadatafilen och klicka på **överför**.

    c. När installationen har slutförts överför **tjänstleverantör metadatafil** den **identifierare** värdet get automatiskt ifylld i **förutser CX Suite-domän och URL: er** avsnittet textrutan som visas nedan:

    ![Förutse CX Suite domän och URL: er enkel inloggning för information](./media/foreseecxsuite-tutorial/urlupload.png)

1. Om du inte har **tjänstleverantör metadatafil**, utför följande steg:

    ![Förutse CX Suite domän och URL: er enkel inloggning för information](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_url.png)

    a. I den **inloggnings-URL** textrutan anger du URL: `https://cxsuite.foresee.com/`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://www.okta.com/saml2/service-provider/<UniqueID>`

    > [!NOTE]
    > ID-värde är inte verkliga. Uppdatera det här värdet med det faktiska ID: T. Kontakta [förutser CX Suite klienten supportteamet](mailto:support@foresee.com) att hämta det här värdet.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_certificate.png)

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/foreseecxsuite-tutorial/tutorial_general_400.png)

1. Att konfigurera enkel inloggning på **förutser CX Suite** sida, som du behöver skicka de hämtade **XML-Metadata för** till [förutser CX Suite supportteamet](mailto:support@foresee.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/foreseecxsuite-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/foreseecxsuite-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/foreseecxsuite-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/foreseecxsuite-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-foresee-cx-suite-test-user"></a>Skapa en testanvändare förutser CX Suite

I det här avsnittet skapar du en användare som kallas Britta Simon i förutser CX Suite. Arbeta med [förutser CX Suite supportteamet](mailto:support@foresee.com) att lägga till användare eller domän som behövs för att bli godkänd i plattformen förutser CX Suite. Om domänen har lagts till av teamet, kommer användare får automatiskt tillhandahållas till plattformen förutser CX Suite. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till förutser CX Suite.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon förutser CX Suite, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

1. I listan med program väljer **förutser CX Suite**.

    ![Länken förutser CX Suite i listan med program](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_app.png)

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen förutser CX Suite i åtkomstpanelen du bör få automatiskt loggat in på ditt förutser CX Suite-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/foreseecxsuite-tutorial/tutorial_general_01.png
[2]: ./media/foreseecxsuite-tutorial/tutorial_general_02.png
[3]: ./media/foreseecxsuite-tutorial/tutorial_general_03.png
[4]: ./media/foreseecxsuite-tutorial/tutorial_general_04.png

[100]: ./media/foreseecxsuite-tutorial/tutorial_general_100.png

[200]: ./media/foreseecxsuite-tutorial/tutorial_general_200.png
[201]: ./media/foreseecxsuite-tutorial/tutorial_general_201.png
[202]: ./media/foreseecxsuite-tutorial/tutorial_general_202.png
[203]: ./media/foreseecxsuite-tutorial/tutorial_general_203.png

