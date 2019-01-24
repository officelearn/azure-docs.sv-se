---
title: 'Självstudier: Azure Active Directory-integrering med Cerner Central | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cerner Central.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d2bc549d-d286-4679-854e-bb67c62b0475
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.openlocfilehash: 82541e8d4eb0a3f603fd70e5ba6724debb4e006c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808153"
---
# <a name="tutorial-azure-active-directory-integration-with-cerner-central"></a>Självstudier: Azure Active Directory-integrering med Cerner Central

I den här självstudien får du lära dig hur du integrerar Cerner centrala med Azure Active Directory (AD Azure).

Integrera Cerner centrala med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Cerner Central
- Du kan aktivera användarna att automatiskt få loggat in på Cerner centrala (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Cerner Central, behöver du följande objekt:

- En Azure AD-prenumeration
- Ett godkända Cerner centrala System-konto

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Cerner centrala från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-cerner-central-from-the-gallery"></a>Att lägga till Cerner centrala från galleriet
För att konfigurera integrering av Cerner centrala i Azure AD, som du behöver lägga till Cerner centrala från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Cerner centrala från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]

1. Lägg till nytt program, klicka på **nytt program** knappen ovanpå dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Cerner centrala**.

    ![Skapa en Azure AD-användare för testning](./media/cernercentral-tutorial/tutorial_cernercentral_search.png)

1. I resultatpanelen väljer **Cerner centrala**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/cernercentral-tutorial/tutorial_cernercentral_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Cerner Central baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarande Indien, centrala Cerner är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren Indien, centrala Cerner upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Cerner Central, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Cerner centrala](#creating-a-cerner-central-test-user)**  – du har en motsvarighet för Britta Simon Indien, centrala Cerner som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Cerner Central.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Cerner Central:**

1. I Azure-portalen på den **Cerner centrala** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/cernercentral-tutorial/tutorial_cernercentral_samlbase.png)

1. På den **Cerner centrala domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/cernercentral-tutorial/tutorial_cernercentral_url.png)

    a. I den **identifierare** textrutan skriver du värdet med följande mönster:

    | |
    |--|
    | `https://<instancename>.cernercentral.com/session-api/protocol/saml2/metadata` |
    | `https://<instancename>.sandboxcernercentral.com/session-api/protocol/saml2/metadata` |
    
    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster:
    | |
    |--|
    | `https://<instancename>.cernercentral.com/session-api/protocol/saml2/sso` |
    | `https://<instancename>.sandboxcernercentral.com/session-api/protocol/saml2/sso` |

    > [!NOTE]
    > De här värdena är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Cerner centrala supportteamet](https://wiki.ucern.com/display/CernerCentral/Contacting+Cloud+Operations) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.

    ![Konfigurera enkel inloggning](./media/cernercentral-tutorial/tutorial_metadataurl.png)

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/cernercentral-tutorial/tutorial_general_400.png)

1. Att konfigurera enkel inloggning på **Cerner centrala** sida, som du behöver skicka den **Appfederationsmetadata** till [Cerner centrala support](https://wiki.ucern.com/display/CernerCentral/Contacting+Cloud+Operations). De kan konfigurera SSO längs program för att slutföra integrationen.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/cernercentral-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.

    ![Skapa en Azure AD-användare för testning](./media/cernercentral-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till**.

    ![Skapa en Azure AD-användare för testning](./media/cernercentral-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:

    ![Skapa en Azure AD-användare för testning](./media/cernercentral-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** för Britta Simon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-cerner-central-test-user"></a>Skapa en testanvändare Cerner Central

**Cerner Central** programmet tillåter autentisering från valfri extern identitetsprovider. Om en användare kan logga in med programmets startsida, de är externa och har inte behov av manuell etablering. Du hittar mer information [här](cernercentral-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Cerner Central.

![Tilldela användare][200]

**Om du vill tilldela Cerner centrala Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

1. I listan med program väljer **Cerner centrala**.

    ![Konfigurera enkel inloggning](./media/cernercentral-tutorial/tutorial_cernercentral_app.png)

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Cerner centrala i åtkomstpanelen du bör få automatiskt loggat in på ditt Cerner Central-program. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera användarförsörjning](cernercentral-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/cernercentral-tutorial/tutorial_general_01.png
[2]: ./media/cernercentral-tutorial/tutorial_general_02.png
[3]: ./media/cernercentral-tutorial/tutorial_general_03.png
[4]: ./media/cernercentral-tutorial/tutorial_general_04.png

[100]: ./media/cernercentral-tutorial/tutorial_general_100.png

[200]: ./media/cernercentral-tutorial/tutorial_general_200.png
[201]: ./media/cernercentral-tutorial/tutorial_general_201.png
[202]: ./media/cernercentral-tutorial/tutorial_general_202.png
[203]: ./media/cernercentral-tutorial/tutorial_general_203.png