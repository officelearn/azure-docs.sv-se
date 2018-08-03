---
title: 'Självstudier: Azure Active Directory-integration med mindWireless | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och mindWireless.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bd00a339-27c9-4904-b66f-a95bf597ac3c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: jeedes
ms.openlocfilehash: 6c6fe0a720795c67a7062f5a5971c699472fca07
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434354"
---
# <a name="tutorial-azure-active-directory-integration-with-mindwireless"></a>Självstudier: Azure Active Directory-integration med mindWireless

I den här självstudien får du lära dig hur du integrerar mindWireless med Azure Active Directory (AD Azure).

Integrera mindWireless med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till mindWireless.
- Du kan aktivera användarna att automatiskt få loggat in på mindWireless (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med mindWireless, behöver du följande objekt:

- En Azure AD-prenumeration
- En mindWireless enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till mindWireless från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-mindwireless-from-the-gallery"></a>Att lägga till mindWireless från galleriet
För att konfigurera integrering av mindWireless i Azure AD, som du behöver lägga till mindWireless från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till mindWireless från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **mindWireless**väljer **mindWireless** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![mindWireless i resultatlistan](./media/mindwireless-tutorial/tutorial_mindwireless_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med mindWireless baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i mindWireless är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i mindWireless upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med mindWireless, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare mindWireless](#create-a-mindwireless-test-user)**  – du har en motsvarighet för Britta Simon i mindWireless som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt mindWireless program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med mindWireless:**

1. I Azure-portalen på den **mindWireless** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/mindwireless-tutorial/tutorial_mindwireless_samlbase.png)

1. På den **mindWireless domän och URL: er** avsnittet, utför följande steg:

    ![mindWireless domän och URL: er med enkel inloggning för information](./media/mindwireless-tutorial/tutorial_mindwireless_url.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.mwsmart.com/`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.mwsmart.com/SAML/AssertionConsumerService.aspx`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare och svars-URL. Kontakta [mindWireless supportteam](mailto:sdulloor@mindwireless.com) att hämta dessa värden.

1. MindWireless programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen.

1. Följande skärmbild visar ett exempel för den. Anspråkets namn alltid vara **anställnings-ID** och värdet som vi har mappats till user.employeeid som innehåller EmployeeID för användaren. Användarmappning från Azure AD till mindWireless görs här på EmployeeID men du kan mappa den till ett annat värde även baserat på dina inställningar för programmet. Du kan arbeta med den [mindWireless supportteam](mailto:sdulloor@mindwireless.com) först att använda rätt identifierare för en användare och mappa värdet med den **anställnings-ID** anspråk.

    ![Konfigurera enkel inloggning](./media/mindwireless-tutorial/tutorial_attribute.png)

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i föregående bild och utför följande steg:
    
    | Attributnamn | Attributvärde | Namespace värde |
    | -------------- | --------------- | ----------------|
    | Anställnings-ID | User.EmployeeID | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/mindwireless-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/mindwireless-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. I den **Namespace** textrutan skriver namnområdesvärdet som visas för den raden.
    
    e. Klicka på **OK**.
    
1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/mindwireless-tutorial/tutorial_mindwireless_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/mindwireless-tutorial/tutorial_general_400.png)

1. På den **mindWireless Configuration** klickar du på **konfigurera mindWireless** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![mindWireless konfiguration](./media/mindwireless-tutorial/tutorial_mindwireless_configure.png) 

1. Att konfigurera enkel inloggning på **mindWireless** sida, som du behöver skicka de hämtade **Certificate(Base64), SAML enkel inloggning för tjänst-URL**, och **SAML entitets-ID** till [mindWireless supportteam](mailto:sdulloor@mindwireless.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/mindwireless-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/mindwireless-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/mindwireless-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/mindwireless-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-mindwireless-test-user"></a>Skapa en mindWireless testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i mindWireless. Arbeta med [mindWireless supportteam](mailto:sdulloor@mindwireless.com) att lägga till användare i mindWireless-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till mindWireless.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon mindWireless, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **mindWireless**.

    ![Länken mindWireless i listan med program](./media/mindwireless-tutorial/tutorial_mindwireless_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen mindWireless i åtkomstpanelen du bör få automatiskt loggat in på ditt mindWireless program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/mindwireless-tutorial/tutorial_general_01.png
[2]: ./media/mindwireless-tutorial/tutorial_general_02.png
[3]: ./media/mindwireless-tutorial/tutorial_general_03.png
[4]: ./media/mindwireless-tutorial/tutorial_general_04.png

[100]: ./media/mindwireless-tutorial/tutorial_general_100.png

[200]: ./media/mindwireless-tutorial/tutorial_general_200.png
[201]: ./media/mindwireless-tutorial/tutorial_general_201.png
[202]: ./media/mindwireless-tutorial/tutorial_general_202.png
[203]: ./media/mindwireless-tutorial/tutorial_general_203.png

