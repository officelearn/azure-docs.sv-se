---
title: 'Självstudier: Azure Active Directory-integration med Yonyx interaktiva guider | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Yonyx interaktiva guider.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 07db4e01-319b-4cb6-9b93-4577bffd3cbc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2017
ms.author: jeedes
ms.openlocfilehash: a7166ac295c8bac3c7bb8d2e053a6f49fe533cc8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448958"
---
# <a name="tutorial-azure-active-directory-integration-with-yonyx-interactive-guides"></a>Självstudier: Azure Active Directory-integration med Yonyx interaktiva guider

I den här självstudien får du lära dig hur du integrerar Yonyx interaktiva guider med Azure Active Directory (AD Azure).

Integrera Yonyx interaktiva guider med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Yonyx interaktiva guider
- Du kan aktivera användarna att automatiskt få loggat in på Yonyx interaktiva guider (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Yonyx interaktiva guider, behöver du följande objekt:

- En Azure AD-prenumeration
- En Yonyx interaktiva guider enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till interaktiva guider för Yonyx från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-yonyx-interactive-guides-from-the-gallery"></a>Att lägga till interaktiva guider för Yonyx från galleriet
För att konfigurera integrering av Yonyx interaktiva guider i Azure AD, som du behöver lägga till interaktiva guider för Yonyx från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till interaktiva guider för Yonyx från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Yonyx interaktiva guider**väljer **Yonyx interaktiva guider** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Yonyx interaktiva guider i resultatlistan](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Yonyx interaktiva guider baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Yonyx interaktiva guider är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Yonyx interaktiva guider upprättas.

I Yonyx interaktiva guider, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Yonyx interaktiva guider, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Yonyx interaktiva guider](#create-a-yonyx-interactive-guides-test-user)**  – du har en motsvarighet för Britta Simon i Yonyx interaktiva guider som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Yonyx interaktiva guider.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Yonyx interaktiva guider:**

1. I Azure-portalen på den **Yonyx interaktiva guider** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_samlbase.png)

1. På den **Yonyx interaktiva guider domän och URL: er** avsnittet, utför följande steg:

    ![Yonyx interaktiva guider domän och URL: er med enkel inloggning för information](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<company name>.yonyx.com/y/conversation/?id=<guid number>`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<company name>.yonyx.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [Yonyx interaktiva guider klienten supportteamet](mailto:support@yonyx.com) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/yonyx-tutorial/tutorial_general_400.png)

1. På den **Yonyx interaktiva guider Configuration** klickar du på **konfigurera Yonyx interaktiva guider** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Yonyx interaktiva guider konfiguration](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_configure.png) 

1. Att konfigurera enkel inloggning på **Yonyx interaktiva guider** sida, som du behöver skicka de hämtade **Certificate(Base64)**, **URL: en för utloggning**, **SAML enkel Inloggnings-tjänstens URL** **SAML entitets-ID** till [Yonyx interaktiva guider supportteam](mailto:support@yonyx.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

  ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](./media/yonyx-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![”Användare och grupper” och ”alla användare”-länkar](./media/yonyx-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Knappen Lägg till](./media/yonyx-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan användare](./media/yonyx-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-yonyx-interactive-guides-test-user"></a>Skapa en testanvändare Yonyx interaktiva guider

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Yonyx interaktiva handböcker. Yonyx interaktiva guider stöder just-in-time-etablering, vilket är som standard aktiverat.

Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Yonyx interaktiva guider om det inte finns ännu.

>[!NOTE]
>Om du vill skapa en användare manuellt kan du behöva kontakta supportteamet Yonyx interaktiva guider via <mailto:support@yonyx.com>. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Yonyx interaktiva guider.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon Yonyx interaktiva guider, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Yonyx interaktiva guider**.

    ![Länken Yonyx interaktiva guider i listan med program](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Yonyx interaktiva guider i åtkomstpanelen du bör få automatiskt loggat in på programmets Yonyx interaktiva guider.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/yonyx-tutorial/tutorial_general_01.png
[2]: ./media/yonyx-tutorial/tutorial_general_02.png
[3]: ./media/yonyx-tutorial/tutorial_general_03.png
[4]: ./media/yonyx-tutorial/tutorial_general_04.png

[100]: ./media/yonyx-tutorial/tutorial_general_100.png

[200]: ./media/yonyx-tutorial/tutorial_general_200.png
[201]: ./media/yonyx-tutorial/tutorial_general_201.png
[202]: ./media/yonyx-tutorial/tutorial_general_202.png
[203]: ./media/yonyx-tutorial/tutorial_general_203.png

