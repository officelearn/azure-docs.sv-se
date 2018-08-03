---
title: 'Självstudier: Azure Active Directory-integration med LogicMonitor | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LogicMonitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 36d1229de6eb71a41eeea895e16e3757c800fc16
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429088"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Självstudier: Azure Active Directory-integration med LogicMonitor

I den här självstudien får du lära dig hur du integrerar LogicMonitor med Azure Active Directory (AD Azure).

Integrera LogicMonitor med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till LogicMonitor
- Du kan aktivera användarna att automatiskt få loggat in på LogicMonitor (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med LogicMonitor, behöver du följande objekt:

- En Azure AD-prenumeration
- En LogicMonitor enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till LogicMonitor från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-logicmonitor-from-the-gallery"></a>Att lägga till LogicMonitor från galleriet
För att konfigurera integrering av LogicMonitor i Azure AD, som du behöver lägga till LogicMonitor från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till LogicMonitor från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **LogicMonitor**.

    ![Skapa en Azure AD-användare för testning](./media/logicmonitor-tutorial/tutorial_logicmonitor_search.png)

1. I resultatpanelen väljer **LogicMonitor**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/logicmonitor-tutorial/tutorial_logicmonitor_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med LogicMonitor baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i LogicMonitor är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i LogicMonitor upprättas.

I LogicMonitor, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med LogicMonitor, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare LogicMonitor](#creating-a-logicmonitor-test-user)**  – du har en motsvarighet för Britta Simon i LogicMonitor som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt LogicMonitor program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med LogicMonitor:**

1. I Azure-portalen på den **LogicMonitor** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/logicmonitor-tutorial/tutorial_logicmonitor_samlbase.png)

1. På den **LogicMonitor domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/logicmonitor-tutorial/tutorial_logicmonitor_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.logicmonitor.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.logicmonitor.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [LogicMonitor klienten supportteamet](https://www.logicmonitor.com/contact/) att hämta dessa värden. 
 


1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/logicmonitor-tutorial/tutorial_logicmonitor_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/logicmonitor-tutorial/tutorial_general_400.png)

1. Logga in på din **LogicMonitor** företagets plats som administratör.

1. Klicka på menyn längst upp **inställningar**.
   
   ![Inställningar för](./media/logicmonitor-tutorial/ic790052.png "inställningar")

1. I funka navigeringen till vänster klickar du på **enkel inloggning**
   
   ![Enkel inloggning](./media/logicmonitor-tutorial/ic790053.png "enkel inloggning")

1. I den **inställningar för enkel inloggning (SSO)** avsnittet, utför följande steg:
   
   ![Enkel inloggning inställningar](./media/logicmonitor-tutorial/ic790054.png "enkel inloggnings-inställningar")
   
   a. Välj **aktivera enkel inloggning**.

   b. Som **standard rolltilldelning**väljer **readonly**.
   
   c. Öppna den hämta metadatafilen i anteckningar och klistra in innehållet i filen till den **identitet providern Metadata** textrutan.
   
   d. Klicka på **spara ändringar**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/logicmonitor-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/logicmonitor-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/logicmonitor-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/logicmonitor-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-logicmonitor-test-user"></a>Skapa en LogicMonitor testanvändare

För AAD-användare för att kunna logga in, måste de etableras till programmet LogicMonitor med deras Azure Active Directory-användarnamn.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på webbplatsen LogicMonitor företag som administratör.

1. Klicka på menyn längst upp **inställningar**, och klicka sedan på **roller och användare**.
   
   ![Roller och användare](./media/logicmonitor-tutorial/ic790056.png "roller och användare")

1. Klicka på **Lägg till**.

1. I den **Lägg till ett konto** avsnittet, utför följande steg:
   
   ![Lägg till ett konto](./media/logicmonitor-tutorial/ic790057.png "Lägg till ett konto")
   
   a. Skriv den **användarnamn**, **e-post**, **lösenord**, och **ange lösenordet** värden för den Azure Active Directory-användare som du vill etablera i den relaterade textrutor.
   
   b. Välj **roller**, **Visa behörigheter**, och **Status**.
   
   c. Klicka på **skicka**.

>[!NOTE]
>Du kan använda alla andra LogicMonitor användare konto verktyg för att skapa eller API: er som tillhandahålls av LogicMonitor för att etablera Azure Active Directory användarkonton. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LogicMonitor.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon LogicMonitor, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **LogicMonitor**.

    ![Konfigurera enkel inloggning](./media/logicmonitor-tutorial/tutorial_logicmonitor_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.
 
När du klickar på panelen LogicMonitor i åtkomstpanelen du bör få automatiskt loggat in på ditt LogicMonitor program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/logicmonitor-tutorial/tutorial_general_01.png
[2]: ./media/logicmonitor-tutorial/tutorial_general_02.png
[3]: ./media/logicmonitor-tutorial/tutorial_general_03.png
[4]: ./media/logicmonitor-tutorial/tutorial_general_04.png

[100]: ./media/logicmonitor-tutorial/tutorial_general_100.png

[200]: ./media/logicmonitor-tutorial/tutorial_general_200.png
[201]: ./media/logicmonitor-tutorial/tutorial_general_201.png
[202]: ./media/logicmonitor-tutorial/tutorial_general_202.png
[203]: ./media/logicmonitor-tutorial/tutorial_general_203.png

