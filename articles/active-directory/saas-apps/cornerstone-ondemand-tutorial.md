---
title: 'Självstudier: Azure Active Directory-integration med Hörnstenen OnDemand | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Hörnstenen OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f57c5fef-49b0-4591-91ef-fc0de6d654ab
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: jeedes
ms.openlocfilehash: cfbf431f8fa483b9a34beb71d9b1dbe3e691cdba
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39053254"
---
# <a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>Självstudier: Azure Active Directory-integration med Hörnstenen OnDemand

I den här självstudien får du lära dig hur du integrerar Hörnstenen OnDemand med Azure Active Directory (AD Azure).

Integrera Hörnstenen OnDemand med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Hörnstenen OnDemand
- Du kan aktivera användarna att automatiskt få loggat in på Hörnstenen OnDemand (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Hörnstenen OnDemand, behöver du följande objekt:

- En Azure AD-prenumeration
- En hörnsten OnDemand enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Hörnstenen OnDemand från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Att lägga till Hörnstenen OnDemand från galleriet
För att konfigurera integrering av OnDemand Hörnstenen i Azure AD, som du behöver lägga till Hörnstenen OnDemand från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Hörnstenen OnDemand från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Hörnstenen OnDemand**.

    ![Skapa en Azure AD-användare för testning](./media/cornerstone-ondemand-tutorial/tutorial_cornerstoneondemand_search.png)

5. I resultatpanelen väljer **Hörnstenen OnDemand**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/cornerstone-ondemand-tutorial/tutorial_cornerstoneondemand_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Hörnstenen OnDemand baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Hörnstenen OnDemand är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Hörnstenen OnDemand upprättas.

I Hörnstenen OnDemand tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Hörnstenen OnDemand, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en OnDemand-Hörnstenen testanvändare](#creating-a-cornerstone-ondemand-test-user)**  – du har en motsvarighet för Britta Simon i Hörnstenen OnDemand som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Hörnstenen OnDemand-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Hörnstenen OnDemand:**

1. I Azure-portalen på den **Hörnstenen OnDemand** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/cornerstone-ondemand-tutorial/tutorial_cornerstoneondemand_samlbase.png)

3. På den **Hörnstenen OnDemand domän och URL: er** avsnittet, utföra följande steg:

    ![Konfigurera enkel inloggning](./media/cornerstone-ondemand-tutorial/tutorial_cornerstoneondemand_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<company>.csod.com`

    b. I **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<company>.csod.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [Hörnstenen OnDemand klienten supportteamet](mailTo:moreinfo@csod.com) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/cornerstone-ondemand-tutorial/tutorial_cornerstoneondemand_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/cornerstone-ondemand-tutorial/tutorial_general_400.png)

6. På den **Hörnstenen OnDemand Configuration** klickar du på **konfigurera Hörnstenen OnDemand** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/cornerstone-ondemand-tutorial/tutorial_cornerstoneondemand_configure.png) 

7. Att konfigurera enkel inloggning på **Hörnstenen OnDemand** sida, som du behöver skicka de hämtade **certifikat**, **URL: en för utloggning** och **SAML enkel inloggning Tjänst-URL för** till [Hörnstenen OnDemand-supportteamet](mailTo:moreinfo@csod.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/cornerstone-ondemand-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.

    ![Skapa en Azure AD-användare för testning](./media/cornerstone-ondemand-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.

    ![Skapa en Azure AD-användare för testning](./media/cornerstone-ondemand-tutorial/create_aaduser_03.png)

4. På den **användaren** dialogrutan utför följande steg:

    ![Skapa en Azure AD-användare för testning](./media/cornerstone-ondemand-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-cornerstone-ondemand-test-user"></a>Skapa en OnDemand-Hörnstenen testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Hörnstenen OnDemand. Hörnstenen OnDemand stöder automatisk användaretablering, vilket är som standard aktiverat. Du hittar mer information om [här](cornerstone-ondemand-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du vill skapa användare manuellt kan du utföra följande steg:**

För att konfigurera användaretablering, skicka information (t.ex.: namn, e-post) om Azure AD-användare du vill etablera till den [Hörnstenen OnDemand-supportteamet](mailTo:moreinfo@csod.com).

>[!NOTE]
>Du kan använda alla andra Hörnstenen OnDemand användare konto verktyg för att skapa eller API: er som tillhandahålls av Hörnstenen OnDemand att etablera AAD-användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Hörnstenen OnDemand.

![Tilldela användare][200] 

**Om du vill tilldela Hörnstenen OnDemand Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Hörnstenen OnDemand**.

    ![Konfigurera enkel inloggning](./media/cornerstone-ondemand-tutorial/tutorial_cornerstoneondemand_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen för OnDemand-Hörnstenen i åtkomstpanelen du bör få automatiskt loggat in på ditt Hörnstenen OnDemand-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](cornerstone-ondemand-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/cornerstone-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/cornerstone-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/cornerstone-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/cornerstone-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/cornerstone-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/cornerstone-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/cornerstone-ondemand-tutorial/tutorial_general_203.png
