---
title: 'Självstudier: Azure Active Directory-integrering med SAML 1.1 Token aktiverade LOB-App | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML 1.1 Token-aktiverade LOB-App.
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
ms.openlocfilehash: fca447b24a299fed116356ca0f985020e079344b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317610"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Självstudier: Azure Active Directory-integrering med SAML 1.1 Token aktiverade LOB-App

I kursen får du lära dig hur du integrerar SAML 1.1 Token-aktiverade LOB-App med Azure Active Directory (AD Azure).

Integrera SAML 1.1 Token ger aktiverade LOB-App med Azure AD följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SAML 1.1 Token aktiverade LOB-App.
- Du kan ge användarna automatiskt får loggat in på SAML 1.1 Token aktiverade LOB-App (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Att konfigurera Azure AD-integrering med SAML 1.1 Token aktiverade LOB-App, behöver du följande:

- En Azure AD-prenumeration
- En SAML 1.1 Token aktiverat LOB-App enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägga till SAML 1.1 Token-aktiverade LOB-App från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>Lägga till SAML 1.1 Token-aktiverade LOB-App från galleriet
Om du vill konfigurera integreringen av SAML-Token 1.1-aktiverade LOB-App till Azure AD, måste du lägga till SAML 1.1 Token-aktiverade LOB-App från galleriet i listan över hanterade SaaS-appar.

**Om du vill lägga till SAML 1.1 Token-aktiverade LOB-App från galleriet, utför du följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SAML 1.1 Token-aktiverade LOB-App**väljer **SAML 1.1 Token-aktiverade LOB-App** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![SAML-Token 1.1 aktiverade LOB-App i resultatlistan över](./media/saml-tutorial/tutorial_saml_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med SAML 1.1 Token aktiverade LOB-App baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, Azure AD som behöver veta vilka motsvarande användaren i SAML 1.1 Token aktiverade LOB-App är att en användare i Azure AD. Med andra ord aktiverat en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SAML-Token 1.1 LOB-App behöver etableras.

Konfigurera och testa Azure AD enkel inloggning med SAML 1.1 Token-aktiverade LOB-App måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en SAML 1.1 Token aktiverat LOB-App testanvändare](#create-a-saml-11-token-enabled-lob-app-test-user)**  – du har en motsvarighet för Britta Simon i SAML-Token 1.1 aktiverade LOB-App som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet kan du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din SAML 1.1 Token-aktiverat program för LOB-App.

**Om du vill konfigurera Azure AD enkel inloggning med SAML 1.1 Token-aktiverade LOB-App, utför följande steg:**

1. I Azure-portalen på den **SAML 1.1 Token-aktiverade LOB-App** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/saml-tutorial/tutorial_saml_samlbase.png)

3. På den **SAML 1.1 Token aktiverat URL: er och LOB tillämpningsdomän** avsnittet, utför följande steg:

    ![SAML-Token 1.1 aktiverat LOB tillämpningsdomän URL: er och enkel inloggning information](./media/saml-tutorial/tutorial_saml_url.png)

    a. I den **inloggning URL** textruta Skriv en URL med följande mönster: `https://your-app-url`

    b. I den **identifierare (enhets-ID)** textruta Skriv en URL med följande mönster: `https://your-app-url`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Ersätt dessa värden med specifika URL: er.  

5. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/saml-tutorial/tutorial_saml_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/saml-tutorial/tutorial_general_400.png)
    
7. På den **SAML 1.1 Token aktiverat LOB Appkonfiguration** klickar du på **konfigurera SAML 1.1 Token-aktiverade LOB-App** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![SAML-Token 1.1 aktiverat LOB-App-konfiguration](./media/saml-tutorial/tutorial_saml_configure.png) 

8. Konfigurera enkel inloggning på **SAML 1.1 Token-aktiverade LOB-App** sida, måste du skicka den hämtade **certifikat (Base64), Sign-Out URL, SAML enhets-ID och SAML inloggning tjänst-URL för enkel** till supportgrupp för programmet. De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/saml-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/saml-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/saml-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/saml-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-saml-11-token-enabled-lob-app-test-user"></a>Skapa en SAML 1.1 Token aktiverat LOB-App testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i SAML-Token 1.1-aktiverade LOB-App. Arbeta med program stöder team för att skapa användare på program på klientsidan. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAML 1.1 Token-aktiverade LOB-App.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon SAML 1.1 Token-aktiverade LOB-App, utför följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SAML 1.1 Token-aktiverade LOB-App**.

    ![SAML 1.1 Token aktiverad länk LOB-App i listan med program](./media/saml-tutorial/tutorial_saml_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på SAML 1.1 Token aktiverat LOB-App-panelen på åtkomstpanelen, du får automatiskt loggat in på ditt SAML 1.1 Token-aktiverat program för LOB-App.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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
