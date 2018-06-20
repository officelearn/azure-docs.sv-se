---
title: 'Självstudier: Azure Active Directory-integrering med Flock | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Flock.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7b2c3ac5-17f1-49a0-8961-c541b258d4b1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 633828cc7e244dca1e43c45852910211aeda68d9
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36229503"
---
# <a name="tutorial-azure-active-directory-integration-with-flock"></a>Självstudier: Azure Active Directory-integrering med Flock

I kursen får lära du att integrera Flock med Azure Active Directory (AD Azure).

Integrerande Flock med Azure AD ger följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Flock.
- Du kan aktivera användarna att automatiskt hämta loggat in på Flock (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Flock, behöver du följande:

- En Azure AD-prenumeration
- En Flock enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Flock från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-flock-from-the-gallery"></a>Att lägga till Flock från galleriet
Du måste lägga till Flock från galleriet i listan över hanterade SaaS-appar för att konfigurera Flock till Azure AD-integrering.

**Utför följande steg för att lägga till Flock från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Flock**väljer **Flock** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Flock i resultatlistan](./media/flock-tutorial/tutorial_flock_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med Flock baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Flock motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Flock upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Flock, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Flock](#create-a-flock-test-user)**  – har en motsvarighet för Britta Simon Flock som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Flock.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Flock:**

1. I Azure-portalen på den **Flock** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning dialogrutan](./media/flock-tutorial/tutorial_flock_samlbase.png)

3. På den **Flock domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och flock domän med enkel inloggning information](./media/flock-tutorial/tutorial_flock_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.flock.com/`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<subdomain>.flock.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Flock klienten supportteamet](mailto:support@flock.com) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/flock-tutorial/tutorial_flock_certificate.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/flock-tutorial/tutorial_general_400.png)

6. På den **Flock Configuration** klickar du på **konfigurera Flock** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Flock konfiguration](./media/flock-tutorial/tutorial_flock_configure.png) 

7. I en annan webbläsarfönster loggar du in på webbplatsen Flock företag som administratör.

8. Välj **autentisering** fliken navigeringsrutan till vänster och välj sedan **SAML-autentisering**.

    ![Flock konfiguration](./media/flock-tutorial/configure1.png)

9. I den **SAML-autentisering** avsnittet, utför följande steg:

    ![Flock konfiguration](./media/flock-tutorial/configure2.png)

    a. I den **SAML 2.0 Endpoint(HTTP)** textruta klistra in **SAML inloggning tjänst-URL för enkel** värde som du har kopierat från Azure-portalen.

    b. I den **identitet providern utfärdaren** textruta klistra in **SAML enhets-ID** värde som du har kopierat från Azure-portalen.

    c. Öppna den hämtade **Certificate(Base64)** från Azure-portalen i anteckningar klistra in innehållet i den **certifikat med offentlig** textruta.

    d. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/flock-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/flock-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/flock-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/flock-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.

### <a name="create-a-flock-test-user"></a>Skapa en Flock testanvändare

Om du vill aktivera Azure AD-användare kan logga in på Flock etableras de i Flock. När det gäller besättning är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på webbplatsen Flock företag som administratör.

2. Klicka på **hantera Team** från den vänstra navigeringsfönstret.

    ![Lägga till medarbetare](./media/flock-tutorial/user1.png)

3. Klicka på **Lägg till medlem** och markera sedan **gruppmedlemmar**.

    ![Lägga till medarbetare](./media/flock-tutorial/user2.png)

4. Ange e-postadressen för användaren som **Brittasimon@contoso.com** och välj sedan **Lägg till användare**.

    ![Lägga till medarbetare](./media/flock-tutorial/user3.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Flock.

![Tilldela rollen][200]

**Om du vill tilldela Flock Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201]

2. Välj i listan med program **Flock**.

    ![Länken Flock i listan med program](./media/flock-tutorial/tutorial_flock_app.png)

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Flock på åtkomstpanelen du ska hämta automatiskt loggat in i tillämpningsprogrammet Flock.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/flock-tutorial/tutorial_general_01.png
[2]: ./media/flock-tutorial/tutorial_general_02.png
[3]: ./media/flock-tutorial/tutorial_general_03.png
[4]: ./media/flock-tutorial/tutorial_general_04.png

[100]: ./media/flock-tutorial/tutorial_general_100.png

[200]: ./media/flock-tutorial/tutorial_general_200.png
[201]: ./media/flock-tutorial/tutorial_general_201.png
[202]: ./media/flock-tutorial/tutorial_general_202.png
[203]: ./media/flock-tutorial/tutorial_general_203.png
