---
title: 'Självstudier: Azure Active Directory-integrering med Questetra BPM Suite | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Questetra BPM Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: e9a4ab5bb262b274f4c53741f7a9ea1ce3956848
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220136"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Självstudier: Azure Active Directory-integrering med Questetra BPM Suite

I kursen får lära du att integrera Questetra BPM Suite med Azure Active Directory (AD Azure).

Integrera Questetra BPM Suite med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Questetra BPM Suite
- Du kan aktivera användarna att automatiskt hämta loggat in på Questetra BPM Suite (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Questetra BPM Suite, behöver du följande:

- En Azure AD-prenumeration
- En Questetra BPM Suite enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägg till Questetra BPM Suite från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>Lägg till Questetra BPM Suite från galleriet
Du måste lägga till Questetra BPM Suite från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Questetra BPM Suite i Azure AD.

**Utför följande steg för att lägga till Questetra BPM Suite från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Questetra BPM Suite**väljer **Questetra BPM Suite** från resultatet Kontrollpanelen och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Lägg till från galleriet](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Questetra BPM Suite baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Questetra BPM Suite motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Questetra BPM Suite upprättas.

I Questetra BPM Suite tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Questetra BPM Suite, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Questetra BPM Suite](#create-a-questetra-bpm-suite-test-user)**  – har en motsvarighet för Britta Simon Questetra BPM Suite som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Questetra BPM Suite-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Questetra BPM Suite:**

1. I Azure-portalen på den **Questetra BPM Suite** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![SAML-baserad inloggning](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_samlbase.png)

3. På den **Questetra BPM Suite domän och URL: er** avsnittet, utför följande steg:

    ![Avsnittet Questetra BPM Suite domän och URL: er](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<subdomain>.questetra.net/`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Du kan få värdena från **SP Information** avsnittet på din **Questetra BPM Suite** företagets plats, vilket beskrivs senare i självstudiekursen eller kontakta [Questetra BPM Suite klientstöd team](https://www.questetra.com/contact/). 
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base 64)** och spara certifikatfilen på datorn.

    ![Signeringscertifikat för SAML-avsnitt](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_certificate.png) 

5. Klicka på **spara** knappen.

    ![Knappen Spara](./media/questetra-bpm-suite-tutorial/tutorial_general_400.png)

6. På den **Questetra BPM Suite Configuration** klickar du på **konfigurera Questetra BPM Suite** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurationsavsnittet Questetra BPM Suite](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_configure.png) 

7. Logga in i ett annat webbläsarfönster din **Questetra BPM Suite** företagets webbplats som administratör.

8. Klicka på menyn högst upp **systeminställningar**. 
   
    ![Azure AD-Single Sign-On][10]

9. Öppna den **SingleSignOnSAML** klickar du på **SSO (SAML)**. 
   
    ![Azure AD-Single Sign-On][11]

10. På din **Questetra BPM Suite** företagets webbplats, i den **SP Information** avsnittet, utför följande steg:

    a. Kopiera den **ACS URL**, och klistrar in det i den **logga URL** TextBox-kontroll i den **Questetra BPM Suite domän och URL: er** avsnitt från Azure-portalen.
    
    b. Kopiera den **enhets-ID**, och klistrar in det i den **identifierare** TextBox-kontroll i den **Questetra BPM Suite domän och URL: er** avsnitt från Azure-portalen.

11. På din **Questetra BPM Suite** företagets webbplats, utför följande steg: 
   
    ![Konfigurera enkel inloggning][15]
   
    a. Välj **aktivera enkel inloggning**.
   
    b. I **enhets-ID** textruta klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen.
    
    c. I **inloggning Sidadress** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.
    
    d. I **URL för utloggning** textruta klistra in värdet för **Sign-Out URL** som du har kopierat från Azure-portalen.
    
    e. I den **NameID format** textruta typen `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Öppna din **Base64-** kodade certifikatet i anteckningar hämtas från Azure-portalen, kopiera innehållet i den till Urklipp och klistrar in det i den **validering certifikat** textruta. 

    g. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-questetra-bpm-suite-test-user"></a>Skapa en testanvändare Questetra BPM Suite

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Questetra BPM Suite.

**Utför följande steg för att skapa en användare som kallas Britta Simon i Questetra BPM Suite:**

1. Logga in på webbplatsen Questetra BPM Suite företag som administratör.
2. Gå till **systeminställningar > användarlistan > Ny användare**. 
3. I dialogrutan Ny användare utför du följande steg: 
   
    ![Skapa testanvändare][300] 
   
    a. I den **namn** textruta typen **namn** användarens **britta.simon@contoso.com**.
   
    b. I den **e-post** textruta typen **e-post** för användaren **britta.simon@contoso.com**
   
    c. I den **lösenord** textruta typ a **lösenord** för användaren.
    
    d. Klicka på **Lägg till nya användare**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Questetra BPM Suite.

![Tilldela användare][200] 

**Om du vill tilldela Questetra BPM Suite Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Questetra BPM Suite**.

    ![Questetra BPM Suite i lista över appar](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Questetra BPM Suite på åtkomstpanelen du bör få automatiskt loggat in på ditt Questetra BPM Suite-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/questetra-bpm-suite-tutorial/tutorial_general_04.png
[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png

[100]: ./media/questetra-bpm-suite-tutorial/tutorial_general_100.png

[200]: ./media/questetra-bpm-suite-tutorial/tutorial_general_200.png
[201]: ./media/questetra-bpm-suite-tutorial/tutorial_general_201.png
[202]: ./media/questetra-bpm-suite-tutorial/tutorial_general_202.png
[203]: ./media/questetra-bpm-suite-tutorial/tutorial_general_203.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 

