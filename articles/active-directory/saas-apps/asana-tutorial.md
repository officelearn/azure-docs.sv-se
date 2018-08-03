---
title: 'Självstudier: Azure Active Directory-integration med Asana | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Asana.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.openlocfilehash: f5ea7a330891d4befeb6388bbe7f37b2a4aa848f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438216"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Självstudier: Azure Active Directory-integration med Asana

I den här självstudien får du lära dig hur du integrerar Asana med Azure Active Directory (AD Azure).

Integrera Asana med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Asana
- Du kan aktivera användarna att automatiskt få loggat in på Asana (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Asana, behöver du följande objekt:

- En Azure AD-prenumeration
- En Asana enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Asana från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-asana-from-the-gallery"></a>Att lägga till Asana från galleriet
För att konfigurera integrering av Asana i Azure AD, som du behöver lägga till Asana från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Asana från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Asana**väljer **Asana** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/asana-tutorial/tutorial_asana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Asana baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Asana är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Asana upprättas.

I Asana, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Asana, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare i Asana](#create-an-asana-test-user)**  – du har en motsvarighet för Britta Simon i Asana som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program i Asana.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Asana:**

1. I Azure-portalen på den **Asana** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/asana-tutorial/tutorial_asana_samlbase.png)

1. På den **Asana domän och URL: er** avsnittet, utför följande steg:

    ![Asana domän och URL: er med enkel inloggning för information](./media/asana-tutorial/tutorial_asana_url.png)

    a. I den **inloggnings-URL** textrutan typen URL: `https://app.asana.com/`

    b. I den **identifierare** textrutan TYPVÄRDE: `https://app.asana.com/`

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/asana-tutorial/tutorial_asana_certificate.png)

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/asana-tutorial/tutorial_general_400.png)

1. På den **Asana Configuration** klickar du på **konfigurera Asana** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Asana-konfiguration](./media/asana-tutorial/tutorial_asana_configure.png)

1. I ett annat webbläsarfönster inloggning i ditt program i Asana. Om du vill konfigurera enkel inloggning i Asana, att komma åt arbetsyteinställningarna för genom att klicka på arbetsytans namn i övre högra hörnet på skärmen. Klicka på  **\<ditt Arbetsytenamn\> inställningar**.

    ![Inställningar för enkel inloggning i Asana](./media/asana-tutorial/tutorial_asana_09.png)

1. På den **organisationsinställningar** fönstret klickar du på **Administration**. Klicka sedan på **medlemmar måste logga in via SAML** att aktivera SSO-konfiguration. Utför följande steg:

    ![Konfigurera inställningar för enkel inloggning för organisationen](./media/asana-tutorial/tutorial_asana_10.png)  

     a. I den **inloggning sid-URL** textrutan klistra in den **SAML enkel inloggning för tjänst-URL**.

     b. Högerklicka på det certifikat som hämtats från Azure-portalen och öppna sedan certifikatfilen med anteckningar eller din önskade textredigerare. Kopiera innehållet mellan start- och slut-certifikat och klistra in den i den **X.509-certifikat** textrutan.

1. Klicka på **Spara**. Gå till [Asana-guiden för att konfigurera enkel inloggning](https://asana.com/guide/help/premium/authentication#gl-saml) om du behöver ytterligare hjälp.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](./media/asana-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/asana-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.

    ![Skapa en Azure AD-användare för testning](./media/asana-tutorial/create_aaduser_03.png)

1. På den **användaren** dialogrutan utför följande steg:

    ![Knappen Lägg till](./media/asana-tutorial/create_aaduser_04.png)

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="create-an-asana-test-user"></a>Skapa en Asana testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Asana. Asana stöder automatisk användaretablering, vilket är som standard aktiverat. Du hittar mer information om [här](asana-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du vill skapa användare manuellt gör du följande:**

I det här avsnittet skapar du en användare som kallas Britta Simon i Asana.

1. På **Asana**går du till den **team** avsnitt på den vänstra panelen. Klicka på plustecknet.

    ![Skapa en Azure AD-användare för testning](./media/asana-tutorial/tutorial_asana_12.png)

1. Skriv e-postmeddelandet britta.simon@contoso.com i textrutan och välj sedan **bjuda in**.

1. Klicka på **skicka inbjudan**. Den nya användaren får ett e-postmeddelande till sin e-postkonto. Hon behöver du skapa och verifiera kontot.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Asana.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon Asana, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

1. I listan med program väljer **Asana**.

    ![Länken Asana i listan med program](./media/asana-tutorial/tutorial_asana_app.png)

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD enkel inloggning.

Gå till inloggningssidan i Asana. Infoga e-postadress i textrutan e-postadress britta.simon@contoso.com. Lämna textrutan lösenord i början och klicka sedan på **logga In**. Du omdirigeras till inloggningssidan för Azure AD. Slutför din Azure AD-autentiseringsuppgifter. Nu kan är du inloggad Asana.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](asana-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/asana-tutorial/tutorial_general_01.png
[2]: ./media/asana-tutorial/tutorial_general_02.png
[3]: ./media/asana-tutorial/tutorial_general_03.png
[4]: ./media/asana-tutorial/tutorial_general_04.png

[100]: ./media/asana-tutorial/tutorial_general_100.png

[200]: ./media/asana-tutorial/tutorial_general_200.png
[201]: ./media/asana-tutorial/tutorial_general_201.png
[202]: ./media/asana-tutorial/tutorial_general_202.png
[203]: ./media/asana-tutorial/tutorial_general_203.png
[10]: ./media/asana-tutorial/tutorial_general_060.png
[11]: ./media/asana-tutorial/tutorial_general_070.png