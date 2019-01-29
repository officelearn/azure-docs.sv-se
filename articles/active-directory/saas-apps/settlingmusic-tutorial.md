---
title: 'Självstudier: Azure Active Directory-integrering med reglera musik | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Settling musik.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6f86a8a2-4bd0-40cc-b1b4-752fce123328
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: jeedes
ms.openlocfilehash: 900254e42410aafa0d8e58048c7d88efa94dfed5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178553"
---
# <a name="tutorial-azure-active-directory-integration-with-settling-music"></a>Självstudier: Azure Active Directory-integrering med reglera musik

I den här självstudien får du lära dig hur du integrerar Settling musik med Azure Active Directory (AD Azure).

Integrera Settling musik med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till att reglera musik.
- Du kan aktivera användarna att automatiskt få loggat in till att reglera musik (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med reglera musik, behöver du följande objekt:

- En Azure AD-prenumeration
- En Settling musik enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägga till Settling musik från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-settling-music-from-the-gallery"></a>Lägga till Settling musik från galleriet
För att konfigurera integrering av Settling musik i Azure AD, som du behöver lägga till Settling musik från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Settling musik från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **reglera musik**väljer **reglera musik** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Reglera musik i resultatlistan](./media/settlingmusic-tutorial/tutorial_settlingmusic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med reglera musik baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Settling musik är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i reglera musik upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Settling musik, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare för Settling musik](#create-a-settling-music-test-user)**  – du har en motsvarighet för Britta Simon i reglera musik som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Settling musik program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med reglera musik:**

1. I Azure-portalen på den **reglera musik** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/settlingmusic-tutorial/tutorial_settlingmusic_samlbase.png)

1. På den **reglera musik domän och URL: er** avsnittet, utför följande steg:

    ![Reglera musik domän och URL: er enkel inloggning för information](./media/settlingmusic-tutorial/tutorial_settlingmusic_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [reglera musik klienten supportteamet](https://rakurakuseisan.jp/) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/settlingmusic-tutorial/tutorial_settlingmusic_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/settlingmusic-tutorial/tutorial_general_400.png)

1. På den **reglera musik Configuration** klickar du på **konfigurera reglera musik** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Reglera musik konfiguration](./media/settlingmusic-tutorial/tutorial_settlingmusic_configure.png) 

1. I ett annat webbläsarfönster, logga in till att reglera musik som en administratör.

1. På sidan klickar du på **management** fliken.

    ![Reglera musik steg 1](./media/settlingmusic-tutorial/tutorial_settlingmusic_step1.png)

1. Klicka på **systeminställningen** fliken.

    ![Reglera musik steg 2](./media/settlingmusic-tutorial/tutorial_settlingmusic_step2.png)

1. Växla till **Security** fliken.

    ![Reglera musik steg 3](./media/settlingmusic-tutorial/tutorial_settlingmusic_step3.png)

1. På den **enkel inloggning inställningen** avsnittet, utför följande steg:

    ![Reglera musik step5](./media/settlingmusic-tutorial/tutorial_settlingmusic_step4.png)

    a. Klicka på **att aktivera**.

    b. I den **inloggnings-URL för ID-provider** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    c. I den **utloggnings-URL för ID** textrutan klistra in värdet för **URL: en för utloggning** som du har kopierat från Azure-portalen.

    d. Klicka på **Välj fil** att ladda upp den **certifikat (Base64)** som du har hämtat utgör Azure-portalen.

    e. Klicka på knappen **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/settlingmusic-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/settlingmusic-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/settlingmusic-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/settlingmusic-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-settling-music-test-user"></a>Skapa en testanvändare för Settling musik

I det här avsnittet skapar du en användare som kallas Britta Simon i Settling musik. Arbeta med [reglera musik klienten supportteamet](https://rakurakuseisan.jp/) att lägga till användare i Settling musik-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att ge åtkomst till att reglera musik.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Settling musik, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **reglera musik**.

    ![Länken Settling musik i listan med program](./media/settlingmusic-tutorial/tutorial_settlingmusic_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Settling musik i åtkomstpanelen du bör få automatiskt loggat in på ditt Settling musik program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/settlingmusic-tutorial/tutorial_general_01.png
[2]: ./media/settlingmusic-tutorial/tutorial_general_02.png
[3]: ./media/settlingmusic-tutorial/tutorial_general_03.png
[4]: ./media/settlingmusic-tutorial/tutorial_general_04.png

[100]: ./media/settlingmusic-tutorial/tutorial_general_100.png

[200]: ./media/settlingmusic-tutorial/tutorial_general_200.png
[201]: ./media/settlingmusic-tutorial/tutorial_general_201.png
[202]: ./media/settlingmusic-tutorial/tutorial_general_202.png
[203]: ./media/settlingmusic-tutorial/tutorial_general_203.png

