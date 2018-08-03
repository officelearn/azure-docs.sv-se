---
title: 'Självstudier: Azure Active Directory-integration med Nexonia | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Nexonia.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a93b771a-9bc3-444a-bdc0-457f8bb7e780
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: 9349164c4386fb32c717b60f132b902d987fc3a5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421635"
---
# <a name="tutorial-azure-active-directory-integration-with-nexonia"></a>Självstudier: Azure Active Directory-integration med Nexonia

I den här självstudien får du lära dig hur du integrerar Nexonia med Azure Active Directory (AD Azure).

Integrera Nexonia med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Nexonia.
- Du kan aktivera användarna att automatiskt få loggat in på Nexonia (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Nexonia, behöver du följande objekt:

- En Azure AD-prenumeration
- En Nexonia enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Nexonia från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-nexonia-from-the-gallery"></a>Att lägga till Nexonia från galleriet
För att konfigurera integrering av Nexonia i Azure AD, som du behöver lägga till Nexonia från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Nexonia från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Nexonia**väljer **Nexonia** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Nexonia i resultatlistan](./media/nexonia-tutorial/tutorial_nexonia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Nexonia baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Nexonia är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Nexonia upprättas.

I Nexonia, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Nexonia, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Nexonia](#create-a-nexonia-test-user)**  – du har en motsvarighet för Britta Simon i Nexonia som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Nexonia program.

  > [!Note]
   > Om du har problem med i integrationen sedan finns det [länken](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery) för felsökningsguide för. Om du fortfarande inte har hittas lösningen skapar du supportförfrågan via Azure-portalen.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Nexonia:**

1. I Azure-portalen på den **Nexonia** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/nexonia-tutorial/tutorial_nexonia_samlbase.png)

1. På den **Nexonia domän och URL: er** avsnittet, utför följande steg:

    ![Nexonia domän och URL: er med enkel inloggning för information](./media/nexonia-tutorial/tutorial_nexonia_url.png)

    a. I den **identifierare** textrutan, ange ett värde: `Nexonia`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://system.nexonia.com/assistant/saml.do?orgCode=<organizationcode>`

    > [!NOTE] 
    > Svars-URL-värdet är inte verkliga. Uppdatera värdet med faktiska svars-URL. Kontakta [Nexonia supportteamet](https://nexonia.zendesk.com/hc/requests/new) att hämta värdet.
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/nexonia-tutorial/tutorial_nexonia_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/nexonia-tutorial/tutorial_general_400.png)

1. På den **Nexonia Configuration** klickar du på **konfigurera Nexonia** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Nexonia konfiguration](./media/nexonia-tutorial/tutorial_nexonia_configure.png) 

1. Att konfigurera enkel inloggning på **Nexonia** sida, som du behöver skicka de hämtade **certifikat (Base64), URL: en för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** och **SAML entitets-ID**  till [Nexonia supportteamet](https://nexonia.zendesk.com/hc/requests/new). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/nexonia-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/nexonia-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/nexonia-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/nexonia-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-a-nexonia-test-user"></a>Skapa en Nexonia testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Nexonia. Arbeta med [Nexonia supportteamet](https://nexonia.zendesk.com/hc/requests/new) att lägga till användare i Nexonia-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Nexonia.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Nexonia, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Nexonia**.

    ![Länken Nexonia i listan med program](./media/nexonia-tutorial/tutorial_nexonia_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Nexonia i åtkomstpanelen du bör få automatiskt loggat in på ditt Nexonia program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/nexonia-tutorial/tutorial_general_01.png
[2]: ./media/nexonia-tutorial/tutorial_general_02.png
[3]: ./media/nexonia-tutorial/tutorial_general_03.png
[4]: ./media/nexonia-tutorial/tutorial_general_04.png

[100]: ./media/nexonia-tutorial/tutorial_general_100.png

[200]: ./media/nexonia-tutorial/tutorial_general_200.png
[201]: ./media/nexonia-tutorial/tutorial_general_201.png
[202]: ./media/nexonia-tutorial/tutorial_general_202.png
[203]: ./media/nexonia-tutorial/tutorial_general_203.png

