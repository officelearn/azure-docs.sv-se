---
title: 'Självstudier: Azure Active Directory-integrering med Rightscale | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Rightscale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 3a8d376d-95fb-4dd7-832a-4fdd4dd7c87c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 301199667d2307bc81da7ef42f3e4f7daa750ee2
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199714"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Självstudier: Azure Active Directory-integrering med Rightscale

I den här självstudien får du lära dig hur du integrerar Rightscale med Azure Active Directory (AD Azure).

Integrera Rightscale med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Rightscale
- Du kan aktivera användarna att automatiskt få loggat in på Rightscale (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Rightscale, behöver du följande objekt:

- En Azure AD-prenumeration
- En Rightscale enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Rightscale från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-rightscale-from-the-gallery"></a>Att lägga till Rightscale från galleriet
För att konfigurera integrering av Rightscale i Azure AD, som du behöver lägga till Rightscale från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Rightscale från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Rightscale**.

    ![Skapa en Azure AD-användare för testning](./media/rightscale-tutorial/tutorial_rightscale_search.png)

1. I resultatpanelen väljer **Rightscale**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/rightscale-tutorial/tutorial_rightscale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Rightscale baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Rightscale är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Rightscale upprättas.

I Rightscale, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Rightscale, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Rightscale](#creating-a-rightscale-test-user)**  – du har en motsvarighet för Britta Simon i Rightscale som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Rightscale program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Rightscale:**

1. I Azure-portalen på den **Rightscale** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_samlbase.png)

1. På den **Rightscale domän och URL: er** om du vill konfigurera programmet i **IDP-initierad läge** du behöver inte utföra några steg som appen är redan förintegrerade med Azure.

    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_url.png)

1. På den **Rightscale domän och URL: er** om du vill konfigurera programmet i **SP initierade läge**, utför följande steg:
    
    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_url1.png)

    a. Klicka på den **visa avancerade URL-inställningar**.

    b. I den **inloggning på URL: en** textrutan anger du URL: `https://login.rightscale.com/`

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_general_400.png)

1. På den **Rightscale Configuration** klickar du på **konfigurera Rightscale** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_configure.png) 
<CS>
1. För att få SSO konfigurerats för ditt program kan behöva du inloggning till din RightScale-klient som administratör.

    a. Klicka på menyn längst upp i **inställningar** fliken och markera **enkel inloggning**.
   
    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_001.png) 

    b. Klicka på den ”**nya**” för att lägga till **Your SAML-Identitetsproviders**.
   
    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_002.png) 
 
    c. I textrutan för **visningsnamn**, ange namnet på ditt företag.
   
    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_003.png)
 
    d. Välj **Tillåt RightScale-initierad SSO med hjälp av en identifieringstips** och indata din **domännamn** i den under textrutan.
   
    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_004.png)

    e. Klistra in värdet för **SAML enkel inloggning för tjänst-URL** som du har kopierat från Azure-portalen i **SAML SSO-slutpunkten** i RightScale.
   
    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_006.png)

    f. Klistra in värdet för **SAML entitets-ID** som du har kopierat från Azure-portalen i **SAML EntityID** i RightScale.
   
    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_008.png)

    g. Klicka på **webbläsare** knappen för att ladda upp det certifikat som du har hämtat från Azure-portalen.
   
    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_009.png)

    h. Klicka på **Spara**.
<CE>
> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/rightscale-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/rightscale-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/rightscale-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/rightscale-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-rightscale-test-user"></a>Skapa en Rightscale testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i RightScale. Arbeta med [Rightscale klienten supportteamet](mailto:support@rightscale.com) att lägga till användare i RightScale-plattformen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Rightscale.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Rightscale, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Rightscale**.

    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.  

När du klickar på panelen RightScale i åtkomstpanelen du bör få automatiskt loggat in på ditt RightScale program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rightscale-tutorial/tutorial_general_01.png
[2]: ./media/rightscale-tutorial/tutorial_general_02.png
[3]: ./media/rightscale-tutorial/tutorial_general_03.png
[4]: ./media/rightscale-tutorial/tutorial_general_04.png

[100]: ./media/rightscale-tutorial/tutorial_general_100.png

[200]: ./media/rightscale-tutorial/tutorial_general_200.png
[201]: ./media/rightscale-tutorial/tutorial_general_201.png
[202]: ./media/rightscale-tutorial/tutorial_general_202.png
[203]: ./media/rightscale-tutorial/tutorial_general_203.png

