---
title: 'Självstudier: Azure Active Directory-integrering med svart tavla Läs - Shibboleth | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och svart tavla Läs - Shibboleth.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: e435cbb4-c0f0-400e-943c-5c923fa8ddf2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: jeedes
ms.openlocfilehash: c285bac1c975aa502705761a12972ddaa54f1484
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55157032"
---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn---shibboleth"></a>Självstudier: Azure Active Directory-integrering med svart tavla Läs - Shibboleth

I den här självstudien får du lära dig hur du integrerar svart tavla Läs - Shibboleth med Azure Active Directory (AD Azure).

Integrera svart tavla Läs - Shibboleth med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till svart tavla Läs - Shibboleth
- Du kan aktivera användarna att automatiskt få loggat in på svart tavla Läs - Shibboleth (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med svart tavla Läs - Shibboleth, behöver du följande objekt:

- En Azure AD-prenumeration
- En svart tavla Läs - Shibboleth enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till svart tavla Läs - Shibboleth från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-blackboard-learn---shibboleth-from-the-gallery"></a>Att lägga till svart tavla Läs - Shibboleth från galleriet
Om du vill konfigurera integreringen av svart tavla Läs - Shibboleth till Azure AD som du behöver lägga till svart tavla Läs - Shibboleth från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till svart tavla Läs - Shibboleth från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **svart tavla Läs - Shibboleth**.

    ![Skapa en Azure AD-användare för testning](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_search.png)

1. I resultatpanelen väljer **svart tavla Läs - Shibboleth**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med svart tavla Läs, Shibboleth baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD behöver du veta vilka motsvarande användaren i svart tavla Läs - Shibboleth är att en användare i Azure AD för enkel inloggning ska fungera. Med andra ord en länk relationen mellan en Azure AD-användare och relaterade användaren i svart tavla Läs - Shibboleth måste upprättas.

I svart tavla Läs - Shibboleth, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med svart tavla Läs - Shibboleth, måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en svart tavla Läs - Shibboleth testanvändare](#creating-a-blackboard-learn---shibboleth-test-user)**  – du har en motsvarighet för Britta Simon i svart tavla Läs - Shibboleth som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktiverar Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din svart tavla Läs - Shibboleth-program.

**Om du vill konfigurera Azure AD utför enkel inloggning med svart tavla Läs - Shibboleth, du följande steg:**

1. I Azure-portalen på den **svart tavla Läs - Shibboleth** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_samlbase.png)

1. På den **svart tavla Läs - Shibboleth domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/Login`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<yourblackoardlearnserver>.blackboardlearn.com/shibboleth-sp`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/SAML2/POST`
 
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [svart tavla Läs - Shibboleth klienten supportteamet](https://www.blackboard.com/forms/contact-us_form.aspx) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/blackboard-learn-shibboleth-tutorial/tutorial_general_400.png)
    
1. På den **svart tavla Läs - Shibboleth Configuration** klickar du på **konfigurera svart tavla Läs - Shibboleth** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_configure.png) 

1. Att konfigurera enkel inloggning på **svart tavla Läs - Shibboleth** sida, som du behöver skicka de hämtade **XML-Metadata för** och **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL**  till [svart tavla Läs - Shibboleth-supportteamet](https://www.blackboard.com/forms/contact-us_form.aspx).

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/blackboard-learn-shibboleth-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/blackboard-learn-shibboleth-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/blackboard-learn-shibboleth-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/blackboard-learn-shibboleth-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-blackboard-learn---shibboleth-test-user"></a>Skapa en svart tavla Läs - Shibboleth testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i svart tavla Läs - Shibboleth. Arbeta med din [svart tavla Läs - Shibboleth-supportteamet](https://www.blackboard.com/forms/contact-us_form.aspx) att lägga till användare i svart tavla Läs - Shibboleth-plattformen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till svart tavla Läs - Shibboleth.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon svart tavla Läs - Shibboleth, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **svart tavla Läs - Shibboleth**.

    ![Konfigurera enkel inloggning](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på svart tavla Läs - Shibboleth-panel i åtkomstpanelen, du bör få automatiskt loggat in på ditt svart tavla Läs - Shibboleth-programmet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_01.png
[2]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_02.png
[3]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_03.png
[4]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_04.png

[100]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_100.png

[200]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_200.png
[201]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_201.png
[202]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_202.png
[203]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_203.png

