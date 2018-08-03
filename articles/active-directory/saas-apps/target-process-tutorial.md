---
title: 'Självstudier: Azure Active Directory-integration med TargetProcess | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TargetProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: b79fa31aed1a264ba52675857c9a80dc65746173
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434096"
---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>Självstudier: Azure Active Directory-integration med TargetProcess

I den här självstudien får du lära dig hur du integrerar TargetProcess med Azure Active Directory (AD Azure).

Integrera TargetProcess med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till TargetProcess
- Du kan aktivera användarna att automatiskt få loggat in på TargetProcess (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med TargetProcess, behöver du följande objekt:

- En Azure AD-prenumeration
- En TargetProcess enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägg till TargetProcess från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-targetprocess-from-the-gallery"></a>Lägg till TargetProcess från galleriet
För att konfigurera integrering av TargetProcess i Azure AD, som du behöver lägga till TargetProcess från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till TargetProcess från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **TargetProcess**väljer **TargetProcess** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Lägg till TargetProcess från galleriet](./media/target-process-tutorial/tutorial_target-process_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med TargetProcess baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i TargetProcess är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i TargetProcess upprättas.

I TargetProcess, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med TargetProcess, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare TargetProcess](#create-a-targetprocess-test-user)**  – du har en motsvarighet för Britta Simon i TargetProcess som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt TargetProcess program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med TargetProcess:**

1. I Azure-portalen på den **TargetProcess** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![SAML-baserad inloggning](./media/target-process-tutorial/tutorial_target-process_samlbase.png)

1. På den **TargetProcess domän och URL: er** avsnittet, utför följande steg:

    ![TargetProcess domän och URL: er](./media/target-process-tutorial/tutorial_target-process_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.tpondemand.com/`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.tpondemand.com/`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [TargetProcess klienten supportteamet](mailto:support@targetprocess.com) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Avsnittet för SAML-signeringscertifikat](./media/target-process-tutorial/tutorial_target-process_certificate.png) 

1. Klicka på **spara** knappen.

    ![Knappen Spara](./media/target-process-tutorial/tutorial_general_400.png)

1. På den **TargetProcess Configuration** klickar du på **konfigurera TargetProcess** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![TargetProcess konfigurationsavsnittet](./media/target-process-tutorial/tutorial_target-process_configure.png) 

1. Inloggning till TargetProcess-programmet som en administratör.

1. Klicka på menyn längst upp **installationsprogrammet**.
   
    ![Konfiguration](./media/target-process-tutorial/tutorial_target_process_05.png)

1. Klicka på **inställningar**.
   
    ![Inställningar](./media/target-process-tutorial/tutorial_target_process_06.png) 

1. Klicka på **enkel inloggning**.
   
    ![Klicka på enkel inloggning](./media/target-process-tutorial/tutorial_target_process_07.png) 

1. Utför följande steg på inställningsdialogrutan enkel inloggning:
   
    ![Konfigurera enkel inloggning](./media/target-process-tutorial/tutorial_target_process_08.png)
    
    a. Klicka på **aktivera enkel inloggning**.
    
    b. I **inloggnings-URL** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    c. Öppna din nedladdade certifikatet i anteckningar, kopiera innehållet och klistra in den i den **certifikat** textrutan.
    
    d. Klicka på **aktivera JIT etablering**.

    e. Klicka på **Spara**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/target-process-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Att visa en lista över användare](./media/target-process-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Knappen Lägg till](./media/target-process-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Användare](./media/target-process-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-targetprocess-test-user"></a>Skapa en TargetProcess testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i TargetProcess.

TargetProcess har stöd för just-in-time-etablering. Du har redan har aktiverat det i [konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on).

Det finns inga uppgift åt dig i det här avsnittet.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TargetProcess.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon TargetProcess, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **TargetProcess**.

    ![TargetProcess i applistan](./media/target-process-tutorial/tutorial_target-process_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen TargetProcess i åtkomstpanelen du bör få automatiskt loggat in på ditt TargetProcess program. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/target-process-tutorial/tutorial_general_01.png
[2]: ./media/target-process-tutorial/tutorial_general_02.png
[3]: ./media/target-process-tutorial/tutorial_general_03.png
[4]: ./media/target-process-tutorial/tutorial_general_04.png

[100]: ./media/target-process-tutorial/tutorial_general_100.png

[200]: ./media/target-process-tutorial/tutorial_general_200.png
[201]: ./media/target-process-tutorial/tutorial_general_201.png
[202]: ./media/target-process-tutorial/tutorial_general_202.png
[203]: ./media/target-process-tutorial/tutorial_general_203.png

