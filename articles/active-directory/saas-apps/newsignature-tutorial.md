---
title: 'Självstudier: Azure Active Directory-integrering med Cloud Management Portal för Microsoft Azure | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cloud Management Portal för Microsoft Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4ea9f47c-25ca-42b0-a878-9e7aa6f34973
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 0cd14a308758701e207e0b1ee6d3591c4b0347bd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427643"
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Självstudier: Azure Active Directory-integrering med Cloud Management Portal för Microsoft Azure

I den här självstudien får du lära dig hur du integrerar Cloud Management Portal för Microsoft Azure med Azure Active Directory (AD Azure).

Integrera Cloud Management Portal för Microsoft Azure med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Cloud Management Portal för Microsoft Azure
- Du kan aktivera användarna att automatiskt få loggat in på Cloud Management Portal för Microsoft Azure (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Cloud Management Portal för Microsoft Azure, behöver du följande objekt:

- En Azure AD-prenumeration
- En Cloud Management Portal för Microsoft Azure enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Cloud Management Portal för Microsoft Azure från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Att lägga till Cloud Management Portal för Microsoft Azure från galleriet
Om du vill konfigurera integreringen av Cloud Management Portal för Microsoft Azure till Azure AD, som du behöver lägga till Cloud Management Portal för Microsoft Azure från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Cloud Management Portal för Microsoft Azure från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **Cloud Management Portal för Microsoft Azure**.

    ![Skapa en Azure AD-användare för testning](./media/newsignature-tutorial/tutorial_newsignature_search.png)

1. I resultatpanelen väljer **Cloud Management Portal för Microsoft Azure**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/newsignature-tutorial/tutorial_newsignature_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Cloud Management Portal för Microsoft Azure baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Cloud Management Portal för Microsoft Azure är att en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Cloud Management Portal för Microsoft Azure upprättas.

I Cloud Management Portal för Microsoft Azure, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Cloud Management Portal för Microsoft Azure, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en Cloud Management Portal för Microsoft Azure testanvändare](#creating-a-cloud-management-portal-for-microsoft-azure-test-user)**  – du har en motsvarighet för Britta Simon i Cloud Management Portal för Microsoft Azure som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din Cloud Management-Portal för Microsoft Azure-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Cloud Management Portal för Microsoft Azure:**

1. I Azure-portalen på den **Cloud Management Portal för Microsoft Azure** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/newsignature-tutorial/tutorial_newsignature_samlbase.png)

1. På den **Cloud Management Portal för Microsoft Azure-domänen och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/newsignature-tutorial/tutorial_newsignature_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: 
    
    | |
    |--|
    | `https://portal.newsignature.com/<instancename>` |   
    | `https://portal.igcm.com/<instancename>` |
    
    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com` |
    | `https://<subdomain>.newsignature.com` |

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com/<instancename>` |
    | `https://<subdomain>.newsignature.com` |
    | `https://<subdomain>.newsignature.com/<instancename>` |

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska inloggnings-URL, identifierare och svars-URL. Kontakta [Cloud Management Portal för Microsoft Azure-Client-supportteamet](mailto:jczernuszka@newsignature.com) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/newsignature-tutorial/tutorial_newsignature_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/newsignature-tutorial/tutorial_general_400.png)

1. På den **Cloud Management Portal för Microsoft Azure Configuration** klickar du på **konfigurera Cloud Management Portal för Microsoft Azure** att öppna **konfigurera inloggning** fönstret. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/newsignature-tutorial/tutorial_newsignature_configure.png) 

1. Att konfigurera enkel inloggning på **Cloud Management Portal för Microsoft Azure** sida, som du behöver skicka de hämtade **certifikat**, **URL: en för utloggning**,  **Enkel inloggning för tjänsten URL för SAML** och **SAML entitets-ID** till [Cloud Management Portal för Microsoft Azure-supportteamet](mailto:jczernuszka@newsignature.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/newsignature-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/newsignature-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/newsignature-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/newsignature-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-cloud-management-portal-for-microsoft-azure-test-user"></a>Skapa en Cloud Management Portal för Microsoft Azure testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i hanteringsportalen för molnet för Microsoft Azure. Kontakta [Cloud Management Portal för Microsoft Azure-supportteamet](mailto:jczernuszka@newsignature.com) att lägga till användare i hanteringsportalen för molnet för Microsoft Azure-konto.


### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Cloud Management Portal för Microsoft Azure.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Cloud Management Portal för Microsoft Azure, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Cloud Management Portal för Microsoft Azure**.

    ![Konfigurera enkel inloggning](./media/newsignature-tutorial/tutorial_newsignature_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.
När du klickar på Cloud Management Portal för Microsoft Azure-panel i åtkomstpanelen du bör få automatiskt loggat in på din Cloud Management-Portal för Microsoft Azure-program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/newsignature-tutorial/tutorial_general_01.png
[2]: ./media/newsignature-tutorial/tutorial_general_02.png
[3]: ./media/newsignature-tutorial/tutorial_general_03.png
[4]: ./media/newsignature-tutorial/tutorial_general_04.png

[100]: ./media/newsignature-tutorial/tutorial_general_100.png

[200]: ./media/newsignature-tutorial/tutorial_general_200.png
[201]: ./media/newsignature-tutorial/tutorial_general_201.png
[202]: ./media/newsignature-tutorial/tutorial_general_202.png
[203]: ./media/newsignature-tutorial/tutorial_general_203.png

