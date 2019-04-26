---
title: 'Självstudier: Azure Active Directory-integrering med LogicMonitor | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LogicMonitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a953ae52f37b8d48f9e2e0566325691dbb08307
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60257785"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Självstudier: Azure Active Directory-integrering med LogicMonitor

I den här självstudien får du lära dig hur du integrerar LogicMonitor med Azure Active Directory (AD Azure).
Integrera LogicMonitor med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till LogicMonitor.
* Du kan aktivera användarna att vara automatiskt inloggad till LogicMonitor (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med LogicMonitor, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* LogicMonitor enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för LogicMonitor **SP** -initierad SSO

## <a name="adding-logicmonitor-from-the-gallery"></a>Att lägga till LogicMonitor från galleriet

För att konfigurera integrering av LogicMonitor i Azure AD, som du behöver lägga till LogicMonitor från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till LogicMonitor från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **LogicMonitor**väljer **LogicMonitor** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![LogicMonitor i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med LogicMonitor baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i LogicMonitor upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med LogicMonitor, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera LogicMonitor Single Sign-On](#configure-logicmonitor-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare LogicMonitor](#create-logicmonitor-test-user)**  – du har en motsvarighet för Britta Simon i LogicMonitor som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med LogicMonitor:

1. I den [Azure-portalen](https://portal.azure.com/)på den **LogicMonitor** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![LogicMonitor domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.logicmonitor.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.logicmonitor.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [LogicMonitor klienten supportteamet](https://www.logicmonitor.com/contact/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **konfigurera LogicMonitor** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-logicmonitor-single-sign-on"></a>Konfigurera LogicMonitor Single Sign-On

1. Logga in på din **LogicMonitor** företagets plats som administratör.

2. På menyn längst upp klickar du på **Inställningar**.

    ![Inställningar](./media/logicmonitor-tutorial/ic790052.png "Inställningar")

3. I funka navigeringen till vänster klickar du på **enkel inloggning**

    ![Enkel inloggning](./media/logicmonitor-tutorial/ic790053.png "Enkel inloggning")

4. I den **inställningar för enkel inloggning (SSO)** avsnittet, utför följande steg:

    ![Inställningar för enkel inloggning](./media/logicmonitor-tutorial/ic790054.png "Inställningar för enkel inloggning")

    a. Välj **aktivera enkel inloggning**.

    b. Som **standard rolltilldelning**väljer **readonly**.

    c. Öppna den hämta metadatafilen i anteckningar och klistra in innehållet i filen till den **identitet providern Metadata** textrutan.

    d. Klicka på **Spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LogicMonitor.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **LogicMonitor**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **LogicMonitor**.

    ![Länken LogicMonitor i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-logicmonitor-test-user"></a>Skapa LogicMonitor testanvändare

För Azure AD-användare för att kunna logga in, måste de etableras till programmet LogicMonitor med deras Azure Active Directory-användarnamn.

**Utför följande steg för att konfigurera användarförsörjning:**

1. Logga in på webbplatsen LogicMonitor företag som administratör.

2. Klicka på menyn längst upp **inställningar**, och klicka sedan på **roller och användare**.

    ![Roller och användare](./media/logicmonitor-tutorial/ic790056.png "roller och användare")

3. Klicka på **Lägg till**.

4. I den **Lägg till ett konto** avsnittet, utför följande steg:

    ![Lägg till ett konto](./media/logicmonitor-tutorial/ic790057.png "Lägg till ett konto")

    a. Skriv den **användarnamn**, **e-post**, **lösenord**, och **ange lösenordet** värden för den Azure Active Directory-användare som du vill etablera i den relaterade textrutor.

    b. Välj **roller**, **Visa behörigheter**, och **Status**.

    c. Klicka på **Skicka**.

> [!NOTE]
> Du kan använda alla andra LogicMonitor användare konto verktyg för att skapa eller API: er som tillhandahålls av LogicMonitor för att etablera Azure Active Directory användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen LogicMonitor i åtkomstpanelen, bör det vara loggas in automatiskt till LogicMonitor som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

