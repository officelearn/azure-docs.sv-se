---
title: 'Självstudier: Azure Active Directory-integrering med O.C. Turesson - AppreciateHub | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och O.C. Turesson - AppreciateHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.openlocfilehash: d2ef323ca2fd3b863ba03a109bf10a43810cf9e8
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35966601"
---
# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>Självstudier: Azure Active Directory-integrering med O.C. Turesson - AppreciateHub

I kursen får du lära dig hur du integrerar O.C. Turesson - AppreciateHub med Azure Active Directory (AD Azure).

Integrera O.C. Turesson - AppreciateHub med Azure AD ger följande fördelar:

- Du kan styra i Azure AD som har åtkomst till O.C. Turesson - AppreciateHub
- Du kan aktivera användarna att automatiskt hämta loggat in på O.C. Turesson - AppreciateHub (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Konfigurera Azure AD-integrering med O.C. Turesson - AppreciateHub, behöver du följande:

- En Azure AD-prenumeration
- EN O.C. Turesson - AppreciateHub enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägga till O.C. Turesson - AppreciateHub från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Lägga till O.C. Turesson - AppreciateHub från galleriet
Så här konfigurerar du integrering av O.C. Turesson - AppreciateHub till Azure AD som du behöver lägga till O.C. Turesson - AppreciateHub från galleriet i listan över hanterade SaaS-appar.

**Att lägga till O.C. Turesson - AppreciateHub från galleriet, utför följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **O.C. Turesson - AppreciateHub**.

    ![Skapa en testanvändare i Azure AD](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_search.png)

5. Välj i resultatpanelen **O.C. Turesson - AppreciateHub**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med O.C. Turesson - AppreciateHub baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD veta vilka motsvarande användaren i O.C. Turesson - AppreciateHub är att en användare i Azure AD. Med andra ord en länk förhållandet mellan en Azure AD-användare och relaterade användaren i O.C. Turesson - AppreciateHub måste upprättas.

I O.C. Turesson - AppreciateHub, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Konfigurera och testa Azure AD enkel inloggning med O.C. Turesson - AppreciateHub, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en O.C. Turesson - AppreciateHub testanvändare](#creating-a-oc-tanner---appreciatehub-test-user)**  – har en motsvarighet för Britta Simon O.C. Turesson - AppreciateHub som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet kan du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din O.C. Turesson - AppreciateHub program.

**Konfigurera Azure AD enkel inloggning med O.C. Turesson - AppreciateHub, utför följande steg:**

1. I Azure-portalen på den **O.C. Turesson - AppreciateHub** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_samlbase.png)

3. På den **O.C. Turesson - URL: er och AppreciateHub domän** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_url.png)

    a. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<companyname>.octanner.net/sp/ACS.saml2`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska Reply-URL. Kontakta [O.C. Turesson - AppreciateHub supportteamet](mailto:sso@octanner.com) att hämta det här värdet.

    b. Öppna metadatafilen med hjälp av följande länk: [ https://fed.appreciatehub.com/fed/sp/metadata ](https://fed.appreciatehub.com/fed/sp/metadata).
   
    c. Leta upp den **md:AssertionConsumerService** nod. 
   
    d. Kopiera värdet för den **plats** attribut. 
   
    ![Konfigurera Appinställningar för][12]
   
    e. I den **logga URL** textruta efter värdet som du har fått i föregående steg.

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/oc-tanner-tutorial/tutorial_general_400.png)

6. Konfigurera enkel inloggning på **O.C. Turesson - AppreciateHub** sida, måste du skicka den hämtade **XML-Metadata för** till [O.C. Turesson - AppreciateHub supportteamet](mailto:sso@octanner.com).

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/oc-tanner-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/oc-tanner-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/oc-tanner-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/oc-tanner-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>Skapa en O.C. Turesson - AppreciateHub testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i O.C. Turesson - AppreciateHub.

**Om du vill skapa en användare med namnet Britta Simon i O.C. Turesson - AppreciateHub, utför följande steg:**

Be din [O.C. Turesson - AppreciateHub supportteamet](mailto:sso@octanner.com) att skapa en användare som som nameID attribut har samma värde som användarnamn för Britta Simon i Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till O.C. Turesson - AppreciateHub.

![Tilldela användare][200] 

**Att tilldela O.C. Britta Simon Turesson - AppreciateHub, utför följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **O.C. Turesson - AppreciateHub**.

    ![Konfigurera enkel inloggning](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.  
När du klickar på O.C. Turesson - AppreciateHub panelen på panelen åtkomst du ska hämta automatiskt loggat in på ditt O.C. Turesson - AppreciateHub program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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

