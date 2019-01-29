---
title: 'Självstudier: Azure Active Directory-integrering med Origami | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Origami.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 042167b77679338ca7e982b1b1cd80c22997b766
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171079"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Självstudier: Azure Active Directory-integrering med Origami

I den här självstudien får du lära dig hur du integrerar Origami med Azure Active Directory (AD Azure).

Integrera Origami med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Origami
- Du kan aktivera användarna att automatiskt få loggat in på Origami (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Origami, behöver du följande objekt:

- En Azure AD-prenumeration
- En Origami enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Origami från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-origami-from-the-gallery"></a>Att lägga till Origami från galleriet
För att konfigurera integrering av Origami i Azure AD, som du behöver lägga till Origami från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Origami från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Origami**.

    ![Skapa en Azure AD-användare för testning](./media/origami-tutorial/tutorial_origami_search.png)

1. I resultatpanelen väljer **Origami**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/origami-tutorial/tutorial_origami_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Origami baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Origami är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Origami upprättas.

I Origami, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Origami, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Origami](#creating-an-origami-test-user)**  – du har en motsvarighet för Britta Simon i Origami som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Origami-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Origami:**

1. I Azure-portalen på den **Origami** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_samlbase.png)

1. På den **Origami domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_url.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE] 
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Origami klienten supportteamet](https://wordpress.org/support/theme/origami) att hämta värdet. 
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_general_400.png)

1. På den **Origami Configuration** klickar du på **konfigurera Origami** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_configure.png) 

1. Logga in på Origami-konto med administratörsrättigheter.

1. På menyn längst upp klickar du på **Admin**.
   
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_51.png)

1. Utför följande steg på sidan enkel inloggning på installation dialogrutan:
   
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_531.png)

    a. Välj **aktivera enkel inloggning**.

    b. I den **identitetsprovider inloggning sid-URL** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    c. I den **sid-URL för identitetsprovider utloggning** textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.

    d. Klicka på **Bläddra** att ladda upp det certifikat som du har hämtat från Azure-portalen.

    e. Klicka på **Spara ändringar**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/origami-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/origami-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/origami-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/origami-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-origami-test-user"></a>Skapa en Origami testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Origami. 

1. Logga in på Origami-konto med administratörsrättigheter.

1. På menyn längst upp klickar du på **Admin**.
   
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_51.png)

1. På den **användare och säkerhet** dialogrutan klickar du på **användare**.
   
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_54.png)

1. Klicka på **Lägg till ny användare**.
   
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_55.png)

1. I dialogrutan Lägg till ny användare utför du följande steg:
   
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_56.png)

    a. I den **användarnamn** textrutan Ange e-postadress för användaren som **brittasimon@contoso.com**.

    b. I den **lösenord** textrutan anger du ett lösenord.

    c. I den **Bekräfta lösenord** textrutan skriver du lösenordet igen.

    d. I den **Förnamn** textrutan Ange först namnet på användaren som **Britta**.

    e. I den **efternamn** textrutan Ange efternamn för användaren som **Simon**.

    f. Klicka på **Spara**.
   
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_57.png)

1. Tilldela **användarroller** och **klientåtkomst** för användaren. 
   
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Origami.

![Tilldela användare][200] 

**Om du vill tilldela Origami Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Origami**.

    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Origami i åtkomstpanelen du bör få automatiskt loggat in på ditt Origami-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/origami-tutorial/tutorial_general_01.png
[2]: ./media/origami-tutorial/tutorial_general_02.png
[3]: ./media/origami-tutorial/tutorial_general_03.png
[4]: ./media/origami-tutorial/tutorial_general_04.png

[100]: ./media/origami-tutorial/tutorial_general_100.png

[200]: ./media/origami-tutorial/tutorial_general_200.png
[201]: ./media/origami-tutorial/tutorial_general_201.png
[202]: ./media/origami-tutorial/tutorial_general_202.png
[203]: ./media/origami-tutorial/tutorial_general_203.png

