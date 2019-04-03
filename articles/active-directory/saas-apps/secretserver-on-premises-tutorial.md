---
title: 'Självstudier: Azure Active Directory-integrering med hemlighet Server (lokalt) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och hemlighet Server (lokalt).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9167a5ed72e6fec2ca03cc97d1d41dd6cd4aaba6
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885850"
---
# <a name="tutorial-azure-active-directory-integration-with-secret-server-on-premises"></a>Självstudier: Azure Active Directory-integrering med hemlighet Server (lokalt)

Lär dig hur du integrerar hemlighet Server (lokalt) med Azure Active Directory (AD Azure) i den här självstudien.

Integrera hemlighet Server (lokalt) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till hemlighet Server (lokalt).
- Du kan aktivera användarna att automatiskt få loggat in på hemlighet Server (lokalt) (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med hemlighet Server (lokalt), behöver du följande objekt:

- En Azure AD-prenumeration
- En hemlighet Server (lokalt) enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till hemlighet Server (lokalt) från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Att lägga till hemlighet Server (lokalt) från galleriet
Om du vill konfigurera integreringen av hemlighet Server (lokalt) till Azure AD, som du behöver lägga till hemlighet Server (lokalt) från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till hemlighet Server (lokalt) från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **hemlighet Server (lokalt)** väljer **hemlighet Server (lokalt)** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Hemlig Server (lokalt) i listan med resultat](./media/secretserver-on-premises-tutorial/tutorial_secretserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med hemlighet Server (lokalt) baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i hemlighet Server (lokalt) till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i hemlighet Server (lokalt) upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med hemlighet Server (lokalt), måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en hemlighet Server (lokalt) testanvändare](#create-a-secret-server-on-premises-test-user)**  – du har en motsvarighet för Britta Simon i hemlighet Server (lokalt) som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktiverar Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i serverprogrammet hemlighet (lokalt).

**Utför följande steg för att konfigurera Azure AD enkel inloggning med hemlighet Server (lokalt):**

1. I Azure-portalen på den **hemlighet Server (lokalt)** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/secretserver-on-premises-tutorial/tutorial_secretserver_samlbase.png)

1. På den **hemlighet Server (lokalt)-domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Hemlig Server (lokalt)-domän och URL: er med enkel inloggning för information](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url.png)

    a. I den **identifierare** textrutan Ange användaren valt värdet som ett exempel: `https://secretserveronpremises.azure`

    b. I textrutan **Svars-URL** anger du en URL med följande mönster: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > Entitets-ID som visas ovan är bara ett exempel och du kan välja ett unikt värde som identifierar din hemlighet Server-instans i Azure AD. Du behöver skicka den här entitets-ID till [hemlighet Server (lokalt) Client supportteamet](https://thycotic.force.com/support/s/) och de konfigureras på sidan. Mer information hittar du [i den här artikeln](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Hemlig Server (lokalt)-domän och URL: er med enkel inloggning för information](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<SecretServerURL>/login.aspx`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Kontakta [hemlighet Server (lokalt) Client supportteamet](https://thycotic.force.com/support/s/) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/secretserver-on-premises-tutorial/tutorial_secretserver_certificate.png)

1. Kontrollera **visa avancerade inställningar för signering av certifikat** och välj **signering alternativet** som **inloggning SAML-svar och försäkran**.

    ![Signeringsalternativ](./media/secretserver-on-premises-tutorial/signing.png)

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning – knappen Spara](./media/secretserver-on-premises-tutorial/tutorial_general_400.png)
    
1. På den **hemlighet serverkonfiguration (lokalt)** klickar du på **konfigurera hemlighet Server (lokalt)** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfiguration av hemliga Server (lokalt)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_configure.png)

1. Att konfigurera enkel inloggning på **hemlighet Server (lokalt)** sida, som du behöver skicka de hämtade **Certificate(Base64) URL för utloggning, enkel inloggning Service URL för SAML**, och **SAML-entitet ID** till [hemlighet Server (lokalt) supportteamet](https://thycotic.force.com/support/s/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/secretserver-on-premises-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/secretserver-on-premises-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/secretserver-on-premises-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/secretserver-on-premises-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-secret-server-on-premises-test-user"></a>Skapa en testanvändare hemlighet Server (lokalt)

I det här avsnittet skapar du en användare som kallas Britta Simon i hemlighet Server (lokalt). Arbeta med [hemlighet Server (lokalt) supportteamet](https://thycotic.force.com/support/s/) att lägga till användare i den hemlighet Server (lokalt)-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till hemlighet Server (lokalt).

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon hemlighet Server (lokalt), utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

1. I listan med program väljer **hemlighet Server (lokalt)**.

    ![Länken hemlighet Server (lokalt) i listan med program](./media/secretserver-on-premises-tutorial/tutorial_secretserver_app.png)

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på knappen **Lägg till**. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen hemlighet Server (lokalt) i åtkomstpanelen du bör få automatiskt loggat in på serverprogrammet hemlighet (lokalt).
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/secretserver-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/secretserver-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/secretserver-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/secretserver-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/secretserver-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/secretserver-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/secretserver-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/secretserver-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/secretserver-on-premises-tutorial/tutorial_general_203.png

