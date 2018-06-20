---
title: 'Självstudier: Azure Active Directory-integrering med SkyDesk e-post | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SkyDesk e-post.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 39821146f953630a7722ff866c60cab8b7d46175
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220842"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Självstudier: Azure Active Directory-integrering med SkyDesk e-post

I kursen får lära du att integrera SkyDesk e-post med Azure Active Directory (AD Azure).

Integrera SkyDesk e-post med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till e-SkyDesk
- Du kan aktivera användarna att automatiskt hämta inloggade till SkyDesk e-post (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med SkyDesk e-post, behöver du följande:

- En Azure AD-prenumeration
- En e-SkyDesk enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad här [utvärderingsversion erbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägga till SkyDesk från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-skydesk-email-from-the-gallery"></a>Lägga till SkyDesk från galleriet
Du måste lägga till SkyDesk e-post från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SkyDesk e-post i Azure AD.

**Utför följande steg för att lägga till SkyDesk e-post från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **SkyDesk e-**.

    ![Skapa en testanvändare i Azure AD](./media/skydeskemail-tutorial/tutorial_skydeskemail_search.png)

5. Välj i resultatpanelen **SkyDesk e-**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/skydeskemail-tutorial/tutorial_skydeskemail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
Du konfigurera och testa Azure AD enkel inloggning med SkyDesk e-post baserat på en testanvändare som kallas ”Britta Simon” i det här avsnittet.

Azure AD måste du känna till motsvarande användaren i SkyDesk e-post till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SkyDesk e-post upprättas.

I SkyDesk e-post, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med SkyDesk e-post, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare SkyDesk e-](#creating-a-skydesk-email-test-user)**  – du har en motsvarighet för Britta Simon i SkyDesk e-post som är länkad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i SkyDesk e-postprogrammet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SkyDesk e-post:**

1. I Azure-portalen på den **SkyDesk e-** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_samlbase.png)

3. På den **SkyDesk e-postdomän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_url.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE] 
    > Värdet är inte verkliga. Uppdatera värdet med det faktiska inloggnings-URL. Kontakta [SkyDesk e-postklient supportteamet](https://www.skydesk.sg/support/) värdet hämtas. 
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_general_400.png)

6. På den **SkyDesk e-postkonfigurationen** klickar du på **Konfigurera e-post från SkyDesk** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_configure.png) 

7. Att aktivera enkel inloggning i **SkyDesk e-**, utför följande steg:

    a. Inloggning till ditt SkyDesk e-postkonto som administratör.

    b. Klicka på menyn högst upp **installationsprogrammet**, och välj **Org**. 
    
      ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
    c. Klicka på **domäner** från den vänstra panelen.
    
      ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

    d. Klicka på **Lägg till domän**.
    
      ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

    e. Ange ditt domännamn och verifiera domänen.
    
      ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

    f. Klicka på **SAML-autentisering** från den vänstra panelen.
    
      ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

8. På den **SAML-autentisering** dialogrutan utför följande steg:
   
      ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)
   
    >[!NOTE]
    >Om du vill använda SAML-baserad autentisering ska du antingen har **verifierade domän** eller **portal URL** installationen. Du kan ange portalen URL: en med ett unikt namn.
    > 
    > 
   
    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. I den **Inloggningswebbadressen** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.
   
    b. I den **logga ut** URL: en textruta klistra in värdet för **Sign-Out URL**, som du har kopierat från Azure-portalen.

    c. **Ändra lösenord URL** är valfritt så lämna det tomt.

    d. Klicka på **hämta nyckeln från filen** välja hämtade certifikatet från Azure-portalen och klicka sedan på **öppna** att ladda upp certifikatet.

    e. Som **algoritmen**väljer **RSA**.

    f. Klicka på **Ok** spara ändringarna.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/skydeskemail-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/skydeskemail-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/skydeskemail-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/skydeskemail-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-skydesk-email-test-user"></a>Skapa en testanvändare SkyDesk e-post

I det här avsnittet kan du skapa en användare som kallas Britta Simon i SkyDesk e-post.

1. Klicka på **användaråtkomst** från vänstra panelen i SkyDesk e-post och ange sedan ditt användarnamn. 

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

>[!NOTE] 
>Om du behöver skapa grupp användare, måste du kontakta den [SkyDesk e-postklient supportteamet](https://www.skydesk.sg/support/).


### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till e-SkyDesk.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon SkyDesk e-post, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SkyDesk e-**.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen SkyDesk e-post på åtkomstpanelen du bör få automatiskt loggat in på SkyDesk e-postprogrammet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/skydeskemail-tutorial/tutorial_general_04.png

[100]: ./media/skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/skydeskemail-tutorial/tutorial_general_201.png
[202]: ./media/skydeskemail-tutorial/tutorial_general_202.png
[203]: ./media/skydeskemail-tutorial/tutorial_general_203.png

