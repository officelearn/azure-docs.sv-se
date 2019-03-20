---
title: 'Självstudier: Azure Active Directory-integrering med Questetra BPM Suite | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Questetra BPM Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78b069e6534d8394de1f9a9fcdf51b871441c386
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838042"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Självstudier: Azure Active Directory-integrering med Questetra BPM Suite

I den här självstudien får du lära dig hur du integrerar Questetra BPM Suite med Azure Active Directory (AD Azure).

Integrera Questetra BPM Suite med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Questetra BPM Suite
- Du kan aktivera användarna att automatiskt få loggat in på Questetra BPM Suite (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Questetra BPM Suite, behöver du följande objekt:

- En Azure AD-prenumeration
- En Questetra BPM Suite enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägg till Questetra BPM Suite från galleriet
1. Konfigurera och testa enkel inloggning med Azure AD

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>Lägg till Questetra BPM Suite från galleriet
Om du vill konfigurera integreringen av Questetra BPM Suite till Azure AD, som du behöver lägga till Questetra BPM Suite från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Questetra BPM Suite från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Questetra BPM Suite**väljer **Questetra BPM Suite** resultatet panelen och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Lägg till från galleriet](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Questetra BPM Suite baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Questetra BPM Suite är en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Questetra BPM Suite upprättas.

I Questetra BPM Suite, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Questetra BPM Suite, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Questetra BPM Suite](#create-a-questetra-bpm-suite-test-user)**  – du har en motsvarighet för Britta Simon i Questetra BPM Suite som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Questetra BPM Suite-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Questetra BPM Suite:**

1. I Azure-portalen på den **Questetra BPM Suite** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![SAML-baserad inloggning](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_samlbase.png)

1. På den **Questetra BPM Suite domän och URL: er** avsnittet, utför följande steg:

    ![Questetra BPM Suite domän och URL: er](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<subdomain>.questetra.net/`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Du kan hämta dessa värden från **SP Information** avsnittet på din **Questetra BPM Suite** företagets webbplats, vilket beskrivs senare i självstudien eller kontakta [Questetra BPM Suite klientstöd team](https://www.questetra.com/contact/). 
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (base64)** och spara certifikatfilen på datorn.

    ![Avsnittet för SAML-signeringscertifikat](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Knappen Spara](./media/questetra-bpm-suite-tutorial/tutorial_general_400.png)

1. På den **Questetra BPM Suite Configuration** klickar du på **konfigurera Questetra BPM Suite** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurationsavsnittet för Questetra BPM Suite](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_configure.png) 

1. I ett annat webbläsarfönster, loggar du in på ditt **Questetra BPM Suite** företagets plats som administratör.

1. Klicka på menyn längst upp **systeminställningar**. 
   
    ![Azure AD enkel inloggning][10]

1. Öppna den **SingleSignOnSAML** klickar du på **SSO (SAML)**. 
   
    ![Azure AD enkel inloggning][11]

1. På din **Questetra BPM Suite** bolaget plats, i den **SP Information** avsnittet, utför följande steg:

    a. Kopiera den **ACS URL**, och klistra in den i den **inloggning på URL: en** -textrutan i den **Questetra BPM Suite domän och URL: er** avsnittet från Azure-portalen.
    
    b. Kopiera den **entitets-ID**, och klistra in den i den **identifierare** -textrutan i den **Questetra BPM Suite domän och URL: er** avsnittet från Azure-portalen.

1. På din **Questetra BPM Suite** företagets webbplats, utför följande steg: 
   
    ![Konfigurera enkel inloggning][15]
   
    a. Välj **aktivera enkel inloggning**.
   
    b. I **entitets-ID** textrutan klistra in värdet för **SAML entitets-ID** som du har kopierat från Azure-portalen.
    
    c. I **inloggning sid-URL** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.
    
    d. I **utloggning sid-URL** textrutan klistra in värdet för **URL: en för utloggning** som du har kopierat från Azure-portalen.
    
    e. I den **NameID-format** textrutan typ `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Öppna din **Base64-** kodade certifikatet i anteckningar som hämtats från Azure-portalen, kopiera innehållet i den till Urklipp och klistra in den i den **verifiering certifikat** textrutan. 

    g. Klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/questetra-bpm-suite-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/questetra-bpm-suite-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/questetra-bpm-suite-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/questetra-bpm-suite-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-questetra-bpm-suite-test-user"></a>Skapa en testanvändare Questetra BPM Suite

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Questetra BPM Suite.

**Utför följande steg för att skapa en användare som kallas Britta Simon i Questetra BPM Suite:**

1. Logga in på webbplatsen för företagets Questetra BPM Suite som en administratör.
1. Gå till **systeminställningar > användarlistan > Ny användare**. 
1. I dialogrutan Ny användare utför du följande steg: 
   
    ![Skapa testanvändare][300] 
   
    a. I den **namn** textrutan typ **namn** användarens **britta.simon\@contoso.com**.
   
    b. I den **e-post** textrutan typ **e-post** användarens **britta.simon\@contoso.com**
   
    c. I den **lösenord** textrutan anger du ett **lösenord** för användaren.
    
    d. Klicka på **Lägg till nya användare**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Questetra BPM Suite.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Questetra BPM Suite, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Questetra BPM Suite**.

    ![Questetra BPM Suite i listan över appar](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Questetra BPM Suite i åtkomstpanelen du bör få automatiskt loggat in på ditt Questetra BPM Suite-program.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/questetra-bpm-suite-tutorial/tutorial_general_04.png
[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png

[100]: ./media/questetra-bpm-suite-tutorial/tutorial_general_100.png

[200]: ./media/questetra-bpm-suite-tutorial/tutorial_general_200.png
[201]: ./media/questetra-bpm-suite-tutorial/tutorial_general_201.png
[202]: ./media/questetra-bpm-suite-tutorial/tutorial_general_202.png
[203]: ./media/questetra-bpm-suite-tutorial/tutorial_general_203.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 

