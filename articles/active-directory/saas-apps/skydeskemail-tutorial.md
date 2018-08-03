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
ms.openlocfilehash: 058aad72ea8e5741bc632b3c27c032613683ae78
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444090"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Självstudier: Azure Active Directory-integrering med SkyDesk e-postadress

I den här självstudien får du lära dig hur du integrerar SkyDesk e-post med Azure Active Directory (AD Azure).

Integrera SkyDesk e-post med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SkyDesk e-post
- Du kan aktivera användarna att automatiskt få loggat in på SkyDesk e-post (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SkyDesk e-postadress, behöver du följande objekt:

- En Azure AD-prenumeration
- En e-SkyDesk enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du kan få en månads utvärdering här [– prova](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till SkyDesk e-post från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-skydesk-email-from-the-gallery"></a>Att lägga till SkyDesk e-post från galleriet
För att konfigurera integrering av SkyDesk e-post i Azure AD, som du behöver lägga till SkyDesk e-post från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SkyDesk e-post från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **SkyDesk e-**.

    ![Skapa en Azure AD-användare för testning](./media/skydeskemail-tutorial/tutorial_skydeskemail_search.png)

1. I resultatpanelen väljer **SkyDesk e-**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/skydeskemail-tutorial/tutorial_skydeskemail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SkyDesk e-post baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användaren i SkyDesk e-postmeddelande till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i SkyDesk e-post upprättas.

I SkyDesk e-post, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med SkyDesk e-postadress, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare SkyDesk e-](#creating-a-skydesk-email-test-user)**  – du har en motsvarighet för Britta Simon i SkyDesk e-post som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktiverar Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i SkyDesk e-postprogrammet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SkyDesk e-postadress:**

1. I Azure-portalen på den **SkyDesk e-** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_samlbase.png)

1. På den **SkyDesk e-postdomäner och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_url.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE] 
    > Värdet är inte verkliga. Uppdatera värdet med faktiska inloggnings-URL: en. Kontakta [SkyDesk e-postklient supportteamet](https://www.skydesk.sg/support/) att hämta värdet. 
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_general_400.png)

1. På den **SkyDesk e-postkonfigurationen** klickar du på **Konfigurera e-post från SkyDesk** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_configure.png) 

1. Att aktivera enkel inloggning i **SkyDesk e-**, utför följande steg:

    a. Inloggning till ditt SkyDesk e-postkonto som administratör.

    b. Klicka på menyn längst upp **installationsprogrammet**, och välj **Org**. 
    
      ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
    c. Klicka på **domäner** från den vänstra panelen.
    
      ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

    d. Klicka på **Lägg till domän**.
    
      ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

    e. Ange ditt domännamn och verifiera domänen.
    
      ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

    f. Klicka på **SAML-autentisering** från den vänstra panelen.
    
      ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. På den **SAML-autentisering** dialogrutan utför följande steg:
   
      ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)
   
    >[!NOTE]
    >Om du vill använda SAML-baserad autentisering, bör du antingen ha **verifierad domän** eller **portal URL** installationen. Du kan ställa in portalen URL: en med ett unikt namn.
    > 
    > 
   
    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. I den **inloggnings-URL** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.
   
    b. I den **utloggning** URL-textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.

    c. **Ändra lösenord URL: en** är valfritt så lämna det tomt.

    d. Klicka på **hämta nyckeln från filen** välja nedladdade certifikatet från Azure-portalen och klicka sedan på **öppna** att ladda upp.

    e. Som **algoritmen**väljer **RSA**.

    f. Klicka på **Ok** att spara ändringarna.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/skydeskemail-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/skydeskemail-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/skydeskemail-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/skydeskemail-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-skydesk-email-test-user"></a>Skapa en testanvändare SkyDesk e-post

I det här avsnittet skapar du en användare som kallas Britta Simon i SkyDesk e-post.

1. Klicka på **användaråtkomst** från vänstra panelen i SkyDesk e-post och ange sedan ditt användarnamn. 

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

>[!NOTE] 
>Om du vill skapa grupp användare kan du behöva kontakta den [SkyDesk e-postklient supportteamet](https://www.skydesk.sg/support/).


### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till SkyDesk e-post.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon SkyDesk e-post, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **SkyDesk e-**.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på panelen SkyDesk e-post i åtkomstpanelen du bör få automatiskt loggat in på SkyDesk e-postprogrammet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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

