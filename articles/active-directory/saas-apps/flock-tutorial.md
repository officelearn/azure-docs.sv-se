---
title: 'Självstudier: Azure Active Directory-integrering med besättningen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och besättningen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7b2c3ac5-17f1-49a0-8961-c541b258d4b1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: d12180ef14c8a91893a4a158ff1b1c5cac06f0e1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55176037"
---
# <a name="tutorial-azure-active-directory-integration-with-flock"></a>Självstudier: Azure Active Directory-integrering med besättningen

I den här självstudien får du lära dig hur du integrerar besättningen med Azure Active Directory (AD Azure).

Integrerande besättningen med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till besättningen.
- Du kan aktivera användarna att automatiskt få loggat in på besättningen (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med besättningen, behöver du följande objekt:

- En Azure AD-prenumeration
- En besättningen enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till besättningen från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-flock-from-the-gallery"></a>Att lägga till besättningen från galleriet
Om du vill konfigurera integreringen av besättningen till Azure AD, som du behöver lägga till besättningen från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till besättningen från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **besättningen**väljer **besättningen** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Besättningen i resultatlistan](./media/flock-tutorial/tutorial_flock_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med besättningen baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i besättningen är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i besättningen upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med besättningen, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare i besättningen](#create-a-flock-test-user)**  – du har en motsvarighet för Britta Simon i besättningen som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i besättningen programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med besättningen:**

1. I Azure-portalen på den **besättningen** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/flock-tutorial/tutorial_flock_samlbase.png)

1. På den **besättningen domän och URL: er** avsnittet, utför följande steg:

    ![Besättningen domän och URL: er med enkel inloggning för information](./media/flock-tutorial/tutorial_flock_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<subdomain>.flock.com/`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<subdomain>.flock.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [besättningen klienten supportteamet](mailto:support@flock.com) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/flock-tutorial/tutorial_flock_certificate.png)

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/flock-tutorial/tutorial_general_400.png)

1. På den **besättningen Configuration** klickar du på **konfigurera besättningen** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Besättningen konfiguration](./media/flock-tutorial/tutorial_flock_configure.png) 

1. I ett annat webbläsarfönster logga du in på webbplatsen besättningen företagets som administratör.

1. Välj **autentisering** fliken från den vänstra navigeringspanelen och välj sedan **SAML-autentisering**.

    ![Besättningen konfiguration](./media/flock-tutorial/configure1.png)

1. I den **SAML-autentisering** avsnittet, utför följande steg:

    ![Besättningen konfiguration](./media/flock-tutorial/configure2.png)

    a. I den **SAML 2.0 Endpoint(HTTP)** textrutan klistra in **SAML enkel inloggning för tjänst-URL** värde som du har kopierat från Azure-portalen.

    b. I den **Providerutgivare** textrutan klistra in **SAML entitets-ID** värde som du har kopierat från Azure-portalen.

    c. Öppna den hämtade **Certificate(Base64)** från Azure-portalen i anteckningar, klistra in innehållet i den **offentligt certifikat** textrutan.

    d. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/flock-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/flock-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/flock-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/flock-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-flock-test-user"></a>Skapa en testanvändare i besättningen

Om du vill aktivera Azure AD-användare att logga in på besättningen, måste de etableras i besättningen. När det gäller besättningen är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen besättningen företagets som administratör.

1. Klicka på **hantera Team** från den vänstra navigeringspanelen.

    ![Lägga till medarbetare](./media/flock-tutorial/user1.png)

1. Klicka på **Lägg till medlem** fliken och välj sedan **teammedlemmar**.

    ![Lägga till medarbetare](./media/flock-tutorial/user2.png)

1. Ange e-postadress för användaren som **Brittasimon@contoso.com** och välj sedan **Lägg till användare**.

    ![Lägga till medarbetare](./media/flock-tutorial/user3.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till besättningen.

![Tilldela rollen][200]

**Om du vill tilldela besättningen Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

1. I listan med program väljer **besättningen**.

    ![Länken besättningen i listan med program](./media/flock-tutorial/tutorial_flock_app.png)

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen besättningen i åtkomstpanelen du bör få automatiskt loggat in på ditt besättningen-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/flock-tutorial/tutorial_general_01.png
[2]: ./media/flock-tutorial/tutorial_general_02.png
[3]: ./media/flock-tutorial/tutorial_general_03.png
[4]: ./media/flock-tutorial/tutorial_general_04.png

[100]: ./media/flock-tutorial/tutorial_general_100.png

[200]: ./media/flock-tutorial/tutorial_general_200.png
[201]: ./media/flock-tutorial/tutorial_general_201.png
[202]: ./media/flock-tutorial/tutorial_general_202.png
[203]: ./media/flock-tutorial/tutorial_general_203.png
