---
title: 'Självstudier: Azure Active Directory-integrering med Jive | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 9fc5659a-c116-4a1b-a601-333325a26b46
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.openlocfilehash: fa802e137f9fb76c531814f6128cabfc11a44f0f
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812343"
---
# <a name="tutorial-azure-active-directory-integration-with-jive"></a>Självstudier: Azure Active Directory-integrering med Jive

I den här självstudien får du lära dig hur du integrerar Jive med Azure Active Directory (AD Azure).

Integrera Jive med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Jive
- Du kan aktivera användarna att automatiskt få loggat in på Jive (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Jive, behöver du följande objekt:

- En Azure AD-prenumeration
- En Jive enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Jive från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-jive-from-the-gallery"></a>Att lägga till Jive från galleriet
Om du vill konfigurera integreringen av Jive i Azure AD, som du behöver lägga till Jive från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Jive från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]

1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Jive**.

    ![Skapa en Azure AD-användare för testning](./media/jive-tutorial/tutorial_jive_search.png)

1. I resultatpanelen väljer **Jive**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/jive-tutorial/tutorial_jive_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Jive baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Jive är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Jive upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Jive.

Om du vill konfigurera och testa Azure AD enkel inloggning med Jive, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Jive](#creating-a-jive-test-user)**  – du har en motsvarighet för Britta Simon i Jive som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Jive-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Jive:**

1. I Azure-portalen på den **Jive** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/jive-tutorial/tutorial_jive_samlbase.png)

1. På den **Jive domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/jive-tutorial/tutorial_jive_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<instance name>.jivecustom.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<instance name>.jiveon.com`

    > [!NOTE]
    > De här värdena är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL:en och identifieraren. Kontakta [Jive klienten supportteamet](https://www.jivesoftware.com/services-support/) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/jive-tutorial/tutorial_jive_certificate.png)

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/jive-tutorial/tutorial_general_400.png)

1. Att konfigurera enkel inloggning på **Jive** sida, inloggning till Jive-klienten som administratör.

1. Klicka på menyn längst upp ”**Saml**”.

    ![Konfigurera enkel inloggning på appsidan](./media/jive-tutorial/tutorial_jive_002.png)

    a. Välj **aktiverat** under den **Allmänt** fliken b. Klicka på ”**spara alla saml-inställningar**” knappen.

1. Navigera till den ”**Idp Metadata**” fliken.

    ![Konfigurera enkel inloggning på appsidan](./media/jive-tutorial/tutorial_jive_003.png)

    a. Kopiera innehållet i den hämtade XML-fil och sedan klistra in den i den **identitet Provider (IDP) Metadata** textrutan.

    b. Klicka på ”**spara alla saml-inställningar**” knappen.

1. Gå till den ”**attributet användarmappning**” fliken.

    ![Konfigurera enkel inloggning på appsidan](./media/jive-tutorial/tutorial_jive_004.png)

    a. I den **e-post** textrutan, kopiera och klistra in attributnamnet för **e** värde.

    b. I den **Förnamn** textrutan, kopiera och klistra in attributnamnet för **givenname** värde.

    c. I den **efternamn** textrutan, kopiera och klistra in attributnamnet för **efternamn** värde.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/jive-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.

    ![Skapa en Azure AD-användare för testning](./media/jive-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.

    ![Skapa en Azure AD-användare för testning](./media/jive-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:

    ![Skapa en Azure AD-användare för testning](./media/jive-tutorial/create_aaduser_04.png)

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-jive-test-user"></a>Skapa en Jive testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Jive. Jive stöder automatisk användaretablering, vilket är som standard aktiverat. Du hittar mer information [här](jive-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

Om du vill skapa användare manuellt kan arbeta med [Jive klienten supportteamet](https://www.jivesoftware.com/services-support/) att lägga till användare i Jive-plattformen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Jive.

![Tilldela användare][200] 

**Om du vill tilldela Jive för Britta Simon, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Jive**.

    ![Konfigurera enkel inloggning](./media/jive-tutorial/tutorial_jive_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Jive i åtkomstpanelen du bör få automatiskt loggat in på ditt Jive-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera användarförsörjning](jive-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/jive-tutorial/tutorial_general_01.png
[2]: ./media/jive-tutorial/tutorial_general_02.png
[3]: ./media/jive-tutorial/tutorial_general_03.png
[4]: ./media/jive-tutorial/tutorial_general_04.png

[100]: ./media/jive-tutorial/tutorial_general_100.png

[200]: ./media/jive-tutorial/tutorial_general_200.png
[201]: ./media/jive-tutorial/tutorial_general_201.png
[202]: ./media/jive-tutorial/tutorial_general_202.png
[203]: ./media/jive-tutorial/tutorial_general_203.png
