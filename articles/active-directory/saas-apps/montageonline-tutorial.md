---
title: 'Självstudier: Azure Active Directory-integrering med Montage Online | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Montage Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5c5e8c6f-e4fb-43fe-8841-e371f568ebed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: 52b89fd830d18c595e29549432514fd60bbc6df9
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158714"
---
# <a name="tutorial-azure-active-directory-integration-with-montage-online"></a>Självstudier: Azure Active Directory-integrering med Montage Online

I den här självstudien får du lära dig hur du integrerar Montage Online med Azure Active Directory (AD Azure).

Integrera Montage Online med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Montage Online.
- Du kan aktivera användarna att automatiskt få loggat in på Montage Online (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Montage Online, behöver du följande objekt:

- En Azure AD-prenumeration
- Ett Montage Online enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Montage Online från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-montage-online-from-the-gallery"></a>Att lägga till Montage Online från galleriet
För att konfigurera integrering av Montage Online i Azure AD, som du behöver lägga till Montage Online från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till Montage Online från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Montage Online**väljer **Montage Online** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Montage Online i resultatlistan](./media/montageonline-tutorial/tutorial_montageonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Montage Online baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Montage Online är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Montage Online upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Montage Online, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en Montage Online testanvändare](#create-a-montage-online-test-user)**  – du har en motsvarighet för Britta Simon i Montage Online som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktiverar Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i programmets Montage Online.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Montage Online:**

1. I Azure-portalen på den **Montage Online** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/montageonline-tutorial/tutorial_montageonline_samlbase.png)

1. På den **Montage Online domän och URL: er** avsnittet, utför följande steg:

    ![Montage Online domän och URL: er med enkel inloggning för information](./media/montageonline-tutorial/tutorial_montageonline_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster:

    För Produktionsmiljö: `https://<subdomain>.montageonline.co.nz/`

    För testmiljön: `https://build-<subdomain>.montageonline.co.nz/`

    b. I den **identifierare** textrutan anger du ett URL:

    För Produktionsmiljö: `MOL_Azure`

    För testmiljön: `MOL_Azure_Build`

    > [!NOTE] 
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Montage Online klienten supportteamet](https://www.montage.co.nz/contact-us/) att hämta värdet. 
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/montageonline-tutorial/tutorial_montageonline_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/montageonline-tutorial/tutorial_general_400.png)

1. På den **Montage Online Configuration** klickar du på **konfigurera Montage Online** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Montage Online konfiguration](./media/montageonline-tutorial/tutorial_montageonline_configure.png) 

1. Att konfigurera enkel inloggning på **Montage Online** sida, som du behöver skicka de hämtade **certifikat (Base64), URL: en för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** till [Montage Online-supportteamet](https://www.montage.co.nz/contact-us/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/montageonline-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/montageonline-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/montageonline-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/montageonline-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-montage-online-test-user"></a>Skapa en Montage Online testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Montage Online. Arbeta med [Montage Online supportteamet](https://www.montage.co.nz/contact-us/) att lägga till användare i Montage Online-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Montage Online.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Montage Online, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Montage Online**.

    ![Länken Montage Online i listan med program](./media/montageonline-tutorial/tutorial_montageonline_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Montage Online i åtkomstpanelen du bör få automatiskt loggat in på programmets Montage Online.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/montageonline-tutorial/tutorial_general_01.png
[2]: ./media/montageonline-tutorial/tutorial_general_02.png
[3]: ./media/montageonline-tutorial/tutorial_general_03.png
[4]: ./media/montageonline-tutorial/tutorial_general_04.png

[100]: ./media/montageonline-tutorial/tutorial_general_100.png

[200]: ./media/montageonline-tutorial/tutorial_general_200.png
[201]: ./media/montageonline-tutorial/tutorial_general_201.png
[202]: ./media/montageonline-tutorial/tutorial_general_202.png
[203]: ./media/montageonline-tutorial/tutorial_general_203.png

