---
title: 'Självstudier: Azure Active Directory-integrering med N2F - utgiftsrapporter | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och N2F - utgiftsrapporter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 7380cf6727817d99edbedf5552c8bea42f177074
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59261691"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Självstudier: Azure Active Directory-integrering med N2F - utgiftsrapporter

I den här självstudien får du lära dig hur du integrerar N2F - utgiftsrapporter med Azure Active Directory (AD Azure).
Integrerande N2F - utgiftsrapporter med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till N2F - utgiftsrapporter.
* Du kan aktivera användarna att vara automatiskt inloggad till N2F - utgiftsrapporter (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med N2F - utgiftsrapporter, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* N2F - utgiftsrapporter enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* N2F - utgiftsrapporter stöder **SP** och **IDP** -initierad SSO

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Att lägga till N2F - utgiftsrapporter från galleriet

Om du vill konfigurera integreringen av N2F - utgiftsrapporter i Azure AD kan du behöva lägga till N2F - utgiftsrapporter från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till N2F - utgiftsrapporter från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **N2F - utgiftsrapporter**väljer **N2F - utgiftsrapporter** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![N2F - utgiftsrapporter i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med N2F - utgiftsrapporter baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning till arbete, en länk förhållandet mellan en Azure AD-användare och relaterade användaren i N2F - utgiftsrapporter måste upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med N2F - utgiftsrapporter, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera N2F – utgiftsrapporter enkel inloggning](#configure-n2f---expense-reports-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa N2F - utgiftsrapporter testanvändare](#create-n2f---expense-reports-test-user)**  - du har en motsvarighet för Britta Simon i N2F - utgiftsrapporter som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Om du vill konfigurera Azure AD utför enkel inloggning med N2F - utgiftsrapporter, du följande steg:

1. I den [Azure-portalen](https://portal.azure.com/)på den **N2F - utgiftsrapporter** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På den **SAML grundkonfiguration** om du vill konfigurera programmet i **IDP** initierad läge, användaren behöver inte utföra några steg som appen är redan förintegrerade med Azure.

    ![N2F - utgiftsrapporter domän och URL: er med enkel inloggning för information](common/preintegrated.png)

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![N2F - utgiftsrapporter domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I rutan **Inloggnings-URL** anger du en URL: `https://www.n2f.com/app/`

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för hämtning av certifikat](common/copy-metadataurl.png)

7. På den **konfigurera myPolicies** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-n2f---expense-reports-single-sign-on"></a>Konfigurera N2F - utgiftsrapporter enkel inloggning

1. I ett annat webbläsarfönster, loggar du in din N2F - telekommunikation rapporter företagets plats som administratör.

2. Klicka på **inställningar** och välj sedan **avancerade inställningar** i listrutan.

    ![N2F - utgiftsrapporter konfiguration](./media/n2f-expensereports-tutorial/configure1.png)

3. Välj **kontoinställningar** fliken.

    ![N2F - utgiftsrapporter konfiguration](./media/n2f-expensereports-tutorial/configure2.png)

4. Välj **autentisering** och välj sedan **+ Lägg till en autentiseringsmetod** fliken.

    ![N2F - utgiftsrapporter konfiguration](./media/n2f-expensereports-tutorial/configure3.png)

5. Välj **SAML Microsoft Office 365** som autentiseringsmetod.

    ![N2F - utgiftsrapporter konfiguration](./media/n2f-expensereports-tutorial/configure4.png)

6. På den **autentiseringsmetod** avsnittet, utför följande steg:

    ![N2F - utgiftsrapporter konfiguration](./media/n2f-expensereports-tutorial/configure5.png)

    a. I den **entitets-ID** textrutan klistra in den **Azure AD-identifierare** värde, som du har kopierat från Azure-portalen.

    b. I den **Metadata_url** textrutan klistra in den **Appfederationsmetadata** värde, som du har kopierat från Azure-portalen.

    c. Klicka på **Spara**.

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

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till N2F – utgiftsrapporter.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **N2F - utgiftsrapporter**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **N2F - utgiftsrapporter**.

    ![N2F - utgiftsrapporter länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-n2f---expense-reports-test-user"></a>Skapa N2F - utgiftsrapporter testanvändare

Om du vill aktivera Azure AD-användare att logga in på N2F - utgiftsrapporter, måste de etableras i N2F - utgiftsrapporter. När det gäller N2F - utgiftsrapporter, är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din N2F - telekommunikation rapporter företagets plats som administratör.

2. Klicka på **inställningar** och välj sedan **avancerade inställningar** i listrutan.

    ![N2F - telekommunikation Lägg till användare](./media/n2f-expensereports-tutorial/configure1.png)

3. Välj **användare** fliken från vänstra navigeringspanelen.

    ![N2F - utgiftsrapporter konfiguration](./media/n2f-expensereports-tutorial/user1.png)

4. Välj **+ ny användare** fliken.

    ![N2F - utgiftsrapporter konfiguration](./media/n2f-expensereports-tutorial/user2.png)

5. På den **användaren** avsnittet, utför följande steg:

    ![N2F - utgiftsrapporter konfiguration](./media/n2f-expensereports-tutorial/user3.png)

    a. I den **e-postadress** textrutan anger du e-postadressen för användaren som **brittasimon\@contoso.com**.

    b. I textrutan **Förnamn** skriver du förnamnet på användaren: **Britta**.

    c. I textrutan **Namn** anger du namnet på användaren: **BrittaSimon**.

    d. Välj **närmsta chef (N + 1)-rollen**, och **Division** enligt krav för din organisation.

    e. Klicka på **verifiera och skicka inbjudan**.

    > [!NOTE]
    > Om du får problem när du lägger till användaren, kontakta [N2F - utgiftsrapporter supportteam](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på N2F - utgiftsrapporter panelen i åtkomstpanelen, bör det vara loggas in automatiskt till N2F - utgiftsrapporter som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

