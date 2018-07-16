---
title: 'Självstudier: Azure Active Directory-integration med enkel inloggning vissa Admin | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och vissa Admin SSO.
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
ms.openlocfilehash: e465518f5526dcf581afab77e30d0af33aaae88e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054047"
---
# <a name="tutorial-azure-active-directory-integration-with-certain-admin-sso"></a>Självstudier: Azure Active Directory-integrering med vissa Admin enkel inloggning

I den här självstudien får du lära dig hur du integrerar vissa Admin enkel inloggning med Azure Active Directory (AD Azure).

Integrera vissa Admin enkel inloggning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till vissa Admin SSO.
- Du kan aktivera användarna att automatiskt få loggat in på vissa Admin SSO (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med vissa Admin SSO, behöver du följande objekt:

- En Azure AD-prenumeration
- En vissa Admin SSO enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till vissa Admin SSO från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-certain-admin-sso-from-the-gallery"></a>Att lägga till vissa Admin SSO från galleriet
Om du vill konfigurera integreringen av vissa Admin enkel inloggning till Azure AD, som du behöver lägga till vissa Admin SSO från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till vissa Admin SSO från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **vissa Admin SSO**väljer **vissa Admin SSO** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Vissa Admin SSO i resultatlistan](./media/certainadminsso-tutorial/tutorial_certainadminsso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med vissa Admin SSO baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i vissa Admin enkel inloggning till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i vissa Admin SSO upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med vissa Admin enkel inloggning, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare för vissa Admin SSO](#create-a-certain-admin-sso-test-user)**  – du har en motsvarighet för Britta Simon i vissa Admin SSO som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt vissa Admin SSO-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med vissa Admin enkel inloggning:**

1. I Azure-portalen på den **vissa Admin SSO** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/certainadminsso-tutorial/tutorial_certainadminsso_samlbase.png)

3. På den **vissa Admin SSO-domän och URL: er** avsnittet, utför följande steg:

    ![Vissa Admin SSO domän och URL: er med enkel inloggning för information](./media/certainadminsso-tutorial/tutorial_certainadminsso_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<YOUR DOMAIN URL>/svcs/sso_admin_login/handleRequest/<ID>`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<SUBDOMAIN>.certain.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [vissa administratörsklient SSO-supportteamet](mailto:integrations@certain.com) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Raw)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/certainadminsso-tutorial/tutorial_certainadminsso_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/certainadminsso-tutorial/tutorial_general_400.png)

6. På den **vissa SSO Administratörskonfigurationen** klickar du på **konfigurera vissa Admin SSO** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Vissa Admin SSO-konfiguration](./media/certainadminsso-tutorial/tutorial_certainadminsso_configure.png) 

7. Att konfigurera enkel inloggning på **vissa Admin SSO** sida, som du behöver skicka de hämtade **certifikat (Raw)**, **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL**till [vissa Admin SSO-supportteamet](mailto:integrations@certain.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/certainadminsso-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/certainadminsso-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/certainadminsso-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/certainadminsso-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-certain-admin-sso-test-user"></a>Skapa en testanvändare för vissa Admin SSO

I det här avsnittet skapar du en användare som kallas Britta Simon i vissa Admin SSO. Arbeta med [vissa Admin SSO-supportteamet](mailto:integrations@certain.com) att lägga till användare i vissa Admin SSO-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till vissa Admin SSO.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon vissa Admin SSO, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **vissa Admin SSO**.

    ![Vissa Admin SSO-länk i listan med program](./media/certainadminsso-tutorial/tutorial_certainadminsso_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen vissa Admin SSO i åtkomstpanelen du bör få automatiskt loggat in på ditt vissa Admin SSO-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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

