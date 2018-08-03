---
title: 'Självstudier: Azure Active Directory-integration med Ceridian Dayforce HCM | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Ceridian Dayforce HCM-system.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 7adf1eb3-d063-45d6-96a8-fd53b329b3f3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 41ec8d2c900367a8ae3d1dde67ff0198996c9ed1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443502"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Självstudier: Azure Active Directory-integration med Ceridian Dayforce HCM-system

I den här självstudien får du lära dig hur du integrerar Ceridian Dayforce HCM-system med Azure Active Directory (AD Azure).

Integrera Ceridian Dayforce HCM-system med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Ceridian Dayforce HCM-system.
- Du kan aktivera användarna att automatiskt få loggat in på Ceridian Dayforce HCM-system (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Ceridian Dayforce HCM, behöver du följande objekt:

- En Azure AD-prenumeration
- En Ceridian Dayforce HCM enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Ceridian Dayforce HCM-system från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Att lägga till Ceridian Dayforce HCM-system från galleriet
Om du vill konfigurera integreringen av Ceridian Dayforce HCM-system till Azure AD, som du behöver lägga till Ceridian Dayforce HCM-system från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Ceridian Dayforce HCM-system från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Ceridian Dayforce HCM**väljer **Ceridian Dayforce HCM** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Ceridian Dayforce HCM i resultatlistan](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Ceridian Dayforce HCM-system baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Ceridian Dayforce HCM-system är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Ceridian Dayforce HCM upprättas.

I Ceridian Dayforce HCM-system, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Ceridian Dayforce HCM-system, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Ceridian Dayforce HCM](#create-a-ceridian-dayforce-hcm-test-user)**  – du har en motsvarighet för Britta Simon i Ceridian Dayforce HCM-system som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Ceridian Dayforce HCM-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Ceridian Dayforce HCM:**

1. I Azure-portalen på den **Ceridian Dayforce HCM** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_samlbase.png)

1. På den **Ceridian Dayforce HCM domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_url.png)
    
    a. I den **inloggning på URL: en** textrutan, skriver du in URL: en används av användarna logga in till ditt Ceridian Dayforce HCM-program.
    
    | Miljö | URL |
    | :-- | :-- |
    | För produktion | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | För testning | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |
    
    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster:
    
    | Miljö | URL |
    | :-- | :-- |
    | För produktion | `https://ncpingfederate.dayforcehcm.com/sp` |
    | För testning | `https://fs-test.dayforcehcm.com/sp` |
    
    c. I den **svars-URL** textrutan, skriver du in URL: en används av Azure AD för att publicera svaret.
    
    | Miljö | URL |
    | :-- | :-- |
    | För produktion | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | För testning | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |
    
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska identifierare, svars-URL och inloggnings-URL. Kontakta [Ceridian Dayforce HCM klienten supportteamet](https://www.ceridian.com/contact-us/index.html) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_certificate.png) 

1. Programmets Ceridian Dayforce HCM förväntar sig SAML-intyg i ett visst format. Arbeta med [Ceridian Dayforce HCM-supportteamet](https://www.ceridian.com/contact-us/index.html) först för att identifiera rätt användar-ID. Microsoft rekommenderar att du använder den **”name”** attribut som användaridentifierare. Du kan hantera värdena för dessa attribut från den **användarattribut** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för detta.  

    ![Konfigurera enkel inloggning](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_07.png)

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Attributnamn  | Attributvärde |
    | --------------- | -------------------- |    
    | namn  | User.extensionattribute2 |    

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/ceridiandayforcehcm-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/ceridiandayforcehcm-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. I den **värdet** väljer du det användarattribut som du vill använda för din implementering.
    Till exempel om du vill använda EmployeeID som unika användare identifierare och du har lagrat attributvärdet i ExtensionAttribute2, välj sedan **user.extensionattribute2**.
    
    d. Klicka på **OK**.

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/ceridiandayforcehcm-tutorial/tutorial_general_400.png)
    
1. På den **Ceridian Dayforce HCM Configuration** klickar du på **konfigurera Ceridian Dayforce HCM** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Ceridian Dayforce HCM-konfiguration](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_configure.png) 

1. Att konfigurera enkel inloggning på **Ceridian Dayforce HCM** sida, som du behöver skicka de hämtade **XML-Metadata för** och **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** till [Ceridian Dayforce HCM-supportteamet](https://www.ceridian.com/contact-us/index.html).

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/ceridiandayforcehcm-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/ceridiandayforcehcm-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/ceridiandayforcehcm-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/ceridiandayforcehcm-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-ceridian-dayforce-hcm-test-user"></a>Skapa en Ceridian Dayforce HCM-testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Ceridian Dayforce HCM-system. Arbeta med den [Ceridian Dayforce HCM-supportteamet](https://www.ceridian.com/contact-us/index.html) att hämta användare har lagts till i Ceridian Dayforce HCM-programmet. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Ceridian Dayforce HCM-system.

![Tilldela användare][200] 

**Om du vill tilldela Ceridian Dayforce HCM Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Ceridian Dayforce HCM**.

    ![Konfigurera enkel inloggning](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Ceridian Dayforce HCM-system.

![Tilldela rollen][200] 

**Om du vill tilldela Ceridian Dayforce HCM Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Ceridian Dayforce HCM**.

    ![Ceridian Dayforce HCM-länk i listan med program](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.  
När du klickar på panelen Ceridian Dayforce HCM i åtkomstpanelen du bör få automatiskt loggat in på ditt Ceridian Dayforce HCM-program. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_01.png
[2]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_02.png
[3]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_03.png
[4]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_04.png

[100]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_100.png

[200]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_200.png
[201]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_201.png
[202]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_202.png
[203]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_203.png

