---
title: 'Självstudiekurs: Azure Active Directory-integrering med ScreenSteps | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ScreenSteps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 864a9243a9f737506fd4d8cbc3940d7a86711f20
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091652"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Självstudiekurs: Azure Active Directory-integrering med ScreenSteps

I den här självstudien får du lära dig hur du integrerar ScreenSteps med Azure Active Directory (Azure AD).
Genom att integrera ScreenSteps med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till ScreenSteps.
* Du kan aktivera dina användare så att de automatiskt loggas in på ScreenSteps (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med ScreenSteps behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* ScreenSteps enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* ScreenSteps stöder **SP** initierade SSO

## <a name="adding-screensteps-from-the-gallery"></a>Lägga till ScreenSteps från galleriet

Om du vill konfigurera integreringen av ScreenSteps i Azure AD måste du lägga till ScreenSteps från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till ScreenSteps från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **ScreenSteps**i sökrutan och välj **ScreenSteps** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![ScreenSteps i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med ScreenSteps baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i ScreenSteps upprättas.

Om du vill konfigurera och testa en enda Azure AD-inloggning med ScreenSteps måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera ScreenSteps Single Sign-On](#configure-screensteps-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa ScreenSteps-testanvändare](#create-screensteps-test-user)** – om du vill ha en motsvarighet till Britta Simon i ScreenSteps som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med ScreenSteps:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **ScreenSteps-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![ScreenSteps-domän och webbadresser med enkel inloggning](common/sp-signonurl.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Det här värdet är inte verkligt. Uppdatera det här värdet med den faktiska inloggnings-URL:en, som förklaras senare i den här självstudien.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera ScreenSteps.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-screensteps-single-sign-on"></a>Konfigurera ScreenSteps enkel inloggning

1. I ett annat webbläsarfönster loggar du in på webbplatsen ScreenSteps som administratör.

1. Klicka på **Kontoinställningar**.

    ![Kontohantering](./media/screensteps-tutorial/ic778523.png "Kontohantering")

1. Klicka **på Enkel inloggning**.

    ![Fjärrautentisering](./media/screensteps-tutorial/ic778524.png "Fjärrautentisering")

1. Klicka på **Skapa enstaka inloggningsslutpunkt**.

    ![Fjärrautentisering](./media/screensteps-tutorial/ic778525.png "Fjärrautentisering")

1. Gör följande i avsnittet **Skapa enstaka inloggningsslutpunkt:**

    ![Skapa en slutpunkt för autentisering](./media/screensteps-tutorial/ic778526.png "Skapa en slutpunkt för autentisering")

    a. Skriv en rubrik i textrutan **Rubrik.**

    b. Välj **SAML**i listan **Läge** .

    c. Klicka på **Skapa**.

1. **Redigera** den nya slutpunkten.

    ![Redigera slutpunkt](./media/screensteps-tutorial/ic778528.png "Redigera slutpunkt")

1. Gör följande i avsnittet **Redigera enstaka inloggningsslutpunkt:**

    ![Slutpunkt för fjärrautentisering](./media/screensteps-tutorial/ic778527.png "Slutpunkt för fjärrautentisering")

    a. Klicka på **Ladda upp en ny SAML-certifikatfil**och ladda sedan upp certifikatet som du har hämtat från Azure-portalen.

    b. Klistra in **url-värde** för inloggning, som du har kopierat från Azure-portalen till textrutan **för URL för fjärr inloggning.**

    c. Klistra in **URL-värdet för utloggning,** som du har kopierat från Azure-portalen till textrutan **Logga ut URL.**

    d. Välj en **grupp** som användarna ska tilldelas när de etableras.

    e. Klicka på **Uppdatera**.

    f. Kopiera **SAML Consumer-URL:en** till Urklipp och klistra in i textrutan **Sign-on URL** i avsnittet Grundläggande **SAML-konfiguration** i Azure-portalen.

    g. Återgå till **endpointen Redigera enkel inloggning**.

    h. Klicka på knappen **Skapa standard för konto om** du vill använda den här slutpunkten för alla användare som loggar in på ScreenSteps. Alternativt kan du klicka på knappen **Lägg till på plats** om du vill använda den här slutpunkten för specifika webbplatser i **ScreenSteps**.

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

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ScreenSteps.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **ScreenSteps**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **ScreenSteps**i programlistan .

    ![Länken ScreenSteps i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-screensteps-test-user"></a>Skapa ScreenSteps-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i ScreenSteps. Arbeta med [ScreenSteps Client supportteam](https://www.screensteps.com/contact) för att lägga till användarna i ScreenSteps-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen ScreenSteps på åtkomstpanelen ska du automatiskt loggas in på de Skärmsteg som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)