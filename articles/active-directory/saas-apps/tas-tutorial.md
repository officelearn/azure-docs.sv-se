---
title: 'Självstudiekurs: Azure Active Directory-integrering med TAS | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TAS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 129b6e69-e3b4-41d7-9ab5-a2ddd0068f76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: efd64830c0d9afa83838adef96cf1c103b4485e8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943281"
---
# <a name="tutorial-azure-active-directory-integration-with-tas"></a>Självstudiekurs: Azure Active Directory-integrering med TAS

I den här självstudien får du lära dig hur du integrerar TAS med Azure Active Directory (Azure AD).
Genom att integrera TAS med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till TAS.
* Du kan aktivera dina användare så att de automatiskt loggas in på TAS (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med TAS behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* TAS enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* TAS stöder **SP och IDP** initierade SSO

## <a name="adding-tas-from-the-gallery"></a>Lägga TAS från galleriet

Om du vill konfigurera integreringen av TAS i Azure AD måste du lägga till TAS från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till TAS från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **TAS**i sökrutan och välj **TAS** från resultatpanelen och klicka sedan på **Lägg** till knappen för att lägga till programmet.

     ![TAS i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med TAS baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i TAS upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med TAS måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera TAS Single Sign-On](#configure-tas-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa TAS-testanvändare](#create-tas-test-user)** - om du vill ha en motsvarighet till Britta Simon i TAS som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med TAS:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **TAS-programintegration**. **TAS**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![TAS-domän och webbadresser med enkel inloggning](common/idp-intiated.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://taseu.combtas.com/<DOMAIN>`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://taseu.combtas.com/<ENVIRONMENTNAME>/AssertionService.aspx`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![TAS-domän och webbadresser med enkel inloggning](common/metadata-upload-additional-signon.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://taseu.combtas.com/<DOMAIN>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du kommer att uppdatera dessa med den faktiska identifieraren, svars-URL:en och inloggnings-URL:en som förklaras senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera TAS.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-tas-single-sign-on"></a>Konfigurera TAS Enkel inloggning

1. I ett annat webbläsarfönster loggar du in på TAS som administratör.

2. På vänster sida av menyn klickar du på Inställningar och **navigerar** till **Administratör och** klickar sedan på Hantera enkel **inloggning på**.

    ![TAS-konfiguration](./media/tas-tutorial/configure01.png)

3. Gör följande på sidan **Hantera enkel inloggning:**

    ![TAS-konfiguration](./media/tas-tutorial/configure02.png)

    a. Skriv ditt miljönamn i textrutan **Namn.**
    
    b. Välj **SAML2** som **autentiseringstyp**.

    c. Klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen i textrutan **Ange URL.**

    d. Öppna det bas-64-kodade certifikat som du hämtade från Azure-portalen i Anteckningar, kopiera dess innehåll och klistra sedan in det i rutan **Ange certifiering.**

    e. Skriv IP-adressen i textrutan **Ange ny IP.**

    >[!NOTE]
    > Kontakta [TAS supportteam](mailto:support@combtas.com) för att få IP-adressen.

    f. Kopiera **url:en enkel inloggning** och klistra in den i **identifieraren (entitets-ID)** och **Logga in i URL-textrutan för** grundläggande **SAML-konfiguration** i Azure-portalen. Observera att webbadressen är skiftlägeskänslig och måste sluta med ett snedstreck (/).

    g. Kopiera **url:en för kontrolltjänsten** på inställningssidan och klistra in den i textrutan **Svars-URL** för **grundläggande SAML-konfiguration** i Azure-portalen.

    h. Klicka på **Infoga SSO-rad**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TAS.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **TAS**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **TAS**i programlistan .

    ![TAS-länken i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-tas-test-user"></a>Skapa TAS-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i TAS. Arbeta med [TAS supportteam](mailto:support@combtas.com) för att lägga till användarna i TAS-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på TAS-panelen på åtkomstpanelen ska du automatiskt loggas in på det TAS som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

