---
title: 'Självstudier: Azure Active Directory-integrering med LockPath Keylight | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LockPath Keylight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bc5121f6604fae9a28b52db1bfb308d7cdb968d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56205094"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Självstudier: Azure Active Directory-integrering med LockPath Keylight

I den här självstudien får du lära dig hur du integrerar LockPath Keylight med Azure Active Directory (AD Azure).

Integrera LockPath Keylight med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till LockPath Keylight
- Du kan aktivera användarna att automatiskt få loggat in på LockPath Keylight (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med LockPath Keylight, behöver du följande objekt:

- En Azure AD-prenumeration
- En LockPath Keylight enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till LockPath Keylight från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Att lägga till LockPath Keylight från galleriet
För att konfigurera integrering av LockPath Keylight i Azure AD, som du behöver lägga till LockPath Keylight från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till LockPath Keylight från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **LockPath Keylight**.

    ![Skapa en Azure AD-användare för testning](./media/keylight-tutorial/tutorial_keylight_search.png)

1. I resultatpanelen väljer **LockPath Keylight**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/keylight-tutorial/tutorial_keylight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med LockPath Keylight baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i LockPath Keylight är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i LockPath Keylight upprättas.

I LockPath Keylight tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med LockPath Keylight, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare LockPath Keylight](#creating-a-lockpath-keylight-test-user)**  – du har en motsvarighet för Britta Simon i LockPath Keylight som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt LockPath Keylight-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med LockPath Keylight:**

1. I Azure-portalen på den **LockPath Keylight** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/keylight-tutorial/tutorial_keylight_samlbase.png)

1. På den **LockPath Keylight domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/keylight-tutorial/tutorial_keylight_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<company name>.keylightgrc.com/`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<company name>.keylightgrc.com`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<company name>.keylightgrc.com/Login.aspx`
    
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [LockPath Keylight klienten supportteamet](https://www.lockpath.com/contact/) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Raw)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/keylight-tutorial/tutorial_keylight_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/keylight-tutorial/tutorial_general_400.png)
    
1. På den **LockPath Keylight Configuration** klickar du på **konfigurera LockPath Keylight** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/keylight-tutorial/tutorial_keylight_configure.png) 

1. Om du vill aktivera enkel inloggning i LockPath Keylight, utför du följande steg:
   
    a. Inloggning till ditt konto LockPath Keylight som administratör.
    
    b. Klicka på menyn längst upp **Person**, och välj **Keylight installationsprogrammet**.
   
    ![Konfigurera enkel inloggning](./media/keylight-tutorial/401.png) 

    c. I trädvyn till vänster, klickar du på **SAML**.
   
    ![Konfigurera enkel inloggning](./media/keylight-tutorial/402.png) 

    d. På den **SAML-inställningar** dialogrutan klickar du på **redigera**.
   
    ![Konfigurera enkel inloggning](./media/keylight-tutorial/404.png) 

1. På den **redigera inställningar för SAML** dialogrutan utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/keylight-tutorial/405.png) 
   
    a. Ange **SAML-autentisering** till **Active**.

    b. Klistra in den **SAML enkel inloggning för tjänst-URL** värde som du har kopierat från Azure-portalen till den **inloggnings-URL för identitetsprovider** textrutan.

    c. Klistra in den **tjänst-URL för enkel utloggning** värde som du har kopierat från Azure-portalen till den **utloggnings-URL för identitetsprovider** textrutan.

    d. Klicka på **Välj fil** välja hämtade LockPath Keylight certifikatet och klicka sedan på **öppna** att ladda upp.

    e. Ange **SAML användar-Id plats** till **NameIdentifier-elementet i instruktionen ämne**.
    
    f. Ange den **Keylight tjänstleverantör** med hjälp av följande mönster: **https://&lt;CompanyName&gt;. keylightgrc.com**.
    
    g. Ange **automatiskt etablera användare** till **Active**.

    h. Ange **automatiskt etablera kontotyp** till **användarens fullständiga**.

    i. Ange **automatiskt etablera säkerhetsroll**väljer **standardanvändare med SAML**.
    
    j. Ange **automatiskt etablera security config**väljer **Standard Användarkonfiguration**.
     
    k. I den **e-attributet** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    l. I den **förnamn attributet** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    m. I den **senaste namnattributet** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
    
    n. Klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/keylight-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/keylight-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/keylight-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/keylight-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-lockpath-keylight-test-user"></a>Skapa en testanvändare LockPath Keylight

I det här avsnittet skapar du en användare som kallas Britta Simon i LockPath Keylight. LockPath Keylight stöder just-in-time-etablering, som är aktiverat som standard.

Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare skapas vid åtkomst till LockPath Keylight om användaren inte finns ännu. 

>[!NOTE]
>Om du vill skapa en användare manuellt kan du behöva kontakta den [LockPath Keylight klienten supportteamet](https://www.lockpath.com/contact/). 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LockPath Keylight.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon LockPath Keylight, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **LockPath Keylight**.

    ![Konfigurera enkel inloggning](./media/keylight-tutorial/tutorial_keylight_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen LockPath Keylight i åtkomstpanelen du bör få automatiskt loggat in på ditt LockPath Keylight-program. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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

