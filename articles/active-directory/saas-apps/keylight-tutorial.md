---
title: 'Självstudier: Azure Active Directory-integrering med LockPath Keylight | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LockPath Keylight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: d7947a2a294b2232a69a403a765cc2cc29c43405
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218324"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Självstudier: Azure Active Directory-integrering med LockPath Keylight

I kursen får lära du att integrera LockPath Keylight med Azure Active Directory (AD Azure).

Integrera LockPath Keylight med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till LockPath Keylight
- Du kan aktivera användarna att automatiskt hämta loggat in på LockPath Keylight (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med LockPath Keylight, behöver du följande:

- En Azure AD-prenumeration
- En LockPath Keylight enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till LockPath Keylight från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Att lägga till LockPath Keylight från galleriet
Du måste lägga till LockPath Keylight från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av LockPath Keylight i Azure AD.

**Utför följande steg för att lägga till LockPath Keylight från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **LockPath Keylight**.

    ![Skapa en testanvändare i Azure AD](./media/keylight-tutorial/tutorial_keylight_search.png)

5. Välj i resultatpanelen **LockPath Keylight**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/keylight-tutorial/tutorial_keylight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med LockPath Keylight baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i LockPath Keylight motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i LockPath Keylight upprättas.

I LockPath Keylight tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med LockPath Keylight, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare LockPath Keylight](#creating-a-lockpath-keylight-test-user)**  – har en motsvarighet för Britta Simon LockPath Keylight som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet LockPath Keylight.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med LockPath Keylight:**

1. I Azure-portalen på den **LockPath Keylight** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/keylight-tutorial/tutorial_keylight_samlbase.png)

3. På den **LockPath Keylight domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/keylight-tutorial/tutorial_keylight_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<company name>.keylightgrc.com/`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<company name>.keylightgrc.com`

    c. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<company name>.keylightgrc.com/Login.aspx`
    
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Kontakta [LockPath Keylight klienten supportteamet](https://www.lockpath.com/contact/) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Raw)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/keylight-tutorial/tutorial_keylight_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/keylight-tutorial/tutorial_general_400.png)
    
6. På den **LockPath Keylight Configuration** klickar du på **konfigurera LockPath Keylight** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out Webbadressen och SAML enkel inloggning Service** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/keylight-tutorial/tutorial_keylight_configure.png) 

7. Om du vill aktivera enkel inloggning i LockPath Keylight, utför du följande steg:
   
    a. Inloggning till ditt konto LockPath Keylight som administratör.
    
    b. Klicka på menyn högst upp **Person**, och välj **Keylight installationsprogrammet**.
   
    ![Konfigurera enkel inloggning](./media/keylight-tutorial/401.png) 

    c. I trädvyn till vänster klickar du på **SAML**.
   
    ![Konfigurera enkel inloggning](./media/keylight-tutorial/402.png) 

    d. På den **SAML inställningar** dialogrutan klickar du på **redigera**.
   
    ![Konfigurera enkel inloggning](./media/keylight-tutorial/404.png) 

8. På den **redigera inställningar för SAML** dialogrutan utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/keylight-tutorial/405.png) 
   
    a. Ange **SAML-autentisering** till **Active**.

    b. Klistra in den **SAML enkel inloggning Tjänstwebbadress** värde som du har kopierat från Azure-portalen i den **identitet providern inloggnings-URL** textruta.

    c. Klistra in den **tjänst-URL för enkel Sign-Out** värde som du har kopierat från Azure-portalen i den **identitet providern logga ut URL** textruta.

    d. Klicka på **Välj fil** välja hämtade LockPath Keylight certifikatet och klicka sedan på **öppna** att ladda upp certifikatet.

    e. Ange **användar-Id för SAML plats** till **NameIdentifier element i instruktionen ämne**.
    
    f. Ange den **Keylight Service Provider** med hjälp av följande mönster: **https://&lt;CompanyName&gt;. keylightgrc.com**.
    
    g. Ange **automatiskt etablera användare** till **Active**.

    h. Ange **automatiskt etablera kontotyp** till **fullständig användaren**.

    i. Ange **automatiskt etablera säkerhetsrollen**väljer **standardanvändare med SAML**.
    
    j. Ange **automatiskt etablera säkerhet config**väljer **Standard Användarkonfiguration**.
     
    k. I den **e-attributet** textruta typen `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    l. I den **förnamn attributet** textruta typen `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    m. I den **senaste namnattributet** textruta typen `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
    
    n. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/keylight-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/keylight-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/keylight-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/keylight-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-lockpath-keylight-test-user"></a>Skapa en testanvändare LockPath Keylight

I det här avsnittet skapar du en användare som kallas Britta Simon i LockPath Keylight. LockPath Keylight stöder just-in-time-allokering som är aktiverad som standard.

Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas vid åtkomst till LockPath Keylight om användaren inte finns ännu. 

>[!NOTE]
>Om du behöver skapa en användare manuellt, måste du kontakta den [LockPath Keylight klienten supportteamet](https://www.lockpath.com/contact/). 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LockPath Keylight.

![Tilldela användare][200] 

**Om du vill tilldela LockPath Keylight Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **LockPath Keylight**.

    ![Konfigurera enkel inloggning](./media/keylight-tutorial/tutorial_keylight_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen LockPath Keylight på åtkomstpanelen du bör få automatiskt loggat in på ditt LockPath Keylight program. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/keylight-tutorial/tutorial_general_01.png
[2]: ./media/keylight-tutorial/tutorial_general_02.png
[3]: ./media/keylight-tutorial/tutorial_general_03.png
[4]: ./media/keylight-tutorial/tutorial_general_04.png

[100]: ./media/keylight-tutorial/tutorial_general_100.png

[200]: ./media/keylight-tutorial/tutorial_general_200.png
[201]: ./media/keylight-tutorial/tutorial_general_201.png
[202]: ./media/keylight-tutorial/tutorial_general_202.png
[203]: ./media/keylight-tutorial/tutorial_general_203.png

