---
title: 'Självstudier: Azure Active Directory-integrering med Neota Logic Studio | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Neota Logic Studio.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 842605e6-a91d-42cc-a0bb-e23e67173ae2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: jeedes
ms.openlocfilehash: 4de968949e9c043d4a005f23b5bfa86dd87f5b70
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55170258"
---
# <a name="tutorial-azure-active-directory-integration-with-neota-logic-studio"></a>Självstudier: Azure Active Directory-integrering med Neota Logic Studio

I den här självstudien får du lära dig hur du integrerar Neota Logic Studio med Azure Active Directory (AD Azure).

Integrera Neota Logic Studio med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Neota Logic Studio
- Du kan aktivera användarna att automatiskt få loggat in på Neota Logic Studio (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i. [Vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Neota Logic Studio, behöver du följande objekt:

- En Azure AD-prenumeration
- En Neota Logic Studio enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Neota Logic Studio från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-neota-logic-studio-from-the-gallery"></a>Att lägga till Neota Logic Studio från galleriet

Om du vill konfigurera integreringen av Neota Logic Studio till Azure AD, som du behöver lägga till Neota Logic Studio från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Neota Logic Studio från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Neota Logic Studio**.

    ![Skapa en Azure AD-användare för testning](./media/neotalogicstudio-tutorial/tutorial_neotalogicstudio_search.png)

1. I resultatpanelen väljer **Neota Logic Studio**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/neotalogicstudio-tutorial/tutorial_neotalogicstudio_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Neota Logic Studio baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Neota Logic Studio är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Neota Logic Studio upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Neota Logic Studio.

Om du vill konfigurera och testa Azure AD enkel inloggning med Neota Logic Studio, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Neota Logic Studio](#creating-a-neota-logic-studio-test-user)**  – du har en motsvarighet för Britta Simon i Neota Logic Studio som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Neota Logic Studio-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Neota Logic Studio:**

1. I Azure-portalen på den **Neota Logic Studio** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/neotalogicstudio-tutorial/tutorial_neotalogicstudio_samlbase.png)

1. På den **Neota Logic Studio domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/neotalogicstudio-tutorial/tutorial_neotalogicstudio_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<sub domain>.neotalogic.com/a/<sub application>`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<sub domain>.neotalogic.com/wb`

    > [!NOTE] 
    > De här värdena är inte verkliga. Uppdatera dessa värden med faktiska inloggnings-URL och identifierare. Här föreslår vi att du använder det unika värdet för strängen i identifieraren. Kontakta [Neota Logic Studia supportteamet](https://www.neotalogic.com/contact-us/) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/neotalogicstudio-tutorial/tutorial_neotalogicstudio_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/neotalogicstudio-tutorial/tutorial_general_400.png)

1. För att få SSO konfigurerats för ditt program kan kontakta [Neota Logic Studio support](https://www.neotalogic.com/contact-us/) team och ge dem med hämtade **XML-Metadata för** fil.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/neotalogicstudio-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/neotalogicstudio-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/neotalogicstudio-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/neotalogicstudio-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-neota-logic-studio-test-user"></a>Skapa en testanvändare Neota Logic Studio

I det här avsnittet skapar du en användare som kallas Britta Simon i Neota Logic Studio. fungerar med [Neota Logic Studia supportteamet](https://www.neotalogic.com/contact-us/) att lägga till användare i Neota Logic Studio-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Neota Logic Studio.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Neota Logic Studio, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Neota Logic Studio**.

    ![Konfigurera enkel inloggning](./media/neotalogicstudio-tutorial/tutorial_neotalogicstudio_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

Klicka på panelen Neota Logic Studio i åtkomstpanelen, omdirigeras du till organisation inloggnings-sida. Efter genomförd inloggning du kommer att loggat in på ditt Neota Logic Studio-program. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/neotalogicstudio-tutorial/tutorial_general_01.png
[2]: ./media/neotalogicstudio-tutorial/tutorial_general_02.png
[3]: ./media/neotalogicstudio-tutorial/tutorial_general_03.png
[4]: ./media/neotalogicstudio-tutorial/tutorial_general_04.png

[100]: ./media/neotalogicstudio-tutorial/tutorial_general_100.png

[200]: ./media/neotalogicstudio-tutorial/tutorial_general_200.png
[201]: ./media/neotalogicstudio-tutorial/tutorial_general_201.png
[202]: ./media/neotalogicstudio-tutorial/tutorial_general_202.png
[203]: ./media/neotalogicstudio-tutorial/tutorial_general_203.png

