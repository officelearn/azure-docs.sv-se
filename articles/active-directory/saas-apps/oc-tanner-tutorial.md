---
title: 'Självstudier: Azure Active Directory-integrering med O.C. Turesson - AppreciateHub | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och O.C. Tanner - AppreciateHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.openlocfilehash: 3a9b11b5d5e70e72a90683d4952b7d34f0303960
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55163610"
---
# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>Självstudier: Azure Active Directory-integrering med O.C. Tanner - AppreciateHub

I den här självstudien får du lära dig hur du integrerar O.C. Turesson - AppreciateHub med Azure Active Directory (AD Azure).

Integrera O.C. Turesson - AppreciateHub med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till O.C. Tanner - AppreciateHub
- Du kan aktivera användarna att automatiskt få loggat in på O.C. Turesson - AppreciateHub (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Konfigurera Azure AD-integrering med O.C. Turesson - AppreciateHub, behöver du följande objekt:

- En Azure AD-prenumeration
- A O.C. Turesson - AppreciateHub enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till O.C. Turesson - AppreciateHub från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Att lägga till O.C. Turesson - AppreciateHub från galleriet
Konfigurera integreringen av O.C. Turesson - AppreciateHub till Azure AD, som du behöver lägga till O.C. Turesson - AppreciateHub från galleriet i din lista över hanterade SaaS-appar.

**Att lägga till O.C. Turesson - AppreciateHub från galleriet, utför följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **O.C. Turesson - AppreciateHub**.

    ![Skapa en Azure AD-användare för testning](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_search.png)

1. I resultatpanelen väljer **O.C. Turesson - AppreciateHub**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med O.C. Turesson - AppreciateHub utifrån en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vilka motsvarande användaren i O.C. Turesson - AppreciateHub är att en användare i Azure AD. Med andra ord en länk förhållandet mellan en Azure AD-användare och relaterade användaren i O.C. Turesson - AppreciateHub måste upprättas.

I O.C. Turesson - AppreciateHub, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Konfigurera och testa Azure AD enkel inloggning med O.C. Turesson - AppreciateHub, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en O.C. Turesson - AppreciateHub testanvändare](#creating-a-oc-tanner---appreciatehub-test-user)**  – du har en motsvarighet för Britta Simon i O.C. Turesson - AppreciateHub som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din O.C. Turesson - AppreciateHub program.

**Konfigurera Azure AD enkel inloggning med O.C. Turesson - AppreciateHub, utför följande steg:**

1. I Azure-portalen på den **O.C. Turesson - AppreciateHub** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_samlbase.png)

1. På den **O.C. Turesson - AppreciateHub domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_url.png)

    a. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<companyname>.octanner.net/sp/ACS.saml2`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med faktiska svars-URL. Kontakta [O.C. Turesson - AppreciateHub supportteamet](mailto:sso@octanner.com) att hämta det här värdet.

    b. Öppna metadatafilen med hjälp av följande länk: [ https://fed.appreciatehub.com/fed/sp/metadata ](https://fed.appreciatehub.com/fed/sp/metadata).
   
    c. Leta upp den **md:AssertionConsumerService** noden. 
   
    d. Kopiera värdet för den **plats** attribut. 
   
    ![Konfigurera Appinställningar][12]
   
    e. I den **inloggning på URL: en** textrutan tidigare värdet som du har fått i föregående steg.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/oc-tanner-tutorial/tutorial_general_400.png)

1. Att konfigurera enkel inloggning på **O.C. Turesson - AppreciateHub** sida, som du behöver skicka de hämtade **XML-Metadata för** till [O.C. Turesson - AppreciateHub supportteamet](mailto:sso@octanner.com).

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/oc-tanner-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/oc-tanner-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/oc-tanner-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/oc-tanner-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>Skapa en O.C. Tanner - AppreciateHub test user

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i O.C. Tanner - AppreciateHub.

**Skapa en användare som kallas Britta Simon i O.C. Turesson - AppreciateHub, utför följande steg:**

Be din [O.C. Turesson - AppreciateHub supportteamet](mailto:sso@octanner.com) att skapa en användare som har du samma värde som användarnamn för Britta Simon i Azure AD som nameID-attribut.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till O.C. Tanner - AppreciateHub.

![Tilldela användare][200] 

**Tilldela Britta Simon till O.C. Turesson - AppreciateHub, utför följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **O.C. Turesson - AppreciateHub**.

    ![Konfigurera enkel inloggning](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.  
När du klickar på O.C. Turesson - AppreciateHub panel i åtkomstpanelen, du bör få automatiskt loggat in på ditt O.C. Turesson - AppreciateHub program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/oc-tanner-tutorial/tutorial_general_04.png

[12]: ./media/oc-tanner-tutorial/tutorial_octanner_08.png

[100]: ./media/oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/oc-tanner-tutorial/tutorial_general_202.png
[203]: ./media/oc-tanner-tutorial/tutorial_general_203.png

