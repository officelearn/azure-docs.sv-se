---
title: 'Självstudier: Azure Active Directory-integrering med Pantheon | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Pantheon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d2c965d1-666f-44c2-b08f-b73163096374
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 0bfe96bb169e54b1ed6c79390b4aea714da8ee69
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814587"
---
# <a name="tutorial-azure-active-directory-integration-with-pantheon"></a>Självstudier: Azure Active Directory-integrering med Pantheon

I den här självstudien får du lära dig hur du integrerar Pantheon med Azure Active Directory (AD Azure).

Integrera Pantheon med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Pantheon
- Du kan aktivera användarna att automatiskt få loggat in på Pantheon (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Pantheon, behöver du följande objekt:

- En Azure AD-prenumeration
- En Pantheon enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Pantheon från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-pantheon-from-the-gallery"></a>Att lägga till Pantheon från galleriet
För att konfigurera integrering av Pantheon i Azure AD, som du behöver lägga till Pantheon från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Pantheon från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Pantheon**.

    ![Skapa en Azure AD-användare för testning](./media/pantheon-tutorial/tutorial_pantheon_search.png)

1. I resultatpanelen väljer **Pantheon**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/pantheon-tutorial/tutorial_pantheon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Pantheon baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Pantheon är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Pantheon upprättas.

I Pantheon, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Pantheon, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Pantheon](#creating-a-pantheon-test-user)**  – du har en motsvarighet för Britta Simon i Pantheon som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Pantheon program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Pantheon:**

1. I Azure-portalen på den **Pantheon** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/pantheon-tutorial/tutorial_pantheon_samlbase.png)

1. På den **Pantheon domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/pantheon-tutorial/tutorial_pantheon_url.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `urn:auth0:pantheon:<orgname>-SSO`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://pantheon.auth0.com/login/callback?connection=<orgname>-SSO`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Pantheon supportteamet](https://pantheon.io/docs/getting-support/) att hämta dessa värden.

1. Pantheon program som förväntar SAML-försäkran i specifika format, vilket kräver att du ställer in värdet på attributet UserIdentifier med användarens e-postadress. Som standard använder Azure AD UserPrincipalName för UserIdentifier attributet. Men för lyckad integration måste du justera det här värdet till matchar användarens e-postadress. Integrationen fungerar bara när du har gjort korrekt mappning.

    ![Konfigurera enkel inloggning](./media/pantheon-tutorial/tutorial_attribute.png)    


1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/pantheon-tutorial/tutorial_pantheon_certificate.png)

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/pantheon-tutorial/tutorial_general_400.png)

1. På den **Pantheon Configuration** klickar du på **konfigurera Pantheon** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/pantheon-tutorial/tutorial_pantheon_configure.png) 

1. Att konfigurera enkel inloggning på **Pantheon** sida, som du behöver skicka de hämtade **certifikat** och **SAML enkel inloggning för tjänst-URL** till [Pantheon supportteam](https://pantheon.io/docs/getting-support/).

     > [!Note]
     > Du måste också ange information för e-domänerna och datum tid när du vill aktivera den här anslutningen. Du hittar mer information om den från [här](https://pantheon.io/docs/sso-organizations/)

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/pantheon-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/pantheon-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/pantheon-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/pantheon-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-pantheon-test-user"></a>Skapa en Pantheon testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Pantheon. Följ de stegen nedan för att lägga till användaren i Pantheon. 

>[!NOTE] 
>För enkel inloggning ska fungera användare måste du först skapas i Pantheon.

1. Logga in på Pantheon med autentiseringsuppgifter som administratör.

1. Gå till **organisation** instrumentpanelssidan.
 
1. Klicka på **Personer**.

1. Klicka på **Lägg till användare**.

1. Ange användarens e-postadress.

1. Välj användarens roll.

1. Klicka på **Lägg till användare**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Pantheon.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Pantheon, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Pantheon**.

    ![Konfigurera enkel inloggning](./media/pantheon-tutorial/tutorial_pantheon_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Pantheon i åtkomstpanelen du bör få automatiskt loggat in på ditt Pantheon program.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/pantheon-tutorial/tutorial_general_01.png
[2]: ./media/pantheon-tutorial/tutorial_general_02.png
[3]: ./media/pantheon-tutorial/tutorial_general_03.png
[4]: ./media/pantheon-tutorial/tutorial_general_04.png

[100]: ./media/pantheon-tutorial/tutorial_general_100.png

[200]: ./media/pantheon-tutorial/tutorial_general_200.png
[201]: ./media/pantheon-tutorial/tutorial_general_201.png
[202]: ./media/pantheon-tutorial/tutorial_general_202.png
[203]: ./media/pantheon-tutorial/tutorial_general_203.png

