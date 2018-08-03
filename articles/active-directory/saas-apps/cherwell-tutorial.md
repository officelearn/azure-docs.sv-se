---
title: 'Självstudier: Azure Active Directory-integration med Cherwell | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cherwell.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ad891f99-179e-4487-834d-35f3bc01c1ec
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2017
ms.author: jeedes
ms.openlocfilehash: 9643a65eb2618a6b9ed5834024a1f29450c54af6
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421268"
---
# <a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Självstudier: Azure Active Directory-integration med Cherwell

I den här självstudien får du lära dig hur du integrerar Cherwell med Azure Active Directory (AD Azure).

Integrera Cherwell med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Cherwell
- Du kan aktivera användarna att automatiskt få loggat in på Cherwell (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Cherwell, behöver du följande objekt:

- En Azure AD-prenumeration
- En Cherwell enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Cherwell från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-cherwell-from-the-gallery"></a>Att lägga till Cherwell från galleriet
För att konfigurera integrering av Cherwell i Azure AD, som du behöver lägga till Cherwell från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Cherwell från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **Cherwell**.

    ![Skapa en Azure AD-användare för testning](./media/cherwell-tutorial/tutorial_cherwell_search.png)

1. I resultatpanelen väljer **Cherwell**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/cherwell-tutorial/tutorial_cherwell_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Cherwell baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Cherwell är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Cherwell upprättas.

I Cherwell, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Cherwell, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Cherwell](#creating-a-cherwell-test-user)**  – du har en motsvarighet för Britta Simon i Cherwell som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Cherwell.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Cherwell:**

1. I Azure-portalen på den **Cherwell** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/cherwell-tutorial/tutorial_cherwell_samlbase.png)

1. På den **Cherwell domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/cherwell-tutorial/tutorial_cherwell_url.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.cherwellondemand.com/cherwellclient`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [Cherwell supportteamet](https://csm.cherwell.com/contact) att hämta det här värdet.
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/cherwell-tutorial/tutorial_cherwell_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/cherwell-tutorial/tutorial_general_400.png)

1. På den **Cherwell Configuration** klickar du på **konfigurera Cherwell** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/cherwell-tutorial/tutorial_cherwell_configure.png) 

1. Att konfigurera enkel inloggning på **Cherwell** sida, som du behöver skicka de hämtade **certifikat (Base64)**, **SAML enkel inloggning för tjänst-URL**, och **SAML Entitets-ID** till [Cherwell supportteamet](https://csm.cherwell.com/contact). 

    >[!NOTE]
    >Supportteamet Cherwell har att göra den faktiska SSO-konfigurationen. Du får ett meddelande när enkel inloggning har aktiverats för din prenumeration.
    > 
    
> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/cherwell-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/cherwell-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/cherwell-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/cherwell-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-cherwell-test-user"></a>Skapa en Cherwell testanvändare

Om du vill aktivera Azure AD-användare att logga in på Cherwell, måste de etableras i Cherwell.

När det gäller Cherwell, behöver användarkontona som ska skapas av din [Cherwell supportteamet](https://csm.cherwell.com/contact).

>[!NOTE]
>Du kan använda alla andra Cherwell användare konto verktyg för att skapa eller API: er som tillhandahålls av Cherwell för att etablera Azure Active Directory användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Cherwell.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Cherwell, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Cherwell**.

    ![Konfigurera enkel inloggning](./media/cherwell-tutorial/tutorial_cherwell_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Öppna panelen om du vill testa dina inställningar för enkel inloggning. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/cherwell-tutorial/tutorial_general_01.png
[2]: ./media/cherwell-tutorial/tutorial_general_02.png
[3]: ./media/cherwell-tutorial/tutorial_general_03.png
[4]: ./media/cherwell-tutorial/tutorial_general_04.png

[100]: ./media/cherwell-tutorial/tutorial_general_100.png

[200]: ./media/cherwell-tutorial/tutorial_general_200.png
[201]: ./media/cherwell-tutorial/tutorial_general_201.png
[202]: ./media/cherwell-tutorial/tutorial_general_202.png
[203]: ./media/cherwell-tutorial/tutorial_general_203.png

