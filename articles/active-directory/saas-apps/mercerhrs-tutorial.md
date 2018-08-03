---
title: 'Självstudier: Azure Active Directory-integrering med Mercer BenefitsCentral (av MBC) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mercer BenefitsCentral (av MBC).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3788b28c-49aa-4208-9acd-630362008e89
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: jeedes
ms.openlocfilehash: 963aff019c849b2637819296185e138d531ddef2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422543"
---
# <a name="tutorial-azure-active-directory-integration-with-mercer-benefitscentral-mbc"></a>Självstudier: Azure Active Directory-integrering med Mercer BenefitsCentral (av MBC)

I den här självstudien får du lära dig hur du integrerar Mercer BenefitsCentral (av MBC) med Azure Active Directory (AD Azure).

Integrera Mercer BenefitsCentral (av MBC) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Mercer BenefitsCentral (av MBC).
- Du kan aktivera användarna att automatiskt få loggat in till Mercer BenefitsCentral (av MBC) (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Mercer BenefitsCentral (av MBC), behöver du följande objekt:

- En Azure AD-prenumeration
- En av (MBC Mercer BenefitsCentral) enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Mercer BenefitsCentral (av MBC) från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-mercer-benefitscentral-mbc-from-the-gallery"></a>Att lägga till Mercer BenefitsCentral (av MBC) från galleriet
För att konfigurera integrering av Mercer BenefitsCentral (av MBC) till Azure AD, som du behöver lägga till Mercer BenefitsCentral (av MBC) från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Mercer BenefitsCentral (av MBC) från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Mercer BenefitsCentral (av MBC)** väljer **Mercer BenefitsCentral (av MBC)** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Mercer BenefitsCentral (av MBC) i resultatlistan](./media/mercerhrs-tutorial/tutorial_mercerhrs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Mercer BenefitsCentral (av MBC) baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Mercer BenefitsCentral (av MBC) till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Mercer BenefitsCentral (av MBC) upprättas.

I Mercer BenefitsCentral (av MBC), tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Mercer BenefitsCentral (av MBC), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Mercer BenefitsCentral (av MBC)](#create-a-mercer-benefitscentral-mbc-test-user)**  – du har en motsvarighet för Britta Simon i Mercer BenefitsCentral (av MBC) som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Mercer BenefitsCentral (av MBC).

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Mercer BenefitsCentral (av MBC):**

1. I Azure-portalen på den **Mercer BenefitsCentral (av MBC)** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/mercerhrs-tutorial/tutorial_mercerhrs_samlbase.png)

1. På den **Mercer BenefitsCentral (av MBC)-domän och URL: er** avsnittet, utför följande steg:

    ![Mercer BenefitsCentral (av MBC)-domän och URL: er med enkel inloggning för information](./media/mercerhrs-tutorial/tutorial_mercerhrs_url.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `stg.mercerhrs.com/saml2.0`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://ssous-stg.mercerhrs.com/SP2/Saml2AssertionConsumer.aspx`

    > [!NOTE] 
    > Svars-URL-värdet är inte verkliga. Uppdatera det här värdet med faktiska svars-URL. Kontakta [Mercer BenefitsCentral (av MBC) supportteamet](https://www.mercer.com/contact-us.html) att hämta det här värdet.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/mercerhrs-tutorial/tutorial_mercerhrs_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/mercerhrs-tutorial/tutorial_general_400.png)

1. På den **Mercer BenefitsCentral (av MBC) konfiguration** klickar du på **konfigurera Mercer BenefitsCentral (av MBC)** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Mercer BenefitsCentral (av MBC) konfiguration](./media/mercerhrs-tutorial/tutorial_mercerhrs_configure.png) 

1. Att konfigurera enkel inloggning på **Mercer BenefitsCentral (av MBC)** sida, som du behöver skicka de hämtade **XML-Metadata för** och **SAML enkel inloggning för tjänst-URL** till [Mercer BenefitsCentral (av MBC) supportteamet](https://www.mercer.com/contact-us.html). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/mercerhrs-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/mercerhrs-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/mercerhrs-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/mercerhrs-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-a-mercer-benefitscentral-mbc-test-user"></a>Skapa en testanvändare Mercer BenefitsCentral (av MBC)

I det här avsnittet skapar du en användare som kallas Britta Simon i Mercer timmar. Arbeta med [Mercer BenefitsCentral (av MBC) supportteamet](https://www.mercer.com/contact-us.html) att lägga till användare i Mercer tim-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att ge åtkomst till Mercer BenefitsCentral (av MBC).

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon till Mercer BenefitsCentral (av MBC), utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Mercer BenefitsCentral (av MBC)**.

    ![Länken Mercer BenefitsCentral (av MBC) i listan med program](./media/mercerhrs-tutorial/tutorial_mercerhrs_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Mercer BenefitsCentral (av MBC) i åtkomstpanelen du bör få automatiskt loggat in på programmets Mercer BenefitsCentral (av MBC).
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/mercerhrs-tutorial/tutorial_general_01.png
[2]: ./media/mercerhrs-tutorial/tutorial_general_02.png
[3]: ./media/mercerhrs-tutorial/tutorial_general_03.png
[4]: ./media/mercerhrs-tutorial/tutorial_general_04.png

[100]: ./media/mercerhrs-tutorial/tutorial_general_100.png

[200]: ./media/mercerhrs-tutorial/tutorial_general_200.png
[201]: ./media/mercerhrs-tutorial/tutorial_general_201.png
[202]: ./media/mercerhrs-tutorial/tutorial_general_202.png
[203]: ./media/mercerhrs-tutorial/tutorial_general_203.png

