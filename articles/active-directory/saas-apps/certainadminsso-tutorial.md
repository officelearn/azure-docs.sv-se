---
title: 'Självstudier: Azure Active Directory-integrering med enkel inloggning vissa Admin | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och vissa Admin enkel inloggning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 98ba0174-be02-408a-8634-c8113b12dedb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeedes
ms.openlocfilehash: cf23620eb3a3b0a9cb8e5877f517281a9c21809c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320620"
---
# <a name="tutorial-azure-active-directory-integration-with-certain-admin-sso"></a>Självstudier: Azure Active Directory-integrering med vissa Admin enkel inloggning

I kursen får lära du att integrera vissa Admin enkel inloggning med Azure Active Directory (AD Azure).

Integrera vissa Admin enkel inloggning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till vissa Admin enkel inloggning.
- Du kan aktivera användarna att automatiskt hämta loggat in på vissa Admin SSO (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med vissa Admin enkel inloggning, behöver du följande:

- En Azure AD-prenumeration
- En vissa Admin SSO enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till vissa Admin SSO från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-certain-admin-sso-from-the-gallery"></a>Att lägga till vissa Admin SSO från galleriet
Du måste lägga till vissa Admin SSO från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av vissa Admin SSO i Azure AD.

**Utför följande steg för att lägga till vissa Admin SSO från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **vissa Admin SSO**väljer **vissa Admin SSO** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Vissa Admin SSO i resultatlistan](./media/certainadminsso-tutorial/tutorial_certainadminsso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med vissa Admin SSO baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren vissa Admin SSO till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren vissa Admin SSO upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med vissa Admin enkel inloggning måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare vissa Admin SSO](#create-a-certain-admin-sso-test-user)**  – du har en motsvarighet för Britta Simon vissa Admin SSO som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i vissa Admin SSO-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med vissa Admin enkel inloggning:**

1. I Azure-portalen på den **vissa Admin SSO** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/certainadminsso-tutorial/tutorial_certainadminsso_samlbase.png)

3. På den **vissa Admin SSO-domänen och URL: er** avsnittet, utför följande steg:

    ![Vissa Admin-domänen för enkel inloggning och URL: er med enkel inloggning information](./media/certainadminsso-tutorial/tutorial_certainadminsso_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<YOUR DOMAIN URL>/svcs/sso_admin_login/handleRequest/<ID>`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<SUBDOMAIN>.certain.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [vissa Admin-klientfilerna supportteamet](mailto:integrations@certain.com) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Raw)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/certainadminsso-tutorial/tutorial_certainadminsso_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/certainadminsso-tutorial/tutorial_general_400.png)

6. På den **vissa SSO Administratörskonfigurationen** klickar du på **konfigurera vissa Admin SSO** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Vissa Admin SSO-konfiguration](./media/certainadminsso-tutorial/tutorial_certainadminsso_configure.png) 

7. Konfigurera enkel inloggning på **vissa Admin SSO** sida, måste du skicka den hämtade **certifikat (Raw)**, **Sign-Out URL, SAML enhets-ID och SAML inloggning tjänst-URL för enkel**till [vissa Admin SSO supportteamet](mailto:integrations@certain.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/certainadminsso-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/certainadminsso-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/certainadminsso-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/certainadminsso-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-certain-admin-sso-test-user"></a>Skapa en testanvändare vissa Admin-SSO

I det här avsnittet skapar du en användare som kallas Britta Simon vissa Admin sso. Arbeta med [vissa Admin SSO supportteamet](mailto:integrations@certain.com) att lägga till användare i vissa Admin SSO-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till vissa Admin enkel inloggning.

![Tilldela rollen][200] 

**Om du vill tilldela vissa Admin SSO Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **vissa Admin SSO**.

    ![Vissa Admin SSO-länken i listan med program](./media/certainadminsso-tutorial/tutorial_certainadminsso_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på vissa Admin SSO-panelen på åtkomstpanelen du bör få automatiskt loggat in på vissa Admin SSO-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/certainadminsso-tutorial/tutorial_general_01.png
[2]: ./media/certainadminsso-tutorial/tutorial_general_02.png
[3]: ./media/certainadminsso-tutorial/tutorial_general_03.png
[4]: ./media/certainadminsso-tutorial/tutorial_general_04.png

[100]: ./media/certainadminsso-tutorial/tutorial_general_100.png

[200]: ./media/certainadminsso-tutorial/tutorial_general_200.png
[201]: ./media/certainadminsso-tutorial/tutorial_general_201.png
[202]: ./media/certainadminsso-tutorial/tutorial_general_202.png
[203]: ./media/certainadminsso-tutorial/tutorial_general_203.png

