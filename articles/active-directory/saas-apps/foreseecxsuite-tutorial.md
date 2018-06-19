---
title: 'Självstudier: Azure Active Directory-integrering med förutse CX Suite | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och förutse CX Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5f4b7830-6186-4d17-b77b-504d4192bfde
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: jeedes
ms.openlocfilehash: 5cf217de4973fd06935d88381de86520df604c29
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35966642"
---
# <a name="tutorial-azure-active-directory-integration-with-foresee-cx-suite"></a>Självstudier: Azure Active Directory-integrering med förutse CX Suite

I kursen får lära du att integrera förutse CX Suite med Azure Active Directory (AD Azure).

Integrera förutse CX Suite med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till förutse CX-paket.
- Du kan aktivera användarna att automatiskt hämta inloggade till förutse CX-paket (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med förutse CX Suite, behöver du följande:

- En Azure AD-prenumeration
- En förutse CX Suite enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till förutse CX Suite från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-foresee-cx-suite-from-the-gallery"></a>Att lägga till förutse CX Suite från galleriet
Du måste lägga till förutse CX Suite från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av förutse CX Suite i Azure AD.

**Utför följande steg för att lägga till förutse CX Suite från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]

3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **förutse CX Suite**väljer **förutse CX Suite** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Förutse CX Suite i resultatlistan](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med förutse CX Suite baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i förutse CX Suite motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i förutse CX Suite upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med förutse CX Suite, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare förutse CX Suite](#create-a-foresee-cx-suite-test-user)**  – har en motsvarighet för Britta Simon förutse CX-paket som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt förutse CX Suite-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med förutse CX Suite:**

1. I Azure-portalen på den **förutse CX Suite** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning dialogrutan](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_samlbase.png)

3. På den **förutse CX Suite domän och URL: er** om du har **tjänstleverantör metadatafil**, utför följande steg:

    ![Förutse CX Suite domän URL: er och enkel inloggning information](./media/foreseecxsuite-tutorial/upload.png)

    a. Klicka på **överför metadatafil**.

    ![Förutse CX Suite domän URL: er och enkel inloggning information](./media/foreseecxsuite-tutorial/tutorial_foreseen_uploadconfig.png)

    b. Klicka på **mappen logotypen** väljer metadatafilen och klickar på **överför**.

    c. Efter slutförd överför **tjänstleverantör metadatafil** den **identifierare** värdet get automatiskt fylls i **förutse CX Suite domän och URL: er** avsnittet textrutan som visas nedan:

    ![Förutse CX Suite domän URL: er och enkel inloggning information](./media/foreseecxsuite-tutorial/urlupload.png)

4. Om du inte har **tjänstleverantör metadatafil**, utför följande steg:

    ![Förutse CX Suite domän URL: er och enkel inloggning information](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_url.png)

    a. I den **inloggnings-URL** textruta anger du URL: `https://cxsuite.foresee.com/`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://www.okta.com/saml2/service-provider/<UniqueID>`

    > [!NOTE]
    > ID-värdet är inte verkliga. Uppdatera det här värdet med den faktiska identifieraren. Kontakta [förutse CX Suite klienten supportteamet](mailto:support@foresee.com) att hämta det här värdet.

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_certificate.png)

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/foreseecxsuite-tutorial/tutorial_general_400.png)

7. Konfigurera enkel inloggning på **förutse CX Suite** sida, måste du skicka den hämtade **XML-Metadata för** till [förutse CX Suite supportteamet](mailto:support@foresee.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/foreseecxsuite-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/foreseecxsuite-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/foreseecxsuite-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/foreseecxsuite-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.

### <a name="create-a-foresee-cx-suite-test-user"></a>Skapa en testanvändare förutse CX Suite

I det här avsnittet kan du skapa en användare som kallas Britta Simon i förutse CX Suite. Arbeta med [förutse CX Suite supportteamet](mailto:support@foresee.com) att lägga till användare eller domän som krävs för att vara godkända i förutse CX Suite-plattformen. Om domänen har lagts till av teamet, kommer användarna få automatiskt tillhandahållas för förutse CX Suite-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till förutse CX Suite.

![Tilldela rollen][200]

**Om du vill tilldela förutse CX Suite Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201]

2. Välj i listan med program **förutse CX Suite**.

    ![Länken förutse CX-paket i listan med program](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_app.png)

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen förutse CX Suite på åtkomstpanelen du bör få automatiskt loggat in på ditt förutse CX Suite-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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

