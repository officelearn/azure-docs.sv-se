---
title: 'Självstudier: Azure Active Directory-integrering med SAML 1.1 Token aktiverat LOB-appen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML 1.1 Token aktiverat LOB-App.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ced1d88d-0e48-40d5-9aea-ef991cd9d270
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: dc07dce24152f9f58253ad96c80f6b004cd1198b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050791"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Självstudier: Azure Active Directory-integrering med SAML 1.1 Token aktiverat LOB-App

I den här självstudien får du lära dig hur du integrerar SAML 1.1 Token-aktiverade LOB-App med Azure Active Directory (AD Azure).

Integrera SAML 1.1 Token ger aktiverade LOB-App med Azure AD dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SAML 1.1 Token aktiverat LOB-App.
- Du kan ge användarna får automatiskt loggat in på SAML 1.1 Token aktiverat LOB-App (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Att konfigurera Azure AD-integrering med SAML 1.1 Token aktiverad LOB-App, behöver du följande objekt:

- En Azure AD-prenumeration
- En SAML 1.1-Token aktiverat LOB-App enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till SAML 1.1 Token aktiverat LOB-App från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>Att lägga till SAML 1.1 Token aktiverat LOB-App från galleriet
Om du vill konfigurera integreringen av SAML 1.1 Token aktiverad LOB-App till Azure AD, du måste lägga till SAML 1.1 Token-aktiverade LOB-App från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till SAML 1.1 Token aktiverat LOB-App från galleriet, utför du följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SAML 1.1 Token aktiverat LOB-App**väljer **SAML 1.1 Token aktiverat LOB-App** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![SAML 1.1 Token aktiverat LOB-App i listan med resultat](./media/saml-tutorial/tutorial_saml_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SAML 1.1 Token aktiverade LOB-App utifrån en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vilka motsvarande användaren i SAML 1.1 Token aktiverade LOB-appen är att en användare i Azure AD. Med andra ord aktiverat en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SAML 1.1 Token LOB-App behöver etableras.

Konfigurera och testa Azure AD enkel inloggning med SAML 1.1 Token aktiverad LOB-App, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en SAML 1.1-Token aktiverat LOB-App testanvändare](#create-a-saml-11-token-enabled-lob-app-test-user)**  – du har en motsvarighet för Britta Simon i SAML 1.1 Token aktiverat LOB-App som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din SAML 1.1 Token-aktiverat program för LOB-App.

**Om du vill konfigurera Azure AD enkel inloggning med SAML 1.1 Token aktiverad LOB-App, utför följande steg:**

1. I Azure-portalen på den **SAML 1.1 Token aktiverat LOB-App** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/saml-tutorial/tutorial_saml_samlbase.png)

3. På den **SAML 1.1 Token aktiverat LOB-App-domän och URL: er** avsnittet, utför följande steg:

    ![SAML 1.1 Token aktiverat LOB-App-domän och URL: er enkel inloggning för information](./media/saml-tutorial/tutorial_saml_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://your-app-url`

    b. I den **identifierare (entitets-ID)** textrutan anger du ett URL med hjälp av följande mönster: `https://your-app-url`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Ersätt värdena med specifika URL: er.  

5. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/saml-tutorial/tutorial_saml_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/saml-tutorial/tutorial_general_400.png)
    
7. På den **SAML 1.1 Token aktiverat LOB-Appkonfiguration** klickar du på **konfigurera SAML 1.1 Token aktiverat LOB-App** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![SAML 1.1 Token aktiverat LOB-Appkonfiguration](./media/saml-tutorial/tutorial_saml_configure.png) 

8. Att konfigurera enkel inloggning på **SAML 1.1 Token aktiverat LOB-App** sida, som du behöver skicka de hämtade **certifikat (Base64), URL: en för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** till Application support-teamet. De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/saml-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/saml-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/saml-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/saml-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-saml-11-token-enabled-lob-app-test-user"></a>Skapa en SAML 1.1-Token aktiverat testanvändare för LOB-App

I det här avsnittet skapar du en användare som kallas Britta Simon SAML 1.1 Token aktiverat LOB-App. Arbeta med program stöder teamet för att skapa användare på programsidan. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera Britta Simon du använder Azure för enkel inloggning genom att bevilja åtkomst till SAML 1.1 Token-aktiverade LOB-App.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon SAML 1.1 Token aktiverad LOB-App, utför följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **SAML 1.1 Token aktiverat LOB-App**.

    ![SAML 1.1-Token aktiverat LOB-App-länk i listan med program](./media/saml-tutorial/tutorial_saml_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på SAML 1.1-Token aktiverad LOB-App-panelen i åtkomstpanelen, bör du få automatiskt loggat in på ditt SAML 1.1 Token-aktiverat program för LOB-App.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/saml-tutorial/tutorial_general_01.png
[2]: ./media/saml-tutorial/tutorial_general_02.png
[3]: ./media/saml-tutorial/tutorial_general_03.png
[4]: ./media/saml-tutorial/tutorial_general_04.png

[100]: ./media/saml-tutorial/tutorial_general_100.png

[200]: ./media/saml-tutorial/tutorial_general_200.png
[201]: ./media/saml-tutorial/tutorial_general_201.png
[202]: ./media/saml-tutorial/tutorial_general_202.png
[203]: ./media/saml-tutorial/tutorial_general_203.png
