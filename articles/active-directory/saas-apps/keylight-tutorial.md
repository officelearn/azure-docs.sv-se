---
title: 'Självstudier: Azure Active Directory-integrering med LockPath Keylight | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LockPath Keylight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9757588b7adb4032600113d2ac948097e8df6c2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717455"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Självstudier: Azure Active Directory-integrering med LockPath Keylight

I den här självstudien får du lära dig hur du integrerar LockPath Keylight med Azure Active Directory (AD Azure).
Integrera LockPath Keylight med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till LockPath Keylight.
* Du kan aktivera användarna att vara automatiskt inloggad till LockPath Keylight (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med LockPath Keylight, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* LockPath Keylight enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för LockPath Keylight **SP** -initierad SSO
* Har stöd för LockPath Keylight **Just In Time** etableringen av användare

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Att lägga till LockPath Keylight från galleriet

För att konfigurera integrering av LockPath Keylight i Azure AD, som du behöver lägga till LockPath Keylight från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till LockPath Keylight från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **LockPath Keylight**väljer **LockPath Keylight** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![LockPath Keylight i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med LockPath Keylight baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i LockPath Keylight upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med LockPath Keylight, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för LockPath-Keylight](#configure-lockpath-keylight-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare LockPath Keylight](#create-lockpath-keylight-test-user)**  – du har en motsvarighet för Britta Simon i LockPath Keylight som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med LockPath Keylight:

1. I den [Azure-portalen](https://portal.azure.com/)på den **LockPath Keylight** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![LockPath Keylight domän och URL: er med enkel inloggning för information](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<company name>.keylightgrc.com/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<company name>.keylightgrc.com`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<company name>.keylightgrc.com/Login.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL, identifierare och svars-URL. Kontakta [LockPath Keylight klienten supportteamet](https://www.lockpath.com/contact/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. På den **konfigurera LockPath Keylight** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-lockpath-keylight-single-sign-on"></a>Konfigurera LockPath Keylight enkel inloggning

1. Om du vill aktivera enkel inloggning i LockPath Keylight, utför du följande steg:

    a. Inloggning till ditt konto LockPath Keylight som administratör.

    b. Klicka på menyn längst upp **Person**, och välj **Keylight installationsprogrammet**.

    ![Konfigurera enkel inloggning](./media/keylight-tutorial/401.png)

    c. I trädvyn till vänster, klickar du på **SAML**.

    ![Konfigurera enkel inloggning](./media/keylight-tutorial/402.png)

    d. På den **SAML-inställningar** dialogrutan klickar du på **redigera**.

    ![Konfigurera enkel inloggning](./media/keylight-tutorial/404.png)

1. På den **redigera inställningar för SAML** dialogrutan utför följande steg:

    ![Konfigurera enkel inloggning](./media/keylight-tutorial/405.png)

    a. Ange **SAML-autentisering** till **Active**.

    b. I den **inloggnings-URL för identitetsprovider** textrutan klistra in den **inloggnings-URL** värde som du har kopierat från Azure-portalen.

    c. I den **utloggnings-URL för identitetsprovider** textrutan klistra in den **URL för utloggning** värde som du har kopierat från Azure-portalen.

    d. Klicka på **Välj fil** välja hämtade LockPath Keylight certifikatet och klicka sedan på **öppna** att ladda upp.

    e. Ange **SAML användar-Id plats** till **NameIdentifier-elementet i instruktionen ämne**.

    f. Ange den **Keylight tjänstleverantör** med hjälp av följande mönster: `https://<CompanyName>.keylightgrc.com`.

    g. Ange **automatiskt etablera användare** till **Active**.

    h. Ange **automatiskt etablera kontotyp** till **användarens fullständiga**.

    i. Ange **automatiskt etablera säkerhetsroll**väljer **standardanvändare med SAML**.

    j. Ange **automatiskt etablera security config**väljer **Standard Användarkonfiguration**.

    k. I den **e-attributet** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    l. I den **förnamn attributet** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    m. I den **senaste namnattributet** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    n. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LockPath Keylight.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **LockPath Keylight**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **LockPath Keylight**.

    ![Länken LockPath Keylight i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-lockpath-keylight-test-user"></a>Skapa LockPath Keylight testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i LockPath Keylight. LockPath Keylight stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i LockPath Keylight, skapas en ny efter autentisering. Om du vill skapa en användare manuellt kan du behöva kontakta den [LockPath Keylight klienten supportteamet](https://www.lockpath.com/contact/).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen LockPath Keylight i åtkomstpanelen, bör det vara loggas in automatiskt till den LockPath Keylight som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)