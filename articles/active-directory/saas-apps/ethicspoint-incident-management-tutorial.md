---
title: 'Självstudier: Azure Active Directory-integrering med EthicsPoint Incident Management (EPIM) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EthicsPoint Incident Management (EPIM).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8cb31a4c-9309-469b-93ac-daf0d3c7a3e6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: c72ed655166dc1fe8045f5b9fdc7221cdf24d567
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52851002"
---
# <a name="tutorial-azure-active-directory-integration-with-ethicspoint-incident-management-epim"></a>Självstudier: Azure Active Directory-integrering med EthicsPoint Incident Management (EPIM)

I den här självstudien får du lära dig hur du integrerar EthicsPoint Incident Management (EPIM) med Azure Active Directory (AD Azure).

Integrera EthicsPoint Incident Management (EPIM) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till EthicsPoint Incident Management (EPIM)
- Du kan aktivera användarna att automatiskt få loggat in till EthicsPoint Incident Management (EPIM) (enkel inloggning) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med EthicsPoint Incident Management (EPIM), behöver du följande objekt:

- En Azure AD-prenumeration
- En EthicsPoint Incident Management (EPIM) enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till EthicsPoint Incident Management (EPIM) från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-ethicspoint-incident-management-epim-from-the-gallery"></a>Att lägga till EthicsPoint Incident Management (EPIM) från galleriet
För att konfigurera integrering av EthicsPoint Incident Management (EPIM) till Azure AD, som du behöver lägga till EthicsPoint Incident Management (EPIM) från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till EthicsPoint Incident Management (EPIM) från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **EthicsPoint Incident Management (EPIM)**.

    ![Skapa en Azure AD-användare för testning](./media/ethicspoint-incident-management-tutorial/tutorial_ethicspoint_search.png)

1. I resultatpanelen väljer **EthicsPoint Incident Management (EPIM)**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/ethicspoint-incident-management-tutorial/tutorial_ethicspoint_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med EthicsPoint Incident Management (EPIM) baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i EthicsPoint Incident Management (EPIM) till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i EthicsPoint Incident Management (EPIM) upprättas.

I EthicsPoint Incident Management (EPIM), tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med EthicsPoint Incident Management (EPIM), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare EthicsPoint Incident Management (EPIM)](#creating-a-ethicspoint-incident-management-epim-test-user)**  – du har en motsvarighet för Britta Simon i EthicsPoint Incident Management (EPIM) och som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för EthicsPoint Incident Management (EPIM).

**Utför följande steg för att konfigurera Azure AD enkel inloggning med EthicsPoint Incident Management (EPIM):**

1. I Azure-portalen på den **EthicsPoint Incident Management (EPIM)** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/ethicspoint-incident-management-tutorial/tutorial_ethicspoint_samlbase.png)

1. På den **EthicsPoint Incident Management (EPIM)-domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/ethicspoint-incident-management-tutorial/tutorial_ethicspoint_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster:
    | |
    |--|
    | `https://<companyname>.navexglobal.com`|
    | `https://<companyname>.ethicspointvp.com`|

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.navexglobal.com/adfs/services/trust`

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<servername>.navexglobal.com/adfs/ls/`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska svars-URL, identifierare och inloggnings-URL. Kontakta [EthicsPoint Incident Management (EPIM) klient-supportteamet](https://www.navexglobal.com/company/contact-us) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/ethicspoint-incident-management-tutorial/tutorial_ethicspoint_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/ethicspoint-incident-management-tutorial/tutorial_general_400.png)
    
1. Att konfigurera enkel inloggning på **EthicsPoint Incident Management (EPIM)** sida, som du behöver skicka de hämtade **XML-Metadata för** till [EthicsPoint Incident Management (EPIM) support-teamet ](https://www.navexglobal.com/company/contact-us).

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/ethicspoint-incident-management-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/ethicspoint-incident-management-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/ethicspoint-incident-management-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/ethicspoint-incident-management-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-ethicspoint-incident-management-epim-test-user"></a>Skapa en testanvändare EthicsPoint Incident Management (EPIM)

I det här avsnittet skapar du en användare som kallas Britta Simon i EthicsPoint Incident Management (EPIM). Kontakta [EthicsPoint Incident Management (EPIM) supportteamet](https://www.navexglobal.com/company/contact-us) att lägga till användare i EthicsPoint Incident Management (EPIM)-plattformen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att ge åtkomst till EthicsPoint Incident Management (EPIM).

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon till EthicsPoint Incident Management (EPIM), utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **EthicsPoint Incident Management (EPIM)**.

    ![Konfigurera enkel inloggning](./media/ethicspoint-incident-management-tutorial/tutorial_ethicspoint_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.
När du klickar på panelen EthicsPoint Incident Management (EPIM) i åtkomstpanelen du bör få automatiskt loggat in på programmets EthicsPoint Incident Management (EPIM).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ethicspoint-incident-management-tutorial/tutorial_general_01.png
[2]: ./media/ethicspoint-incident-management-tutorial/tutorial_general_02.png
[3]: ./media/ethicspoint-incident-management-tutorial/tutorial_general_03.png
[4]: ./media/ethicspoint-incident-management-tutorial/tutorial_general_04.png

[100]: ./media/ethicspoint-incident-management-tutorial/tutorial_general_100.png

[200]: ./media/ethicspoint-incident-management-tutorial/tutorial_general_200.png
[201]: ./media/ethicspoint-incident-management-tutorial/tutorial_general_201.png
[202]: ./media/ethicspoint-incident-management-tutorial/tutorial_general_202.png
[203]: ./media/ethicspoint-incident-management-tutorial/tutorial_general_203.png

