---
title: 'Självstudier: Azure Active Directory-integrering med Promapp | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Promapp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: jeedes
ms.openlocfilehash: 392bd8cf660ce1e394d25693bf1cd74d882fb028
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810150"
---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>Självstudier: Azure Active Directory-integrering med Promapp

I den här självstudien får du lära dig hur du integrerar Promapp med Azure Active Directory (AD Azure).

Integrera Promapp med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Promapp
- Du kan aktivera användarna att automatiskt få loggat in på Promapp (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Promapp, behöver du följande objekt:

- En Azure AD-prenumeration
- En Promapp enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Promapp från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-promapp-from-the-gallery"></a>Att lägga till Promapp från galleriet
För att konfigurera integrering av Promapp i Azure AD, som du behöver lägga till Promapp från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Promapp från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Promapp**.

    ![Skapa en Azure AD-användare för testning](./media/promapp-tutorial/tutorial_promapp_search.png)

1. I resultatpanelen väljer **Promapp**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/promapp-tutorial/tutorial_promapp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Promapp baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Promapp är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Promapp upprättas.

I Promapp, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Promapp, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Promapp](#creating-a-promapp-test-user)**  – du har en motsvarighet för Britta Simon i Promapp som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Promapp program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Promapp:**

1. I Azure-portalen på den **Promapp** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/promapp-tutorial/tutorial_promapp_samlbase.png)

1. På den **Promapp domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/promapp-tutorial/tutorial_promapp_url.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster:
    
    | |
    |--|
    | `https://go.promapp.com/TENANTNAME/`|
    | `https://au.promapp.com/TENANTNAME/`|
    | `https://us.promapp.com/TENANTNAME/`|
    | `https://eu.promapp.com/TENANTNAME/`|
    | `https://ca.promapp.com/TENANTNAME/`|
    
    > [!NOTE] 
    > Azure AD-integrering med Promapp har för närvarande endast konfigurerats för initierad tjänstautentisering t.ex. Gå till en URL för Promapp initierar autentiseringen. Men svars-URL måste anges.
    
    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://DOMAINNAME.promapp.com/azuread/saml/authenticate.aspx`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/promapp-tutorial/tutorial_promapp_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://DOMAINNAME.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Promapp klienten supportteamet](https://www.promapp.com/about-us/contact-us/) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/promapp-tutorial/tutorial_promapp_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/promapp-tutorial/tutorial_general_400.png)

1. På den **Promapp Configuration** klickar du på **konfigurera Promapp** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/promapp-tutorial/tutorial_promapp_configure.png) 

1. Inloggning till webbplatsen Promapp företag som administratör. 

1. På menyn längst upp klickar du på **Admin**. 
   
    ![Azure AD enkel inloggning][12]

1. Klicka på **Konfigurera**. 
   
    ![Azure AD enkel inloggning][13]

1. På den **Security** dialogrutan utför följande steg:
   
    ![Azure AD enkel inloggning][14]
    
    a. Klistra in **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen till den **SSO-inloggnings-URL** textrutan.
    
    b. Som **SSO - läge för enkel inloggning**väljer **valfritt**, och klicka sedan på **spara**.

    > [!NOTE]
    > **Valfritt** läge är endast för testning. När du är nöjd med konfigurationen, Välj **krävs** läge för att tillämpa alla användare att autentisera med hjälp av Azure AD.

    c. Öppna det nedladdade certifikatet i anteckningar, kopiera certifikatet innehållet utan att den första raden (---**börjar certifikat**---) och den sista raden (---**END CERTIFICATE**---), klistra in den i  **SSO-x.509-certifikat** textrutan och klicka sedan på **spara**.
        
> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/promapp-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/promapp-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/promapp-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/promapp-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-promapp-test-user"></a>Skapa en Promapp testanvändare

Promapp programmet stöder Just-in-Time-etablering. Det innebär att ett användarkonto skapas automatiskt om det behövs vid ett försök att komma åt programmet via åtkomstpanelen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Promapp.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Promapp, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Promapp**.

    ![Konfigurera enkel inloggning](./media/promapp-tutorial/tutorial_promapp_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Att testa ditt program i **SP** initierade läge, måste du initiera autentisering från webbplatsen Promapp. Detta kan göras genom att klicka på knappen Logga in med enkel inloggning på inloggningssidan anger även om **valfritt** läge är aktiverat.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/promapp-tutorial/tutorial_general_01.png
[2]: ./media/promapp-tutorial/tutorial_general_02.png
[3]: ./media/promapp-tutorial/tutorial_general_03.png
[4]: ./media/promapp-tutorial/tutorial_general_04.png
[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png

[100]: ./media/promapp-tutorial/tutorial_general_100.png

[200]: ./media/promapp-tutorial/tutorial_general_200.png
[201]: ./media/promapp-tutorial/tutorial_general_201.png
[202]: ./media/promapp-tutorial/tutorial_general_202.png
[203]: ./media/promapp-tutorial/tutorial_general_203.png

