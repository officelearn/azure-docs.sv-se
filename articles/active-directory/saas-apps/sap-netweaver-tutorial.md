---
title: 'Självstudier: Azure Active Directory-integrering med SAP NetWeaver | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: jeedes
ms.openlocfilehash: b773380a21e0c47a1e1519e592aa0ddd5e6388fa
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421958"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Självstudier: Azure Active Directory-integration med SAP NetWeaver

I den här självstudien får du lära dig hur du integrerar SAP NetWeaver med Azure Active Directory (AD Azure).

Integrera SAP NetWeaver med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SAP NetWeaver
- Du kan aktivera användarna att automatiskt få loggat in på SAP NetWeaver (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).


## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SAP NetWeaver, behöver du följande objekt:

- En Azure AD-prenumeration
- En SAP NetWeaver enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till SAP NetWeaver från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sap-netweaver-from-the-gallery"></a>Att lägga till SAP NetWeaver från galleriet
För att konfigurera integrering av SAP NetWeaver i Azure AD, som du behöver lägga till SAP NetWeaver från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SAP NetWeaver från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **SAP NetWeaver**.

    ![Skapa en Azure AD-användare för testning](./media/sap-netweaver-tutorial/tutorial_sapnetweaver_search.png)

1. I resultatpanelen väljer **SAP NetWeaver**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/sap-netweaver-tutorial/tutorial_sapnetweaver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SAP NetWeaver-baserade på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i SAP NetWeaver till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i SAP NetWeaver upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i SAP NetWeaver.

Om du vill konfigurera och testa Azure AD enkel inloggning med SAP NetWeaver, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare för SAP NetWeaver](#creating-an-sap-netweaver-test-user)**  – du har en motsvarighet för Britta Simon i SAP NetWeaver som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt SAP NetWeaver-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SAP NetWeaver:**

1. I Azure-portalen på den **SAP NetWeaver** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/sap-netweaver-tutorial/tutorial_sapnetweaver_samlbase.png)

1. På den **SAP NetWeaver domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/sap-netweaver-tutorial/tutorial_sapnetweaver_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<your company instance of SAP NetWeaver>`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<your company instance of SAP NetWeaver>`

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<your company instance of SAP NetWeaver>/sap/saml2/sp/acs/100`
     
    > [!NOTE] 
    > Dessa värden är inte verkligt. Uppdatera dessa värden med den faktiska identifierare och svars-URL och inloggnings-URL. Här föreslår vi att du för att använda det unika värdet av strängen i identifierare. Kontakta [SAP NetWeaver klienten supportteamet](https://www.sap.com/support.html) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/sap-netweaver-tutorial/tutorial_sapnetweaver_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/sap-netweaver-tutorial/tutorial_general_400.png)
    
1. På den **SAP NetWeaver-konfigurationen** klickar du på **konfigurera SAP NetWeaver** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/sap-netweaver-tutorial/tutorial_sapnetweaver_configure.png) 

1. Att konfigurera enkel inloggning på **SAP NetWeaver** sida, som du behöver skicka de hämtade **XML-Metadata för** och **SAML entitets-ID** till [stöd för SAP NetWeaver](https://www.sap.com/support.html). 

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/sap-netweaver-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/sap-netweaver-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/sap-netweaver-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/sap-netweaver-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **Britta Simon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** för Britta Simon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-sap-netweaver-test-user"></a>Skapa en testanvändare för SAP NetWeaver

I det här avsnittet skapar du en användare som kallas Britta Simon i SAP NetWeaver. Arbeta med din [stöd för SAP NetWeaver](https://www.sap.com/support.html) att lägga till användare i SAP NetWeaver-plattformen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP NetWeaver.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon SAP NetWeaver, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **SAP NetWeaver**.

    ![Konfigurera enkel inloggning](./media/sap-netweaver-tutorial/tutorial_sapnetweaver_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen SAP NetWeaver på åtkomstpanelen, du bör få automatiskt loggat in på ditt SAP NetWeaver-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sap-netweaver-tutorial/tutorial_general_01.png
[2]: ./media/sap-netweaver-tutorial/tutorial_general_02.png
[3]: ./media/sap-netweaver-tutorial/tutorial_general_03.png
[4]: ./media/sap-netweaver-tutorial/tutorial_general_04.png

[100]: ./media/sap-netweaver-tutorial/tutorial_general_100.png

[200]: ./media/sap-netweaver-tutorial/tutorial_general_200.png
[201]: ./media/sap-netweaver-tutorial/tutorial_general_201.png
[202]: ./media/sap-netweaver-tutorial/tutorial_general_202.png
[203]: ./media/sap-netweaver-tutorial/tutorial_general_203.png

