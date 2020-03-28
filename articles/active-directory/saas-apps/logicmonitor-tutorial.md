---
title: 'Självstudiekurs: Azure Active Directory-integrering med LogicMonitor | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LogicMonitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 463a8981689614d96100e03965117c9344aa5d50
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159516"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Självstudiekurs: Azure Active Directory-integrering med LogicMonitor

I den här självstudien får du lära dig hur du integrerar LogicMonitor med Azure Active Directory (Azure AD).
Genom att integrera LogicMonitor med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till LogicMonitor.
* Du kan aktivera dina användare så att de automatiskt loggas in på LogicMonitor (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med LogicMonitor behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* LogicMonitor enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* LogicMonitor stöder **SP** initierade SSO

## <a name="adding-logicmonitor-from-the-gallery"></a>Lägga till LogicMonitor från galleriet

Om du vill konfigurera integreringen av LogicMonitor i Azure AD måste du lägga till LogicMonitor från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till LogicMonitor i galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **LogicMonitor**i sökrutan och välj **LogicMonitor** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![LogicMonitor i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med LogicMonitor baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i LogicMonitor upprättas.

Om du vill konfigurera och testa azure AD-enkel inloggning med LogicMonitor måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera LogicMonitor Enkel inloggning](#configure-logicmonitor-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa LogicMonitor-testanvändare](#create-logicmonitor-test-user)** – om du vill ha en motsvarighet till Britta Simon i LogicMonitor som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med LogicMonitor:

1. Välj Enkel inloggning på sidan LogicMonitor-programintegrering på [Azure-portalen](https://portal.azure.com/). **LogicMonitor** **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![LogicMonitor Domän och webbadresser enkel inloggningsinformation](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.logicmonitor.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.logicmonitor.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [LogicMonitor Client supportteam](https://www.logicmonitor.com/contact/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera LogicMonitor.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-logicmonitor-single-sign-on"></a>Konfigurera LogicMonitor enkel inloggning

1. Logga in på **LogicMonitor-företagets** webbplats som administratör.

2. På menyn längst upp klickar du på **Inställningar**.

    ![Inställningar](./media/logicmonitor-tutorial/ic790052.png "Inställningar")

3. I navigeringsfladdergen på vänster sida klickar du på **Enkel inloggning**

    ![Enkel inloggning](./media/logicmonitor-tutorial/ic790053.png "för Aha!")

4. I avsnittet **Inställningar för enkel inloggning (SSO)** utför du följande steg:

    ![Inställningar för enkel inloggning](./media/logicmonitor-tutorial/ic790054.png "Inställningar för enkel inloggning")

    a. Välj **Aktivera enkel inloggning**.

    b. Som **standardrolltilldelning**väljer du **readonly**.

    c. Öppna den nedladdade metadatafilen i anteckningar och klistra sedan in innehållet i filen i textrutan **Identitetsprovidermetadata.**

    d. Klicka på **Spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** skriver **du\@brittasimon yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till LogicMonitor.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **LogicMonitor**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **LogicMonitor**i programlistan .

    ![Länken LogicMonitor i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-logicmonitor-test-user"></a>Skapa LogicMonitor-testanvändare

För att Azure AD-användare ska kunna logga in måste de etableras i LogicMonitor-programmet med sina Azure Active Directory-användarnamn.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på LogicMonitor-företagets webbplats som administratör.

2. Klicka på **Inställningar**på menyn högst upp och klicka sedan på **Roller och Användare**.

    ![Roller och användare](./media/logicmonitor-tutorial/ic790056.png "Roller och användare")

3. Klicka på **Lägg till**.

4. Gör följande i avsnittet **Lägg till ett konto:**

    ![Lägga till ett konto](./media/logicmonitor-tutorial/ic790057.png "Lägga till ett konto")

    a. Skriv lösenordvärdena **Användarnamn,** **E-post,** **Lösenord**och **Skriv om lösenord** för den Azure Active Directory-användare som du vill etablera i relaterade textrutor.

    b. Välj **Roller,** **Visa behörigheter**och **status**.

    c. Klicka på **Skicka**.

> [!NOTE]
> Du kan använda andra LogicMonitor-verktyg eller API:er för att skapa Azure Active Directory-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen LogicMonitor på åtkomstpanelen ska du automatiskt loggas in på LogicMonitor som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

