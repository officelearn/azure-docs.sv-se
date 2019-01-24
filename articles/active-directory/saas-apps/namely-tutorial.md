---
title: 'Självstudier: Azure Active Directory-integrering med nämligen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och nämligen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 9541d5c4-4c82-4b5b-b01a-6a3f75a2b7a1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: b76dfa7d16da487798281f5f31b9915ca9716ebc
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808416"
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Självstudier: Azure Active Directory-integrering med nämligen

Lär dig hur du integrerar nämligen med Azure Active Directory (AD Azure) i den här självstudien.

Integrera nämligen med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till nämligen
- Du kan ge användarna automatiskt får loggat in på nämligen (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med handlar bl.a, om du behöver följande:

- En Azure AD-prenumeration
- En nämligen enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till nämligen från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-namely-from-the-gallery"></a>Att lägga till nämligen från galleriet
För att konfigurera integrering av nämligen i Azure AD, som du behöver lägga till nämligen från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till nämligen från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **nämligen**.

    ![Skapa en Azure AD-användare för testning](./media/namely-tutorial/tutorial_namely_search.png)

1. I resultatpanelen väljer **nämligen**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/namely-tutorial/tutorial_namely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med nämligen baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till användaren i motsvarande nämligen till en användare i Azure AD. Med andra ord en länk relationen mellan en Azure AD-användare och relaterade användaren i nämligen måste upprättas.

I det kan tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD måste enkel inloggning med handlar bl.a, du du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare nämligen](#creating-a-namely-test-user)**  – du har en motsvarighet för Britta Simon i nämligen som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktiverar Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din nämligen program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med nämligen:**

1. I Azure-portalen på den **nämligen** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_samlbase.png)

1. På den **nämligen domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<subdomain>.namely.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [nämligen klienten supportteamet](https://www.namely.com/contact/) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_general_400.png)

1. På den **nämligen Configuration** klickar du på **konfigurera nämligen** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_configure.png) 

1. I ett nytt webbläsarfönster loggar du in på ditt nämligen företagets plats som administratör.

1. I verktygsfältet högst upp, klickar du på **företagets**.
   
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_06.png) 

1. Klicka på fliken **Settings** (Inställningar).
   
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_07.png) 

1. Klicka på **SAML**.
   
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_08.png) 

1. På den **SAML-inställningar** utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_09.png)
 
    a. Klicka på **Aktivera SAML**. 

    b. I den **SSO-url för identitetsprovider** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.
    
    c. Öppna din nedladdade certifikatet i anteckningar, kopiera innehållet och klistra in den i den **providern identitetscertifikat** textrutan.
     
    d. Klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/namely-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/namely-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/namely-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/namely-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-namely-test-user"></a>Skapa en nämligen testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon nämligen.

**Utför följande steg för att skapa en användare som kallas Britta Simon nämligen:**

1. Inloggning till din nämligen företagets plats som administratör.

1. I verktygsfältet högst upp, klickar du på **personer**.
   
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_10.png) 

1. Klicka på den **Directory** fliken.
   
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_11.png) 

1. Klicka på **Lägg till ny Person**.

    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_12.png)

1. På den **Lägg till ny Person** dialogrutan utför följande steg:

    a. I den **Förnamn** textrutan typ **Britta**.

    b. I den **efternamn** textrutan typ **Simon**.

    c. I den **e-post** textrutan skriver den **e-postadress** av BrittaSimon.

    d. Klicka på **Spara**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till nämligen.

![Tilldela användare][200] 

**Så här tilldelar Britta Simon nämligen utföra följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **nämligen**.

    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på den panelen nämligen i åtkomstpanelen, du bör få automatiskt loggat in på ditt nämligen program

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/namely-tutorial/tutorial_general_01.png
[2]: ./media/namely-tutorial/tutorial_general_02.png
[3]: ./media/namely-tutorial/tutorial_general_03.png
[4]: ./media/namely-tutorial/tutorial_general_04.png

[100]: ./media/namely-tutorial/tutorial_general_100.png

[200]: ./media/namely-tutorial/tutorial_general_200.png
[201]: ./media/namely-tutorial/tutorial_general_201.png
[202]: ./media/namely-tutorial/tutorial_general_202.png
[203]: ./media/namely-tutorial/tutorial_general_203.png

