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
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: c9229afd7bd8ebad85ce9e329fb11f992236bce0
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220109"
---
# <a name="tutorial-azure-active-directory-integration-with-secret-server-on-premises"></a>Självstudier: Azure Active Directory-integrering med hemlighet Server (lokalt)

I kursen får lära du att integrera hemlighet Server (lokalt) med Azure Active Directory (AD Azure).

Integrera hemlighet Server (lokalt) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till hemlighet Server (lokalt).
- Du kan aktivera användarna att automatiskt hämta loggat in på hemlighet Server (lokalt) (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med hemlighet Server (lokalt), behöver du följande:

- En Azure AD-prenumeration
- En hemlighet Server (lokalt) enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till hemlighet Server (lokalt) från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Att lägga till hemlighet Server (lokalt) från galleriet
Du måste lägga till hemlighet Server (lokalt) från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av hemlighet Server (lokalt) till Azure AD.

**Utför följande steg för att lägga till hemlighet Server (lokalt) från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **hemlighet Server (lokalt)** väljer **hemlighet Server (lokalt)** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Hemlig Server (lokalt) i resultatlistan](./media/secretserver-on-premises-tutorial/tutorial_secretserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med hemlighet Server (lokalt) baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i hemlighet Server (lokalt) till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i hemlighet Server (lokalt) upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med hemlighet Server (lokalt), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en hemlighet Server (lokalt) testanvändare](#create-a-secret-server-on-premises-test-user)**  – du har en motsvarighet för Britta Simon i hemlighet Server (lokalt) som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i serverprogrammet hemligheten (lokal).

**Utför följande steg för att konfigurera Azure AD enkel inloggning med hemlighet Server (lokalt):**

1. I Azure-portalen på den **hemlighet Server (lokalt)** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning dialogrutan](./media/secretserver-on-premises-tutorial/tutorial_secretserver_samlbase.png)

3. På den **hemlighet serverdomänen (lokalt) och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och hemliga Server (lokalt)-domän med enkel inloggning information](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url.png)

    a. I den **identifierare** textruta ange användaren valt värdet som ett exempel: `https://secretserveronpremises.azure`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx `

    > [!NOTE]
    > Enhets-ID som visas ovan är bara ett exempel och du kan välja ett unikt värde som identifierar din hemlighet Server-instans i Azure AD. Du måste skicka den här enhets-ID till [hemlighet Server (lokalt) Client supportteamet](https://thycotic.force.com/support/s/) och de konfigureras på sidan. Mer information finns [i den här artikeln](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och hemliga Server (lokalt)-domän med enkel inloggning information](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<SecretServerURL>/login.aspx`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska Reply URL och inloggnings-URL. Kontakta [hemlighet Server (lokalt) Client supportteamet](https://thycotic.force.com/support/s/) att hämta dessa värden.

5. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/secretserver-on-premises-tutorial/tutorial_secretserver_certificate.png)

6. Kontrollera **visa avancerade inställningar för signering av certifikat** och välj **signering alternativet** som **signera SAML-svar och assertion**.

    ![Signering alternativ](./media/secretserver-on-premises-tutorial/signing.png)

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/secretserver-on-premises-tutorial/tutorial_general_400.png)
    
8. På den **hemlighet serverkonfiguration (lokalt)** klickar du på **konfigurera hemlighet Server (lokalt)** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Hemlig serverkonfiguration (lokalt)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_configure.png)

9. Konfigurera enkel inloggning på **hemlighet Server (lokalt)** sida, måste du skicka den hämtade **Certificate(Base64) Sign-Out URL SAML enkel inloggning Tjänstwebbadress**, och **SAML entitet ID** till [hemlighet Server (lokalt) supportteamet](https://thycotic.force.com/support/s/). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/secretserver-on-premises-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/secretserver-on-premises-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/secretserver-on-premises-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/secretserver-on-premises-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-secret-server-on-premises-test-user"></a>Skapa en testanvändare hemlighet Server (lokalt)

I det här avsnittet skapar du en användare som kallas Britta Simon i hemlighet Server (lokalt). Arbeta med [hemlighet Server (lokalt) supportteamet](https://thycotic.force.com/support/s/) att lägga till användare i hemlighet Server (lokalt)-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till hemlighet Server (lokalt).

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon hemlighet Server (lokalt), utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201]

2. Välj i listan med program **hemlighet Server (lokalt)**.

    ![Länken hemlighet Server (lokalt) i listan med program](./media/secretserver-on-premises-tutorial/tutorial_secretserver_app.png)

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen hemlighet Server (lokalt) på åtkomstpanelen du bör få automatiskt loggat in på serverprogrammet hemligheten (lokal).
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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

