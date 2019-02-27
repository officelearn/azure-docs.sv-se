---
title: 'Självstudier: Azure Active Directory-integrering med InsideView | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a315933dcd438c68a1326d2b656c2353e4185f12
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56866801"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Självstudier: Azure Active Directory-integrering med InsideView

I den här självstudien får du lära dig hur du integrerar InsideView med Azure Active Directory (AD Azure).

Integrera InsideView med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till InsideView
- Du kan aktivera användarna att automatiskt få loggat in på InsideView (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med InsideView, behöver du följande objekt:

- En Azure AD-prenumeration
- En InsideView enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till InsideView från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-insideview-from-the-gallery"></a>Att lägga till InsideView från galleriet
Om du vill konfigurera integreringen av InsideView i till Azure AD, som du behöver lägga till InsideView från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till InsideView från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **InsideView**.

    ![Skapa en Azure AD-användare för testning](./media/insideview-tutorial/tutorial_insideview_search.png)

1. I resultatpanelen väljer **InsideView**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/insideview-tutorial/tutorial_insideview_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med InsideView baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i InsideView är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i InsideView upprättas.

I InsideView, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med InsideView, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare InsideView](#creating-a-insideview-test-user)**  – du har en motsvarighet för Britta Simon i InsideView som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt InsideView program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med InsideView:**

1. I Azure-portalen på den **InsideView** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/insideview-tutorial/tutorial_insideview_samlbase.png)

1. På den **InsideView domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/insideview-tutorial/tutorial_insideview_url.png)
    
    I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE] 
    > Det här värdet är inte verkligt. Uppdatera det här värdet med faktiska svars-URL. Kontakta [InsideView supportteamet](mailto:support@insideview.com) att hämta det här värdet.
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Raw)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/insideview-tutorial/tutorial_insideview_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/insideview-tutorial/tutorial_general_400.png)

1. På den **InsideView Configuration** klickar du på **konfigurera InsideView** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/insideview-tutorial/tutorial_insideview_configure.png) 

1. I ett annat webbläsarfönster logga du in på webbplatsen InsideView företag som administratör.

1. I verktygsfältet högst upp, klickar du på **Admin**, **SingleSignOn inställningar**, och klicka sedan på **lägga till SAML**.
   
   ![SAML enkel inloggning inställningar](./media/insideview-tutorial/ic794135.png "SAML enkel inloggning inställningar")

1. I den **lägga till en ny SAML** avsnittet, utför följande steg:

    ![Lägg till en ny SAML](./media/insideview-tutorial/ic794136.png "lägga till en ny SAML")
   
    a. I den **STS Name** textrutan anger du ett namn för din konfiguration.

    b. I **SamlP/WS-Fed oombedda EndPoint** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.
    
    c. Öppna Base64-kodade certifikatet, som du har hämtat från Azure-portalen, kopiera innehållet i den till Urklipp och klistra in den till den **STS Certificate** textrutan.

    d. I den **Crm användarmappning för Id** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
        
    e. I den **Crm e-mappning** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. I den **Crm förnamn mappning** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    g. I den **Crm lastName mappning** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.  

    h. Klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 
 
### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/insideview-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/insideview-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/insideview-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/insideview-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-insideview-test-user"></a>Skapa en InsideView testanvändare

Om du vill aktivera Azure AD-användare att logga in på InsideView, måste de vara etablerade i att InsideView. När det gäller InsideView är etablering en manuell aktivitet.

För att få användare eller kontakter som skapas i InsideView kan kontakta [InsideView supportteamet](mailto:support@insideview.com).

>[!NOTE]
>Du kan använda alla andra InsideView användare konto verktyg för att skapa eller API: er som tillhandahålls av InsideView att etablera användarkonton i Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till InsideView.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon InsideView, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **InsideView**.

    ![Konfigurera enkel inloggning](./media/insideview-tutorial/tutorial_insideview_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen InsideView i åtkomstpanelen du bör få automatiskt loggat in på ditt InsideView program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/insideview-tutorial/tutorial_general_01.png
[2]: ./media/insideview-tutorial/tutorial_general_02.png
[3]: ./media/insideview-tutorial/tutorial_general_03.png
[4]: ./media/insideview-tutorial/tutorial_general_04.png

[100]: ./media/insideview-tutorial/tutorial_general_100.png

[200]: ./media/insideview-tutorial/tutorial_general_200.png
[201]: ./media/insideview-tutorial/tutorial_general_201.png
[202]: ./media/insideview-tutorial/tutorial_general_202.png
[203]: ./media/insideview-tutorial/tutorial_general_203.png

