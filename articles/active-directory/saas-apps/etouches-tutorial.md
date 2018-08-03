---
title: 'Självstudier: Azure Active Directory-integration med etouches | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och etouches.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 76cccaa8-859c-4c16-9d1d-8a6496fc7520
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 6850763aa13e30265ca055482917edd28e4759d6
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425045"
---
# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>Självstudier: Azure Active Directory-integration med etouches

I den här självstudien får du lära dig hur du integrerar etouches med Azure Active Directory (AD Azure).

Integrera etouches med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till etouches
- Du kan aktivera användarna att automatiskt få loggat in på etouches (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med etouches, behöver du följande objekt:

- En Azure AD-prenumeration
- En etouches enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till etouches från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-etouches-from-the-gallery"></a>Att lägga till etouches från galleriet
För att konfigurera integrering av etouches i Azure AD, som du behöver lägga till etouches från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till etouches från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **etouches**väljer **etouches** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![etouches i resultatlistan](./media/etouches-tutorial/tutorial_etouches_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med etouches baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i etouches är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i etouches upprättas.

I etouches, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med etouches, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare etouches](#create-an-etouches-test-user)**  – du har en motsvarighet för Britta Simon i etouches som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt etouches program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med etouches:**

1. I Azure-portalen på den **etouches** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/etouches-tutorial/tutorial_etouches_samlbase.png)

1. På den **etouches domän och URL: er** avsnittet, utför följande steg:

    ![etouches domän och URL: er med enkel inloggning för information](./media/etouches-tutorial/tutorial_etouches_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://www.eiseverywhere.com/saml/accounts/?sso&accountid=<ACCOUNTID>`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://www.eiseverywhere.com/<instance name>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Du kan uppdatera värdet med faktiska inloggnings-URL och identifierare som beskrivs senare i självstudien.
    > 

1. etouches program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut** av programmet. Följande skärmbild visar ett exempel för detta. 

    ![Användarattribut](./media/etouches-tutorial/tutorial_etouches_attribute.png) 

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ------------------- | -------------------- |
    | E-post | User.Mail |    
    
    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Lägg till attribut](./media/etouches-tutorial/tutorial_attribute_04.png)

    ![Lägg till attribut-dialogrutan](./media/etouches-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **OK**. 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/etouches-tutorial/tutorial_etouches_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/etouches-tutorial/tutorial_general_400.png)

1. Utför följande steg för att få SSO konfigurerats för ditt program i etouches programmet: 

    ![etouches konfiguration](./media/etouches-tutorial/tutorial_etouches_06.png) 

    a. Logga in på **etouches** program med hjälp av administratörsrättigheter.
   
    b. Gå till den **SAML** konfiguration.

    c. I den **allmänna inställningar** avsnittet, öppna det nedladdade certifikatet från Azure-portalen i anteckningar, kopiera innehållet och klistra in den i textrutan för IDP-metadata. 

    d. Klicka på den **spara och håll** knappen.
  
    e. Klicka på den **uppdateringsmetadata** i avsnittet SAML-Metadata. 

    f. Detta öppnar sidan och utföra en enkel inloggning. När SSO fungerar kan du ställa in användarnamnet.

    g. I fältet för användarnamn, väljer du den **e-postadress** enligt bilden nedan. 

    h. Kopiera den **SP entitets-ID** värde och klistra in den i den **identifierare** textrutan, som finns i **etouches domän och URL: er** avsnittet på Azure-portalen.

    i. Kopiera den **URL för enkel inloggning / ACS** värde och klistra in den i den **inloggnings-URL** textrutan, som finns i **etouches domän och URL: er** avsnittet på Azure-portalen.
   
> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](./media/etouches-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![”Användare och grupper” och ”alla användare”-länkar](./media/etouches-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Knappen Lägg till](./media/etouches-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan användare](./media/etouches-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-an-etouches-test-user"></a>Skapa en etouches testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i etouches. Arbeta med [etouches klienten supportteam](https://www.etouches.com/event-software/support/customer-support/) att lägga till användare i etouches-plattformen.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till etouches.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon etouches, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **etouches**.

    ![Länken etouches i listan med program](./media/etouches-tutorial/tutorial_etouches_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning


Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen etouches i åtkomstpanelen du bör få automatiskt loggat in på ditt etouches program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/etouches-tutorial/tutorial_general_01.png
[2]: ./media/etouches-tutorial/tutorial_general_02.png
[3]: ./media/etouches-tutorial/tutorial_general_03.png
[4]: ./media/etouches-tutorial/tutorial_general_04.png

[100]: ./media/etouches-tutorial/tutorial_general_100.png

[200]: ./media/etouches-tutorial/tutorial_general_200.png
[201]: ./media/etouches-tutorial/tutorial_general_201.png
[202]: ./media/etouches-tutorial/tutorial_general_202.png
[203]: ./media/etouches-tutorial/tutorial_general_203.png

