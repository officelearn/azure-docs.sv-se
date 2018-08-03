---
title: 'Självstudier: Azure Active Directory-integration med LaunchDarkly | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LaunchDarkly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0e9cb37e-16bf-493b-bfc8-8d9840545a1e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 54bf459f6acd7649f3ad1a546bef1d0429393161
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420767"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Självstudier: Azure Active Directory-integration med LaunchDarkly

I den här självstudien får du lära dig hur du integrerar LaunchDarkly med Azure Active Directory (AD Azure).

Integrera LaunchDarkly med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till LaunchDarkly.
- Du kan aktivera användarna att automatiskt få loggat in på LaunchDarkly (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med LaunchDarkly, behöver du följande objekt:

- En Azure AD-prenumeration
- En LaunchDarkly enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till LaunchDarkly från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-launchdarkly-from-the-gallery"></a>Att lägga till LaunchDarkly från galleriet
För att konfigurera integrering av LaunchDarkly i Azure AD, som du behöver lägga till LaunchDarkly från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till LaunchDarkly från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **LaunchDarkly**väljer **LaunchDarkly** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![LaunchDarkly i resultatlistan](./media/launchdarkly-tutorial/tutorial_launchdarkly_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med LaunchDarkly baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i LaunchDarkly är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i LaunchDarkly upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med LaunchDarkly, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare LaunchDarkly](#create-a-launchdarkly-test-user)**  – du har en motsvarighet för Britta Simon i LaunchDarkly som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt LaunchDarkly program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med LaunchDarkly:**

1. I Azure-portalen på den **LaunchDarkly** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/launchdarkly-tutorial/tutorial_launchdarkly_samlbase.png)

1. På den **LaunchDarkly domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![LaunchDarkly domän och URL: er med enkel inloggning för information](./media/launchdarkly-tutorial/tutorial_launchdarkly_url.png)

    a. I den **identifierare (entitets-ID)** textrutan anger du URL: `app.launchdarkly.com`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`
    
    > [!NOTE]
    > Svars-URL-värdet är inte verkliga. Du ska uppdatera värdet med faktiska svars-URL som beskrivs senare i självstudien.

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![LaunchDarkly domän och URL: er med enkel inloggning för information](./media/launchdarkly-tutorial/tutorial_launchdarkly_url1.png)

    I den **inloggnings-URL** textrutan anger du URL: `https://app.launchdarkly.com`

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/launchdarkly-tutorial/tutorial_launchdarkly_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/launchdarkly-tutorial/tutorial_general_400.png)
    
1. På den **LaunchDarkly Configuration** klickar du på **konfigurera LaunchDarkly** att öppna **konfigurera inloggning** fönster. Kopiera den **enkel inloggnings-URL för** från den **Snabbreferens avsnittet.**

    ![LaunchDarkly konfiguration](./media/launchdarkly-tutorial/tutorial_launchdarkly_configure.png)

1. Logga in på webbplatsen LaunchDarkly företag som en administratör i ett annat webbläsarfönster.

1. Välj **kontoinställningar** från den vänstra navigeringspanelen.

    ![LaunchDarkly konfiguration](./media/launchdarkly-tutorial/configure1.png)

1. Klicka på **Security** fliken.

    ![LaunchDarkly konfiguration](./media/launchdarkly-tutorial/configure2.png)

1. Klicka på **aktivera SSO** och sedan **redigera SAML-konfiguration**.

    ![LaunchDarkly konfiguration](./media/launchdarkly-tutorial/configure3.png)

1. På den **redigera SAML-konfiguration** avsnittet, utför följande steg:

    ![LaunchDarkly konfiguration](./media/launchdarkly-tutorial/configure4.png)

    a. Kopiera den **SAML URL för konsumenttjänst** för din instans och klistra in den i svars-URL-textrutan i **LaunchDarkly domän och URL: er** avsnittet på Azure-portalen.

    b. I den **inloggnings-URL** textrutan klistra in den **enkel inloggnings-URL för** värde, som du har kopierat från Azure-portalen.

    c. Öppna det nedladdade certifikatet från Azure-portalen i anteckningar, kopiera innehållet och klistra in den i den **X.509-certifikat** box eller du kan direkt ladda upp certifikatet genom att klicka på den **ladda upp en**.

    d. Klicka på **Spara**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/launchdarkly-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/launchdarkly-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/launchdarkly-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/launchdarkly-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-launchdarkly-test-user"></a>Skapa en LaunchDarkly testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i LaunchDarkly. LaunchDarkly stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt LaunchDarkly om det inte finns ännu.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [LaunchDarkly klienten supportteamet](mailto:support@launchdarkly.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LaunchDarkly.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon LaunchDarkly, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **LaunchDarkly**.

    ![Länken LaunchDarkly i listan med program](./media/launchdarkly-tutorial/tutorial_launchdarkly_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen LaunchDarkly i åtkomstpanelen du bör få automatiskt loggat in på ditt LaunchDarkly program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/launchdarkly-tutorial/tutorial_general_01.png
[2]: ./media/launchdarkly-tutorial/tutorial_general_02.png
[3]: ./media/launchdarkly-tutorial/tutorial_general_03.png
[4]: ./media/launchdarkly-tutorial/tutorial_general_04.png

[100]: ./media/launchdarkly-tutorial/tutorial_general_100.png

[200]: ./media/launchdarkly-tutorial/tutorial_general_200.png
[201]: ./media/launchdarkly-tutorial/tutorial_general_201.png
[202]: ./media/launchdarkly-tutorial/tutorial_general_202.png
[203]: ./media/launchdarkly-tutorial/tutorial_general_203.png

