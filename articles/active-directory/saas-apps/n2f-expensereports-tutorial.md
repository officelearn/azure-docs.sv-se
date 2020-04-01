---
title: 'Självstudiekurs: Azure Active Directory-integrering med N2F – Utgiftsrapporter | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och N2F - Utgiftsrapporter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 11f5e2f7763008c3af09c5367d90265af6a9653a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161280"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Självstudiekurs: Azure Active Directory-integrering med N2F - Utgiftsrapporter

I den här självstudien får du lära dig hur du integrerar N2F - Utgiftsrapporter med Azure Active Directory (Azure AD).
Genom att integrera N2F – Utgiftsrapporter med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till N2F - Utgiftsrapporter.
* Du kan aktivera dina användare automatiskt inloggade på N2F - Utgiftsrapporter (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med N2F - Utgiftsrapporter behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* N2F - Utgiftsrapporter enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* N2F - Utgiftsrapporter stöder **SP** och **IDP** initierade SSO

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Lägga till N2F - Utgiftsrapporter från galleriet

Om du vill konfigurera integreringen av N2F - Utgiftsrapporter i Azure AD måste du lägga till N2F - Utgiftsrapporter från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till N2F - Utgiftsrapporter från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **N2F - Utgiftsrapporter**i sökrutan , välj **N2F - Utgiftsrapporter** från resultatpanelen och klicka sedan på **Lägg** till knappen För att lägga till programmet.

     ![N2F - Utgiftsrapporter i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med N2F - Utgiftsrapporter baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i N2F - Utgiftsrapporter upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med N2F - Utgiftsrapporter måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera N2F - Utgiftsrapporter Enkel inloggning](#configure-n2f---expense-reports-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa N2F - Utgiftsrapporter testanvändare](#create-n2f---expense-reports-test-user)** - för att ha en motsvarighet till Britta Simon i N2F - Utgiftsrapporter som är länkade till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med N2F - Utgiftsrapporter:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **N2F - Utgiftsrapporter** för programintegrering . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![N2F - Utgiftsrapporter Domän- och webbadresser med enkel inloggning](common/preintegrated.png)

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![N2F - Utgiftsrapporter Domän- och webbadresser med enkel inloggning](common/metadata-upload-additional-signon.png)

    Skriv en URL i textrutan **Sign-on-URL:**`https://www.n2f.com/app/`

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera myPolicies.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-n2f---expense-reports-single-sign-on"></a>Konfigurera N2F - Utgiftsrapporter Enkel inloggning

1. I ett annat webbläsarfönster loggar du in på företagets webbplats N2F – Utgiftsrapporter som administratör.

2. Klicka på **Inställningar** och välj sedan **Förhandsinställningar** i listrutan.

    ![N2F - Konfiguration av utgiftsrapporter](./media/n2f-expensereports-tutorial/configure1.png)

3. Fliken **Välj kontoinställningar.**

    ![N2F - Konfiguration av utgiftsrapporter](./media/n2f-expensereports-tutorial/configure2.png)

4. Välj **Autentisering** och välj sedan **+ Lägg till en autentiseringsmetodflik.**

    ![N2F - Konfiguration av utgiftsrapporter](./media/n2f-expensereports-tutorial/configure3.png)

5. Välj **SAML Microsoft Office 365** som autentiseringsmetod.

    ![N2F - Konfiguration av utgiftsrapporter](./media/n2f-expensereports-tutorial/configure4.png)

6. Gör följande i avsnittet **Autentiseringsmetod:**

    ![N2F - Konfiguration av utgiftsrapporter](./media/n2f-expensereports-tutorial/configure5.png)

    a. I textrutan **Entitets-ID** klistrar du in azure **AD-identifierare,** som du har kopierat från Azure-portalen.

    b. I textrutan **Metadata-URL** klistrar du in **url-värdet för appfederationens metadata,** som du har kopierat från Azure-portalen.

    c. Klicka på **Spara**.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till N2F - Utgiftsrapporter.

1. I Azure-portalen väljer du **Företagsprogram**, väljer **Alla program**och väljer sedan **N2F - Utgiftsrapporter**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **N2F - Utgiftsrapporter**i programlistan .

    ![Länken N2F - Utgiftsrapporter i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-n2f---expense-reports-test-user"></a>Skapa N2F - Testanvändare för utgiftsrapporter

Om du vill att Azure AD-användare ska kunna logga in på N2F - Utgiftsrapporter måste de etableras i N2F - Utgiftsrapporter. När det gäller N2F - Utgiftsrapporter är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din N2F - Utgiftsrapporter företagets webbplats som administratör.

2. Klicka på **Inställningar** och välj sedan **Förhandsinställningar** i listrutan.

    ![N2F - Kostnad Lägg till användare](./media/n2f-expensereports-tutorial/configure1.png)

3. Välj fliken **Användare** från den vänstra navigeringspanelen.

    ![N2F - Konfiguration av utgiftsrapporter](./media/n2f-expensereports-tutorial/user1.png)

4. Välj **fliken Ny användare.**

    ![N2F - Konfiguration av utgiftsrapporter](./media/n2f-expensereports-tutorial/user2.png)

5. Gör följande i avsnittet **Användare:**

    ![N2F - Konfiguration av utgiftsrapporter](./media/n2f-expensereports-tutorial/user3.png)

    a. I textrutan **E-postadress** anger du e-postadressen till användare som **\@brittasimon contoso.com**.

    b. I textrutan **Förnamn** anger du förnamnet på en användare som **Britta**.

    c. I textrutan **Namn** anger du namnet på användaren: **BrittaSimon**.

    d. Välj **Roll, Direct Manager (N+1)** och **Division** enligt organisationens krav.

    e. Klicka på **Validera och skicka inbjudan**.

    > [!NOTE]
    > Om du står inför några problem när du lägger till användaren, vänligen kontakta [N2F - Utgiftsrapporter supportteam](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen N2F - Utgiftsrapporter på åtkomstpanelen ska du automatiskt loggas in i de N2F - Utgiftsrapporter som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

