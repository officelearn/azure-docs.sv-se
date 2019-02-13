---
title: 'Självstudier: Azure Active Directory-integrering med Soonr arbetsplats | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Soonr arbetsplats.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: b75f5f00-ea8b-4850-ae2e-134e5d678d97
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e81ff0f1d92fcf644287fd0cf417a245581bfeb7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210942"
---
# <a name="tutorial-azure-active-directory-integration-with-soonr-workplace"></a>Självstudier: Azure Active Directory-integrering med Soonr arbetsplats

I den här självstudien får du lära dig hur du integrerar Soonr arbetsplats med Azure Active Directory (AD Azure).

Integrera Soonr arbetsplats med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Soonr arbetsplats
- Du kan aktivera användarna att automatiskt få loggat in på Soonr arbetsplats (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Soonr arbetsplats, behöver du följande objekt:

- En Azure AD-prenumeration
- En Soonr arbetsplats enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Soonr arbetsplats från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-soonr-workplace-from-the-gallery"></a>Att lägga till Soonr arbetsplats från galleriet
För att konfigurera integrering av Soonr arbetsplats i Azure AD, som du behöver lägga till Soonr arbetsplats från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Soonr arbetsplats från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Soonr arbetsplats**.

    ![Skapa en Azure AD-användare för testning](./media/soonr-tutorial/tutorial_soonr_search.png)

1. I resultatpanelen väljer **Soonr arbetsplats**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/soonr-tutorial/tutorial_soonr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Soonr arbetsplats utifrån en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Soonr arbetsplats är till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Soonr arbetsplatsen upprättas.

I Soonr arbetsplats, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Soonr arbetsplats, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Soonr arbetsplats](#creating-a-soonr-workplace-test-user)**  – du har en motsvarighet för Britta Simon i Soonr arbetsplats som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Soonr arbetsplats.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Soonr arbetsplats:**

1. I Azure-portalen på den **Soonr arbetsplats** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/soonr-tutorial/tutorial_soonr_samlbase.png)

1. På den **Soonr arbetsplats domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/soonr-tutorial/tutorial_soonr_url.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<servername>.soonr.com/singlesignon/saml/metadata`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<servername>.soonr.com/singlesignon/saml/SSO`

1. På den **Soonr arbetsplats domän och URL: er** om du vill konfigurera programmet i **SP initierade läge**, utför följande steg:
    
    ![Konfigurera enkel inloggning](./media/soonr-tutorial/tutorial_soonr_url1.png)

    a. Klicka på den **visa avancerade URL-inställningar**.

    b. I den **inloggning på URL: en** textrutan anger du ett URL med hjälp av följande mönster: `https://<servername>.soonr.com/singlesignon/saml/SSO`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren logga URL och svars-URL. Kontakta [Soonr arbetsplats supportteamet](https://awp.autotask.net/help/) att hämta dessa värden.
 
1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/soonr-tutorial/tutorial_soonr_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/soonr-tutorial/tutorial_general_400.png)

1. På den **Soonr arbetsplats Configuration** klickar du på **konfigurera Soonr arbetsplats** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/soonr-tutorial/tutorial_soonr_configure.png) 

1. Att konfigurera enkel inloggning på **Soonr arbetsplats** sida, som du behöver skicka de hämtade **XML-Metadata för**, **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** att [Soonr arbetsplats supportteamet](https://awp.autotask.net/help/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

    >[!Note]
    >Om du behöver hjälp med att konfigurera Autotask Workplace läser du [den här sidan](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) för att få hjälp med ditt Workplace-konto.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/soonr-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/soonr-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/soonr-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/soonr-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-soonr-workplace-test-user"></a>Skapa en testanvändare Soonr arbetsplats

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon på Soonr arbetsplats. Arbeta med [Soonr arbetsplats supportteamet](https://awp.autotask.net/help/) att skapa en användare i plattformen. Du kan öka supportärende med Soonr från <a href="https://na01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fsoonr.com%2FAWPHelp%2FContent%2F0_HOME%2FSupport_for_End_Clients.htm&data=01%7C01%7Cv-saikra%40microsoft.com%7Ccbb4367ab09b4dacaac408d3eebe3f42%7C72f988bf86f141af91ab2d7cd011db47%7C1&sdata=FB92qtE6m%2Fd8yox7AnL2f1h%2FGXwSkma9x9H8Pz0955M%3D&reserved=0/">här</a>.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Soonr arbetsplats.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Soonr arbetsplats, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Soonr arbetsplats**.

    ![Konfigurera enkel inloggning](./media/soonr-tutorial/tutorial_soonr_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.  

När du klickar på panelen Soonr arbetsplats i åtkomstpanelen du bör få automatiskt loggat in på programmets Soonr arbetsplats.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/soonr-tutorial/tutorial_general_01.png
[2]: ./media/soonr-tutorial/tutorial_general_02.png
[3]: ./media/soonr-tutorial/tutorial_general_03.png
[4]: ./media/soonr-tutorial/tutorial_general_04.png

[100]: ./media/soonr-tutorial/tutorial_general_100.png

[200]: ./media/soonr-tutorial/tutorial_general_200.png
[201]: ./media/soonr-tutorial/tutorial_general_201.png
[202]: ./media/soonr-tutorial/tutorial_general_202.png
[203]: ./media/soonr-tutorial/tutorial_general_203.png

