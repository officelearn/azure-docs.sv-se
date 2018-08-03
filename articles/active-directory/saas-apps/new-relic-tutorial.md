---
title: 'Självstudier: Azure Active Directory-integrering med New Relic | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och New Relic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: 80bd77504f1b2ab5b6e5c781eadb7c2cd4c99220
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440753"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Självstudier: Azure Active Directory-integrering med New Relic

I den här självstudien får du lära dig hur du integrerar New Relic med Azure Active Directory (AD Azure).

Integrera New Relic med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till New Relic.
- Du kan aktivera användarna att automatiskt få loggat in på New Relic (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med New Relic, behöver du följande objekt:

- En Azure AD-prenumeration
- Ett New Relic enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägger till New Relic från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-new-relic-from-the-gallery"></a>Lägger till New Relic från galleriet
Om du vill konfigurera integreringen av New Relic till Azure AD, som du behöver lägga till New Relic från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till nya Relic från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **New Relic**väljer **New Relic** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![New Relic i resultatlistan](./media/new-relic-tutorial/tutorial_new-relic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med New Relic baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i New Relic är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i New Relic upprättas.

I New Relic tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med New Relic, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare i New Relic](#create-a-new-relic-test-user)**  – du har en motsvarighet för Britta Simon i New Relic som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt New Relic-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med New Relic:**

1. I Azure-portalen på den **New Relic** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/new-relic-tutorial/tutorial_new-relic_samlbase.png)

1. På den **nya Relic-domän och URL: er** avsnittet, utför följande steg:

    ![Nya Relic domän och URL: er med enkel inloggning för information](./media/new-relic-tutorial/tutorial_new-relic_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` – se till att ersätta ditt eget New Relic-konto-ID.

    b. I den **identifierare** textrutan angett värde: `rpm.newrelic.com`

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/new-relic-tutorial/tutorial_new-relic_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/new-relic-tutorial/tutorial_general_400.png)

1. På den **nya Relic konfigurationen** klickar du på **konfigurera New Relic** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Nya Relic-konfigurationen](./media/new-relic-tutorial/tutorial_new-relic_configure.png) 

1. I ett annat webbläsarfönster, loggar du in på ditt **New Relic** företagets plats som administratör.

1. Klicka på menyn längst upp **kontoinställningar**.
   
    ![Kontoinställningar](./media/new-relic-tutorial/ic797036.png "kontoinställningar")

1. Klicka på den **säkerhet och autentisering** fliken och klicka sedan på den **enkel inloggning** fliken.
   
    ![Enkel inloggning](./media/new-relic-tutorial/ic797037.png "enkel inloggning")

1. Utför följande steg på sidan SAML dialogrutan:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
   a. Klicka på **Välj fil** att ladda upp din hämtade Azure Active Directory-certifikatet.

   b. I den **fjärrinloggning URL** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.
   
   c. I den **utloggning landing URL** textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.

   d. Klicka på **spara mina ändringar**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/new-relic-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/new-relic-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/new-relic-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/new-relic-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-new-relic-test-user"></a>Skapa en testanvändare i New Relic

För att aktivera Azure Active Directory-användare att logga in på New Relic, måste de etableras i New Relic. När det gäller New Relic är etablering en manuell aktivitet.

**Om du vill konfigurera ett användarkonto till New Relic, utför du följande steg:**

1. Logga in på din **New Relic** företagets plats som administratör.

1. Klicka på menyn längst upp **kontoinställningar**.
   
    ![Kontoinställningar](./media/new-relic-tutorial/ic797040.png "kontoinställningar")

1. I den **konto** fönstret till vänster, klickar du på **sammanfattning**, och klicka sedan på **Lägg till användare**.
   
    ![Kontoinställningar](./media/new-relic-tutorial/ic797041.png "kontoinställningar")

1. På den **aktiva användare** dialogrutan utför följande steg:
   
    ![Aktiva användare](./media/new-relic-tutorial/ic797042.png "aktiva användare")
   
    a. I den **e-post** textrutan skriver du en giltig Azure Active Directory-användare som du vill etablera e-postadress.

    b. Som **rollen** Välj **användaren**.

    c. Klicka på **Lägg till användaren**.

>[!NOTE]
>Du kan använda New Relic användaren-konto skapas verktyg från någon annan eller API: er fås från New Relic för att etablera AAD-användarkonton.
> 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till New Relic.

![Tilldela rollen][200] 

**Om du vill tilldela New Relic Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **New Relic**.

    ![New Relic-länk i listan med program](./media/new-relic-tutorial/tutorial_new-relic_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på New Relic-panelen i åtkomstpanelen du bör få automatiskt loggat in på ditt New Relic-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/new-relic-tutorial/tutorial_general_01.png
[2]: ./media/new-relic-tutorial/tutorial_general_02.png
[3]: ./media/new-relic-tutorial/tutorial_general_03.png
[4]: ./media/new-relic-tutorial/tutorial_general_04.png

[100]: ./media/new-relic-tutorial/tutorial_general_100.png

[200]: ./media/new-relic-tutorial/tutorial_general_200.png
[201]: ./media/new-relic-tutorial/tutorial_general_201.png
[202]: ./media/new-relic-tutorial/tutorial_general_202.png
[203]: ./media/new-relic-tutorial/tutorial_general_203.png

