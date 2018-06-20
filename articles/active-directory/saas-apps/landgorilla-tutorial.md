---
title: 'Självstudier: Azure Active Directory-integrering med mark Gorilla klienten | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och mark Gorilla.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: jeedes
ms.openlocfilehash: a65c15991a4545cbd0dc9956069af3292c9859dd
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36222574"
---
# <a name="tutorial-azure-active-directory-integration-with-land-gorilla-client"></a>Självstudier: Azure Active Directory-integrering med mark Gorilla klienten

I kursen får lära du att integrera mark Gorilla klienten med Azure Active Directory (AD Azure).

Integrera mark Gorilla klienten med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till mark Gorilla klienten
- Du kan aktivera användarna att automatiskt hämta loggat in på mark Gorilla klienten (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - till Azure-hanteringsportalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).


## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med mark Gorilla klienten behöver du följande:

- En Azure AD-prenumeration
- En mark Gorilla klienten enkel inloggning på aktiverade prenumeration


> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.


Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion av en månad [här](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägger till mark Gorilla klient från galleriet
2. Konfigurera och testa Azure AD enkel inloggning


## <a name="adding-land-gorilla-client-from-the-gallery"></a>Lägger till mark Gorilla klient från galleriet
Du måste lägga till mark Gorilla klienten från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av mark Gorilla klienten i Azure AD.

**Utför följande steg för att lägga till mark Gorilla klienten från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **mark Gorilla klienten**.

    ![Skapa en testanvändare i Azure AD](./media/landgorilla-tutorial/tutorial_landgorilla_search.png)

5. Välj i resultatpanelen **mark Gorilla klienten**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/landgorilla-tutorial/tutorial_landgorilla_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med mark Gorilla klient med en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i mark Gorilla klienten till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i mark Gorilla klienten upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** mark Gorilla-klienten.

Om du vill konfigurera och testa Azure AD enkel inloggning med mark Gorilla klienten, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med en begränsad grupp.
3. **[Skapa en testanvändare mark Gorilla](#creating-a-land-gorilla-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt Land Gorilla klientprogram.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med mark Gorilla klienten:**

1. I Azure-hanteringsportalen på den **mark Gorilla klienten** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserade inloggning** att aktivera enkel inloggning på.
 
    ![Konfigurera enkel inloggning](./media/landgorilla-tutorial/tutorial_landgorilla_samlbase.png)

3. På den **mark Gorilla klienten domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/landgorilla-tutorial/tutorial_landgorilla_url_02.png)

    a. I den **identifierare** textruta Skriv värdet med någon av följande mönster: 
    
    `https://<customer domain>.landgorilla.com/` 
    
    `https://www.<customer domain>.landgorilla.com`

    b. I den **Reply URL** textruta, ange ett URL-Adressen med något av följande mönster:

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`
    
    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`

    > [!NOTE] 
    > Observera att detta inte är verkliga värden. Du måste uppdatera dessa värden med de faktiska identifierare och Reply-URL. Här rekommenderar vi att du om du vill använda det unika värdet på strängen i identifieraren. Kontakta [mark Gorilla klienten team](https://www.landgorilla.com/support/) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/landgorilla-tutorial/tutorial_landgorilla_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/landgorilla-tutorial/tutorial_general_400.png) 

6. För att få SSO konfigurationen klar för tillämpningsprogrammet mark Gorilla slutet, kontakta [mark Gorilla klienten supportteamet](https://www.landgorilla.com/support/) och ger dem den hämtade **”XML-Metadata för** fil.


### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/landgorilla-tutorial/create_aaduser_01.png) 

2. Gå till **användare och grupper** och på **alla användare** att visa en lista över användare.
    
    ![Skapa en testanvändare i Azure AD](./media/landgorilla-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/landgorilla-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/landgorilla-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**. 

### <a name="creating-a-land-gorilla-test-user"></a>Skapa en testanvändare mark Gorilla

Se tillsammans med [mark Gorilla supportteamet](https://www.landgorilla.com/support/) att lägga till användare i mark Gorilla-plattformen.
    
### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja sin åtkomst till mark Gorilla klienten.

![Tilldela användare][200] 

**Om du vill tilldela mark Gorilla klienten Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **mark Gorilla klienten**.

    ![Konfigurera enkel inloggning](./media/landgorilla-tutorial/tutorial_landgorilla_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    


### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen mark Gorilla klienten på åtkomstpanelen du bör få automatiskt loggat in på ditt Land Gorilla klientprogram.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/landgorilla-tutorial/tutorial_general_01.png
[2]: ./media/landgorilla-tutorial/tutorial_general_02.png
[3]: ./media/landgorilla-tutorial/tutorial_general_03.png
[4]: ./media/landgorilla-tutorial/tutorial_general_04.png

[100]: ./media/landgorilla-tutorial/tutorial_general_100.png
[200]: ./media/landgorilla-tutorial/tutorial_general_200.png
[201]: ./media/landgorilla-tutorial/tutorial_general_201.png
[202]: ./media/landgorilla-tutorial/tutorial_general_202.png
[203]: ./media/landgorilla-tutorial/tutorial_general_203.png
